---
title: SSL인증서 자동 갱신(Let's Encrypt)
date: 2026-07-21
categories: [ssl인증서, Let's Encrypt, acme.sh, CloudFlare]
---

## CloudeFlare 에서 작업하기

### 1. 도메인의 네임서버를 CloudFlare 로 이전한다.
자동갱신을 위해서는 DNS 관리 권한을 API 가 되는 곳으로 옮겨야 한다.
![이전할 도메인을 넣는다.](/imgs/servers/Ubuntu/2026-07-21/01-cloudflare-add-comain.png)
Add a domain 에 해당 도메인을 넣고 `connect` 버튼이 나오면 `connect` 버튼을 클릭한다.

### 2. 도메인 연결
추가구성 항목에 봇에 대한 검색을 허용여부를 판단 후 `계속` 버튼을 클릭한다.
![도메인 연결](/imgs/servers/Ubuntu/2026-07-21/02-cloudflare-connect-domain.png)

### 3. 요금제 선택
요금제 선택화면에서 `무료` 요금제를 선택한다.
![요금제 선택](/imgs/servers/Ubuntu/2026-07-21/03-cloudflare-price.png)


### 4. DNS 레코드 추가
옮겨올 도메인의 DNS 레코드를 추가 후 프록시 상태를 전부 `OFF` 한다.
![DNS 레코드 추가](/imgs/servers/Ubuntu/2026-07-21/04-cloudflare-dns-record-add.png)

[활성화료 계속] 버튼을 클릭한다.


### 5. CloudFlare 의 네임서버주소 복사하기
![네임서버주소 복사](/imgs/servers/Ubuntu/2026-07-21/05-cloudflare-nsname.png)

복사한 주소를 기존에 사용하던 도메인의 네임서버를 변경한다.

![네임서버 변경](/imgs/servers/Ubuntu/2026-07-21/06-name-server-change.png)


### 6. CloudFlare 에서 토큰 생성하기
`내 아이콘 > 프로필 > API 토큰` 메뉴로 이동한다.
![토큰생성 메뉴](/imgs/servers/Ubuntu/2026-07-21/07-cloudflare-create-token.png)

`토큰 생성` 버튼을 누른 후 나오는 페이지에서 `영역 DNS 편집` 의 `템플릿 사용`을 클릭한다.
![템플릿 사용](/imgs/servers/Ubuntu/2026-07-21/08-cloudflare-token-template.png)

`영역 리소스` 에서 `포함`, `계정의 모든 영역`, `계정아이디` 를 선택한 후 `요약 계속` 버튼을 클릭한다.
![템플릿 사용](/imgs/servers/Ubuntu/2026-07-21/09-cloudflare-resource.png)

`토큰 생성` 버튼을 클릭한다.
![토큰생성 확인](/imgs/servers/Ubuntu/2026-07-21/10-cloudflare-token-confirm.png)

생성된 토큰을 복사해서 메모장에 복사해 둔다.
![토큰복사](/imgs/servers/Ubuntu/2026-07-21/11-cloudflare-token-copy.png)



## 서버에서 자동화

### 1. acme.sh 설치
```bash
curl https://get.acme.sh | sh -s email=your@email.com
source ~/.bashrc
```
설치하면 cron에 매일 1회 갱신 체크가 자동 등록된다. 확인:
```
[root@myserver:~]# crontab -l | grep acme
0 5,11,17,23 * * * "/root/.acme.sh"/acme.sh --cron --home "/root/.acme.sh" > /dev/null
```
매일 5시, 11시, 17시, 23시에 하루 4번 6시간 간격으로 실행되지만, 
하루 4번 갱신하는 것은 아니고 만료일 30일이 남은 것만 갱신된다.
참고로 Let's Encrypt 인증서는 90일(3달)이다.
고로 60일마다 갱신되는 것이다.


### 2. DNS API 토큰 설정
```bash
export CF_Token="클라우드플레어에서_발급한_토큰"
```
인증서 발급 성공 시 acmd.sh 가 이 값을 `~/.acme.sh/account.conf` 에 저장해서 이후 갱신 때 재사용한다.


### 3. 인증서 발급
```bash
acme.sh --issue \
  -d mydomain.com -d '*.mydomain.com' \
  --dns dns_cf \
  --server letsencrypt \
  --keylength 2048
```
인증서가 발급되면  `~/.acme.sh/` 폴더에 디렉토리가 생성되며 인증서가 저장된다.

### 4. 서버에 설치
```bash
mkdir -p /etc/ssl/mydomain.com

chmod 600 /etc/ssl/mydomain.com/privkey.pem

acme.sh --install-cert -d mydomain.com \
  --key-file       /etc/ssl/mydomain.com/privkey.pem \
  --fullchain-file /etc/ssl/mydomain.com/fullchain.pem \
  --reloadcmd      "systemctl reload apache2"
```
nginx 의 경우 `--reloadcmd` 옵션에 `apache2` 대신 `nginx`를 넣으면 된다.

VirtualHost 설정:
```bash
<VirtualHost *:443>
    ServerName mydomain.com
    ServerAlias *.mydomain.com

    SSLEngine on
    SSLCertificateFile      /etc/ssl/mydomain.com/fullchain.pem
    SSLCertificateKeyFile   /etc/ssl/mydomain.com/privkey.pem
</VirtualHost>
```

Nginx 설정:
```bash
server {
	listen		443 ssl;
	server_name	mydomain.com www.mydomain.com;

	ssl_certificate_key	/etc/ssl/mydomain.com/privkey.pem;
	ssl_certificate		/etc/ssl/mydomain.com/fullchain.pem;
}
```

재시작(Apache)
```bash
apachectl configtest && systemctl reload apache2
```

재시작(Nginx)
```bash
nginx -t && systemctl reload nginx
```

### 5. 검증
```bash
# 인증서 적용 확인
echo | openssl s_client -connect mydomain.com:443 -servername mydomain.com 2>/dev/null | openssl x509 -noout -issuer -dates -ext subjectAltName

# 갱신 시뮬레이션 (강제 갱신 테스트)
acme.sh --renew -d mydomain.com --force
```

