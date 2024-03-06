---
title: Docker 설치
date: 2024-02-22 
#categories: [Laravel, 환경설정]
tag: []
---

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



사용법: wsl.exe [인수] [옵션...] [CommandLine]

Linux 이진 파일을 실행하기 위한 인수:

    명령줄이 제공되지 않으면 wsl.exe는 기본 셸을 시작합니다.

    --exec, -e <CommandLine>
        기본 Linux 셸을 사용하지 않고 지정된 명령을 실행합니다.

    --shell-type <standard|login|none>
        제공된 셸 형식으로 지정된 명령을 실행합니다.

    --
        나머지 명령줄을 있는 그대로 전달합니다.

옵션:
    --cd <디렉터리>
        지정된 디렉터리를 현재 작업 디렉터리로 설정합니다.
        ~ 가 사용되는 경우 Linux 사용자의 홈 경로가 사용됩니다. 경로가 시작되면
        / 문자를 사용하면 절대 Linux 경로로 해석됩니다.
        그렇지 않으면 값이 절대 Windows 경로여야 합니다.

    --distribution, -d <Distro>
        지정된 배포를 실행합니다.

    --user, -u <UserName>
        지정된 사용자로 실행합니다.

    --system
        시스템 배포에 대한 셸을 시작합니다.

Linux용 Windows 하위 시스템을 관리하기 위한 인수:

    --help
        사용 정보를 표시합니다.

    --debug-shell
        진단을 위해 WSL2 디버그 셸을 엽니다.

    --install [Distro] [옵션...]
        Linux 배포용 Windows 하위 시스템을 설치합니다.
        유효한 배포 목록의 경우 'wsl.exe --list --online'을 사용합니다.

        옵션:
            --no-launch, -n
                설치 후 배포를 시작하지 마세요.

            --web-download
                Microsoft Store 대신 인터넷에서 배포를 다운로드합니다.

            --no-distribution
                필요한 선택적 구성 요소만 설치하고 배포를 설치하지 않습니다.

            --enable-wsl1
                WSL1 지원을 사용하도록 설정합니다.

    --manage <Distro> <옵션...>
        배포판 관련 옵션을 변경합니다.

        옵션:
            --set-sparse, -s <true|false>
                배포판의 vhdx를 스파스로 설정하여 디스크 공간을 자동으로 회수할 수 있도록 합니다.

    --mount <디스크>
        모든 WSL 2 배포에서 실제 또는 가상 디스크를 연결하고 탑재합니다.

        옵션:
            --vhd
                <디스크>가 가상 하드 디스크를 참조하도록 지정합니다.

            --bare
                디스크를 WSL2에 연결하고 탑재하지는 마세요.

            --name <이름>
                탑재 지점의 사용자 지정 이름을 사용하여 디스크를 탑재합니다.

            --type <유형>
                디스크를 탑재할 때 사용할 파일 시스템이 지정되지 않은 경우 기본적으로 ext4입니다.

            --options <옵션>
                추가 탑재 옵션입니다.

            --partition <인덱스>
                탑재할 파티션의 인덱스가 지정되지 않은 경우 기본값은 전체 디스크입니다.

    --set-default-version <버전>
        새 배포에 대한 기본 설치 버전을 변경합니다.

    --shutdown
        실행 중인 모든 배포와 WSL 2을 즉시 종료합니다.
        경량 유틸리티 가상 머신입니다.

    --status
        Linux용 Windows 하위 시스템 상태를 표시합니다.

    --unmount [디스크]
        모든 WSL2 배포에서 디스크를 해제하고 분리합니다.
        인수 없이 호출되는 경우 모든 디스크를 해제하고 분리합니다.

    --update
        Linux 패키지용 Windows 하위 시스템을 업데이트합니다.

        옵션:
            --pre-release
                사용 가능한 경우 시험판 버전을 다운로드합니다.

    --version, -v
        버전 정보를 표시합니다.

Linux용 Windows 하위 시스템 배포를 관리하기 위한 인수:

    --export <Distro> <FileName> [옵션]
        배포를 tar 파일로 내보냅니다.
        파일 이름은 - for stdout 일 수 있습니다.

        옵션:
            --vhd
                배포를 .vhdx 파일로 내보내도록 지정합니다.

    --import <Distro> <InstallLocation> <FileName> [옵션]
        지정된 tar 파일을 새 배포로 가져옵니다.
        파일 이름은 - for stdin 일 수 있습니다.

        옵션:
            --version <버전>
                새 배포에 사용할 버전을 지정합니다.

            --vhd
                제공된 파일이 tar 파일이 아닌 .vhdx 파일임을 지정합니다.
                이 작업은 지정된 설치 위치에 .vhdx 파일의 복사본을 만듭니다.

    --import-in-place <Distro> <FileName>
        지정된 .vhdx 파일을 새 배포판로 가져옵니다.
        이 가상 하드 디스크는 ext4 파일 시스템 형식으로 포맷해야 합니다.

    --list, -l [옵션]
        배포를 나열합니다.

        옵션:
            --all

배포를 포함하여 모든 배포를 나열합니다.
                현재 설치 중이거나 제거되고 있습니다.

            --running
                현재 실행 중인 배포만 나열합니다.

            --quiet, -q
                배포 이름만 표시합니다.

            --verbose, -v
                모든 배포에 대한 자세한 정보를 표시합니다.

            --online, -o
                'wsl.exe --install'을 사용하여 설치에 사용할 수 있는 배포 목록을 표시합니다.

    --set-default, -s <Distro>
        배포를 기본값으로 설정합니다.

    --set-version <Distro> <버전>
        지정된 배포의 버전을 변경합니다.

    --terminate, -t <Distro>
        지정된 배포를 종료합니다.

    --unregister <Distro>
        배포를 등록 취소하고 루트 파일 시스템을 삭제합니다