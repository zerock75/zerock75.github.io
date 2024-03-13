---
title: Docker 설치
date: 2024-02-22 
#categories: [Laravel, 환경설정]
tag: []
---

1. WSL 진입
```console
wsl -d <Distro> -u <사용자명>
```

2. 


docker 실행 시 오류가 났다.
``

cmd를 관리자 권한으로 실행 후 
```terminal
bcdedit /set hypervisorlaunchtype auto
```

https://laravel.com/docs/10.x

https://summer-cat93.tistory.com/44

1. 컴포저 설치: 

https://docs.docker.com/desktop/install/windows-install/   
[Docker Desktop for Windows] 를 다운로드 받아 설치한다.





php를 다운받아 설치한다.
Windows Server + IIS (ISAPI) -> TS 버전
Windows Server + IIS (CGI or FastCGI) -> NTS 버전
Windows Server + Apache -> TS 버전
Linux Server + Apache prefork -> NTS버전
Linux Server + Apache worker,event -> TS 버전
Linux Server + Nginx (PHP-FPM FastCGI) -> NTS버전


https://getcomposer.org/download/
Composer-Setup.exe 파일을 다운받아 설치한다.

https://git-scm.com/download/win
GIT 다운로드



composer update
composer create-project laravel/laravel 프로젝트명
php.ini에 
extension=fileinfo # 주석해제
extension=zip

#종속성 추가
#composer require laravel/sail --dev

hub.docker.com



docker 
원하는 디렉토리에서 마우스 오른쪽을 눌러서 `여기에서 Linux(L) 쉘 열기` 클릭
```shell
curl -s https://laravel.build/example-app | bash
curl -s "https://laravel.build/example-app?with=mysql" | bash
curl -s https://laravel.build/example-app | bash -s --directory test
```


wsl --list -v
wsl --shutdown


C:\Users\zeroc\AppData\Roaming\Docker\settings.json


디스크도 삭제된다.
wsl --unregister Ubuntu-22.04


wsl --list --online

wsl --install <버전>



C:\Users\<유저명>\AppData\Local\Packages\<리눅스이름>\LocalState
`C:\Users\zeroc\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu22.04LTS_79rhkp1fndgsc\LocalState`

export
```terminal
wsl --export Ubuntu-22.04 D:\Docker\wsl\Ubuntu-22.04.tar
wsl --import Ubuntu-22.04 D:\Docker\wsl\Ubuntu-22.04.vhdx (X)
```


wsl -l -v
wsl --list --online
wsl --install Ubuntu-22.04

sudo apt update
sudo apt upgrade

wsl -t Ubuntu-22.04
wsl --export Ubuntu-22.04 D:\Docker\Wsl\Ubuntu-22.04.vhdx --vhd
wsl --unregister Ubuntu-22.04
wsl --import-in-place  Ubuntu-22.04 D:\Docker\Wsl\Ubuntu-22.04.vhdx 
wsl -l -v

wsl -d Ubuntu-22.04
'D:\Docker\Wsl\Ubuntu-22.04.vhdx' 디스크를 WSL2에 연결하지 못했습니다. 액세스가 거부되었습니다.
Error code: Wsl/Service/CreateInstance/MountVhd/HCS/E_ACCESSDENIED

icacls "D:\Docker\Wsl\Ubuntu-22.04.vhdx" /grant Everyone:F /T


wsl -s Ubuntu-22.04



sudo snap install docker 

sudo usermod -aG docker $(whoami)

sudo chmod 666 /var/run/docker.sock




curl -s "https://laravel.build/laravel-4?with=mysql" | bash
curl -s "https://laravel.build/test-laravel?with=mysql" | bash


https://weingchicken.com/223
https://velog.io/@hwangduli515/Docker-%EB%8F%84%EC%BB%A4%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-laravel-nginx-mysql-ssl-%EC%97%B0%EB%8F%99

sudo service docker start