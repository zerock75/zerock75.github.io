---
title: 우분투 24.04 nftable 전용 방화벽 설정 가이드
date: 2025-09-26
categories: [Server, Ubuntu]
tag: [linux, nftable, ubuntu]
---

# Ubuntu 24.04 - nftables 전용 방화벽 설정 가이드

## 현재 상태 확인
```bash
# nftables 서비스 상태 확인
systemctl status nftables

# 현재 규칙 확인
sudo nft list ruleset
```


## 2. iptables 흔적 정리
```bash
# iptables 규칙 초기화
sudo iptables -F
sudo iptables -X
sudo ip6tables -F
sudo ip6tables -X

# iptables 관련 패키지 제거 (선택사항)
sudo apt remove --purge iptables-persistent -y
```

## 3. nftables 기본 설정
```pgsql
table → chain → rule
```
> table → 큰 틀 (IPv4/IPv6 공용인지, NAT용인지 등)
> chain → 그 테이블 안에서 패킷이 흐르는 “경로”
> rule → chain 안에서 실제로 검사하고 허용/차단하는 조건

***/etc/nftables.conf***
```bash 
#!/usr/sbin/nft -f
flush ruleset

table inet webserver {

  set allow_ips {
    type ipv4_addr;
    elements = {220.86.239.194, 180.228.236.147}
  }

  chain input {   # chain {규칙이름}
    type filter hook input priority 0;
    policy drop;  # 기본정책(drop, accept)

    iif lo accept # 루프백(localhost) 허용
    ct state established,related accept

    ip saddr @allow_ips tcp dport {22,3306} accept
    tcp dport {80,443} accept
  }

  chain output  { type filter hook output  priority 0; policy accept; } # 서버에서 바깥으로 나가는건 모두 허용
  chain forward { type filter hook forward priority 0; policy drop; } # 서버 경유 불가
  
}
```

```
table inet webserver
/*
table 
<family>
  ip: IPv4 전용
  ip6: IPv6 전용
  inet: IPv4 + IPv6 
  arp: 맥어드레스(원격서버에서는 의미 없음)
  bridge: 가상화나 docker 에서 활용할 수 있다
  netdev: 네트워크 인터페이스 장치(Device) 수준에서 패킷을 제어. DDoS 방어, 초고속 방화벽
</family>
$table-name
*/
```

```
chain input
/*
chain $chain-name
*/

```

```
type filter hook input priority 0;

/*
  type
    - filter: type은 필터다
  hook: 잡는다
    - input: 들어오는 패킷
    - output: 나가는 패킷
    - forward: 다른곳으로 전달되는 패킷
    - prerouting / postrouting: NAT/라우팅 시점
  priority:
    - 0~999: 실행순서
*/
```

```
policy drop;  # 기본정책
```

```
iif lo accept

/*
  iif: input interface (입력 인터페이스)의 줄임말
    - lo: 루프백(localhost)
*/
```

```
ct state established,related accept
/*
  ct: connection tracking(커넥션 추적) 기능을 의미, 리눅스 커널 netfilter 모듈이 패킷의 상태를 추적함
    - state: 커넥션 상태(세션의 단계)를 검사
      - new: 새로운 연결 패킷
      - established: 이미 연결된 상태
      - related: 기존 연결과 관련된 새로운 연결
      - invalid: 비정상 패킷
  # established, related: 연결 후 응답. ex) client -> webserver -> client
*/
```

```
# 서버 경유 불가
chain forward { type filter hook forward priority 0; policy drop; }
```

```
# 서버에서 바깥으로 나가는건 모두 허용
chain output  { type filter hook output  priority 0; policy accept; }
```


## 적용
```
sudo nft -c -f /etc/nftables.conf   # 문법 체크
sudo nft -f /etc/nftables.conf      # 적용
sudo nft list ruleset               # 확인
sudo systemctl enable nftables
sudo systemctl restart nftables

systemctl status nftables # 상태
```






## 3. nftables 기본 설정
테이블 & 체인 생성(원격에서는 실행 X)
```bash
sudo nft add table inet filter

sudo nft add chain inet filter input '{ type filter hook input priority 0; policy drop; }'
sudo nft add chain inet filter forward '{ type filter hook forward priority 0; policy drop; }'
sudo nft add chain inet filter output '{ type filter hook output priority 0; policy accept; }'
```

기본룰 추가
```bash
# 루프백 허용
sudo nft add rule inet filter input iif lo accept

# 이미 연결된 세션 허용
sudo nft add rule inet filter input ct state established,related accept

# SSH 허용 (22/tcp)
sudo nft add rule inet filter input tcp dport 22 accept

# HTTP/HTTPS 허용 (80, 443)
sudo nft add rule inet filter input tcp dport {80, 443} accept

# Ping 허용 (ICMP echo-request)
sudo nft add rule inet filter input icmp type echo-request accept
```

## 4. 규칙저장
```bash
# 규칙 저장
sudo nft list ruleset > /etc/nftables.conf

# netfilter-persistent로도 저장 가능
sudo netfilter-persistent save
```

## 5. 서비스 활성화
```bash
sudo systemctl enable nftables
sudo systemctl restart nftables
```

## 6. 확인
```bash
# 규칙 다시 확인
sudo nft list ruleset

# 포트 확인
sudo ss -tulnp
```
