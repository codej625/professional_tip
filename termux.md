# 안드로이드 기기로 서버 만들기

<br />
<br />

* 테스트 서버가 필요한데..!

---

```
가끔 테스트 서버가 필요할 때가 있다.

이미 운영 중인 서버는 프로덕션용이라 리소스를 함부로 쓰기 어렵다.
이럴 때 놀고 있는 안드로이드 기기를 테스트 서버로 활용할 수 있다.
```

<br />
<br />
<br />
<br />

1. 사전 준비

```
안드로이드 12부터 도입된 팬텀 프로세스 킬러는
백그라운드 점유율이 높은 프로세스를 수시로 강제 종료한다.

"Process completed signal 9 press enter" 메시지를 보지 않으려면
안드로이드 개발자 옵션을 활성화하고 USB 디버깅을 체크해야 한다.
```

```zsh
# ADB(Android Debug Bridge) 설치
brew install --cask android-platform-tools
```

<br />
<br />
<br />

2. 명령어 실행

```zsh
# 기기 연결 확인
adb devices
```

```zsh
# 팬텀 프로세스 제한 무력화 (핵심)
adb shell "/system/bin/device_config set_sync_disabled_for_tests persistent; /system/bin/device_config put activity_manager max_phantom_processes 2147483647"
```

```zsh
# 팬텀 프로세스 모니터링 비활성화
adb shell "settings put global settings_enable_monitor_phantom_procs false"
```

```
각 명령어 입력 후 에러 없이 다음 입력줄(➜ ~)이 나오면 성공이다.

마지막으로 Termux를 완전히 종료(강제 중지 또는 최근 앱 목록에서 제거)한 후 다시 실행한다.
```

<br />
<br />
<br />

3. 성공 확인

```zsh
# 팬텀 프로세스 제한 수치 확인
adb shell "device_config get activity_manager max_phantom_processes"
# 출력값이 2147483647이면 성공
```

```zsh
# 모니터링 비활성화 확인
adb shell "settings get global settings_enable_monitor_phantom_procs"
# 출력값이 false이면 성공
```

<br />
<br />
<br />

4. Termux 초기 설정

```
Termux는 proot Ubuntu를 실행하기 위한 부트스트랩 역할만 한다.

git, node, nvm 등 개발 도구는 Termux가 아니라 Ubuntu 안에 설치한다.

Termux openssh는 패키지 의존성으로 함께 설치되지만,
Cursor 연결에는 사용하지 않는다. (Ubuntu sshd가 담당)
```

```zsh
# 기본 패키지 설치
pkg update && pkg upgrade -y

# openssh 설치
pkg install openssh

# 비밀번호 설정 (SSH 접속 시 사용)
passwd

# 리모트 접속을 위해
sshd

# proot-distro 설치
pkg install proot-distro -y

# pkg install proot-distro 설치 실패 시 이렇게 실행해야 한다.
# py3compile, py3clean 임시 제거
mv /data/data/com.termux/files/usr/bin/py3compile /data/data/com.termux/files/usr/bin/py3compile.bak
mv /data/data/com.termux/files/usr/bin/py3clean /data/data/com.termux/files/usr/bin/py3clean.bak

# 재설치
pkg install proot-distro -y

# 복구
mv /data/data/com.termux/files/usr/bin/py3compile.bak /data/data/com.termux/files/usr/bin/py3compile
mv /data/data/com.termux/files/usr/bin/py3clean.bak /data/data/com.termux/files/usr/bin/py3clean

# Android 내부 저장소 접근 권한 부여
termux-setup-storage
```

```zsh
# zsh 설치 (선택)
pkg install -y zsh

# 기본 쉘을 zsh로 변경
chsh -s zsh

# Termux 재시작
# 처음 켤 때 선택 화면이 나오면 0번을 입력하여 ~/.zshrc 파일을 생성

# 깃 설치
pkg install -y git

# oh-my-zsh 설치
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# 4. 쉘 환경 설정 새로고침
source ~/.zshrc
```

<br />
<br />
<br />

5. proot Ubuntu 설치

```
Cursor Remote SSH는 glibc 기반 바이너리를 사용한다.

Termux는 Android의 bionic libc를 사용하기 때문에 직접 연결이 불가능하다.
(Termux에 SSH로 연결하면 비밀번호 입력 후 무한 로딩이 걸리는 것이 정상)

proot-distro로 Ubuntu 환경을 구성해 이 문제를 해결한다.
```

```zsh
# Ubuntu 설치
proot-distro install ubuntu

# Ubuntu 진입
proot-distro login ubuntu
```

<br />
<br />
<br />

6. Ubuntu 초기 설정

```zsh
apt update && apt upgrade -y
apt install openssh-server sudo -y

# root 비밀번호 설정
passwd root

# 일반 사용자 계정 생성 (원하는 이름으로 변경 가능, 예시 -> dev)
adduser dev

# sudo 권한 주기 (선택)
usermod -aG sudo dev

# SSH 설정 (root 허용 + dev 계정도 허용)
cat >> /etc/ssh/sshd_config << EOF

Port 8023
PermitRootLogin yes
PasswordAuthentication yes
AllowUsers root dev
EOF

# HostKey 생성
ssh-keygen -A

# sshd 시작
sudo service ssh restart

# 확인
ps aux | grep sshd
echo "Setup complete"

# sshd 실행 (sshd가 꺼져있으면)
/usr/sbin/sshd
```

```zsh
# root로 테스트
ssh -p 8023 root@192.168.0.11

# dev 계정으로 테스트
ssh -p 8023 dev@192.168.0.11
```

<br />
<br />
<br />

7. Ubuntu 개발 환경 설정

```zsh
# 기본 도구 설치
sudo apt install -y git curl wget vim zsh build-essential libssl-dev python3

# git 전역 설정
git config --global user.name "codej625"
git config --global user.email "codej625@gmail.com"

# 설정 확인
git config --global --list
```

```zsh
# zsh 설치
chsh -s $(which zsh)

# oh-my-zsh 설치
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# 현재 기본 쉘이 뭔지 확인
echo $SHELL

# zsh가 제대로 설치됐는지
which zsh
```

```zsh
# nvm 설치
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

# zshrc에 nvm 설정 추가
cat >> ~/.zshrc << 'EOF'
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
EOF

source ~/.zshrc

# Node.js LTS 설치 및 버전 확인
nvm install --lts
node -v && npm -v
```

<br />
<br />
<br />

8. 작업 디렉토리

```
workspace는 codej625 홈 디렉토리에 두는 것이 성능상 가장 유리하다.

/sdcard/ 등 Android 파일시스템은 chmod, symlink 등이 제한되어
Node.js 프로젝트에서 문제가 생길 수 있다.
```

```zsh
mkdir ~/workspace
```
