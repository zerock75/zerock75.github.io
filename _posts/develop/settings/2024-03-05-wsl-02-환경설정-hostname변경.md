---
title: WSL 환경설정 - hostsname 변경
date: 2024-03-05
categories: [개발, 환경설정]
tag: [wsl]
mermaid: true
---

**1. /etc/wsl.conf 파일 수정**
```
[network]
hostname = <원하는호스트명>
generateHosts = false
```
{: file='/etc/wsl.conf'}

**2. /etc/hosts 파일에 수정한 hostname 등록**   
```
127.0.0.1 <수정한호스트명>
```
{: file='/etc/hosts'}


**3. wsl 재시작**
```console
wsl -t <Distro>
```   
