---
title: Docker Desktop 저장위치 변경
date: 2026-03-31
categories: [Docker, backup]
---

# 도커 데스크톱 저장 위치 변경 및 복원

Docker는 WSL 위에서 동작하며, WSL의 기본 OS는 **C** 드라이브에 위치한다.

작업 중인 소스 파일이 **C** 드라이브에 있다면 OS 재설치 시 데이터를 잃을 위험이 있다.
이를 예방하려면 **C** 드라이브에 위치한 **Docker Image**를 **D** 드라이브 등 다른 곳으로 이동해야 한다.

먼저 구조부터 확인해 보자.

```powershell
wsl -l -v
------------------------------------------------------
  NAME              STATE           VERSION
* Ubuntu            Running         2
  docker-desktop    Running         2
```
여기서 **Ubuntu**가 `우리가 작업하는 소스들이 위치한 곳`이다.
**docker-desktop**은 Docker **전용 OS**이다.

각 파일들은 **vhdx** 파일로 관리되며, 기본 위치는 다음과 같다.

- **Ubuntu**: `C:\Users\{사용자명}\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu_<id>\LocalState\ext4.vhdx`
- **docker-desktop**: `C:\Users\{사용자명}\AppData\Local\Docker\wsl\main\ext4.vhdx`

참고로 도커 컨테이너 및 이미지의 기본 위치는
`C:\Users\{사용자명}\AppData\Local\Docker\wsl\disk\docker_data.vhdx` 이다.


## WSL 기본 배포판 다른 드라이브 이전
**작업 중인 소스들의 저장 위치부터 변경**해 보자.

1. WSL 데이터 추출.
```powershell
   wsl --export Ubuntu "<export_할_파일명>".tar
```
   {: .nolineno }

2. C 드라이브에 설치된 기존 배포판 삭제
```powershell
   wsl --unregister Ubuntu
```
   {: .nolineno }

3. 다른 드라이브의 지정된 경로로 복원 (새로운 VHD 생성)
```powershell
   wsl --import Ubuntu "<새로운_저장_위치>" "<export_한_파일명>".tar
```
   {: .nolineno }

4. 추출한 데이터 삭제.
```powershell
   del "<export_한_파일명>".tar
```
   {: .nolineno }


## 이전에 사용한 WSL 복원
쭉 사용하다가 OS가 느려지거나 문제가 발생한 경우, OS를 재설치하고 기존 Docker 컨테이너의 소스코드를 복원해야 한다.

1. WSL 설치
```powershell
   wsl --install --no-distribution
```
   {: .nolineno }


   > --no-distribution 옵션을 주어서 기본 OS를 설치하지 않도록 한다.
   {: .prompt-tip}

2. 기존 VHDX를 WSL에 직접 등록
```powershell
   wsl --import-in-place Ubuntu "<기존에_사용하던_파일명>.vhdx"
```
   {: .nolineno }

3. 기본 배포판으로 설정
```powershell
   wsl --set-default Ubuntu
```
   {: .nolineno }

4. 정상 확인
```powershell
   wsl -l -v
```
   {: .nolineno }


## Docker Desktop 복원
Docker Desktop 또한 기본 저장 위치가 **C** 드라이브이므로 다른 드라이브로 이동해야 한다.

1. WSL 종료
```powershell
   wsl --shutdown
```
   {: .nolineno }

2. 기존 파일 이동.
예) `D:\Docker-Data\DockerDesktopWSL\disk\docker_data.vhdx`에 위치해 있을 때
```powershell
   move D:\Docker-Data D:\Docker-Data.bak
```
   {: .nolineno }

3. Docker Desktop 설치

4. `Docker Desktop > Settings`에서 `Disk image location`을 D 드라이브로 지정
```powershell
   D:\Docker-Data
```
   {: .nolineno }

5. WSL 종료
```powershell
   wsl --shutdown
```
   {: .nolineno }


6. `D:\Docker-Data.bak` 폴더의 `docker_data.vhdx` 파일을 `D:\Docker-Data`로 이동
```powershell
   del D:\Docker-Data\DockerDesktopWSL\disk\docker_data.vhdx
   move D:\Docker-Data.bak\DockerDesktopWSL\disk\docker_data.vhdx D:\Docker-Data\DockerDesktopWSL\disk\docker_data.vhdx
```
   {: .nolineno }
