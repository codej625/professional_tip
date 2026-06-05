# 안드로이드 기기로 서버 만들기

<br />
<br />

* 테스트 서버가 필요한데..!

---

```
가끔 테스트 서버가 필요할 때가 있다.

이미 운영하고 있는 서버는 프로덕션용이라
리소스를 함부로 쓰기가 쉽지 않은 상황에서
놀고 있는 안드로이드 기기는 최고의 테스트 서버로 활용할 수 있다.
```

<br />
<br />
<br />
<br />

1. 사전 준비

```
안드로이드 12부터 생겨난 팬텀 프로세스 모니터라는 녀석은
시도때도없이 백그라운드 점유율이 높은 프로세스를 종료시킨다.

"Process completed signal 9 press enter"와 같은
문구를 보기 싫다면 안드로이드의 개발자 옵션을 활성화 시켜야 한다.

그리고 USB 디버깅을 체크한다.
```

```zsh
# 마지막으로 ADB(Android Debug Bridge)를 사용하기 위한 tool 설치
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
# 모니터링 기능 비활성화
adb shell "settings put global settings_enable_monitor_phantom_procs false"
```

<br />

```
각 명령어 입력 후 에러 메시지 없이 다음 입력줄(➜ ~)이 나오면 성공이다.

마지막으로 Termux 앱을 완전히 종료(강제 중지 또는 최근 앱 목록에서 제거)한 후 다시 실행한다.
```

<br />
<br />
<br />

3. 성공 확인

```zsh
# 팬텀 프로세스 제한 수치 확인
adb shell "device_config get activity_manager max_phantom_processes"
# 출력값 2147483647 나오면 성공
```

```zsh
# 모니터링 비활성화 확인
adb shell "settings get global settings_enable_monitor_phantom_procs"
# 출력값 false 나오면 성공
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
# 기본 세팅
pkg update && pkg upgrade -y
pkg install -y openssh proot-distro

# Android 내부 저장소(파일)에 Termux가 접근할 수 있게 권한 부여
termux-setup-storage
```

```zsh
# zsh 설치 (Termux 터미널용)
pkg install -y zsh
chsh -s zsh

# Termux 재시작 → 선택화면 나오면 0번 (~/.zshrc 생성)

# oh-my-zsh 설치
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

<br />
<br />
<br />

5. proot Ubuntu 설치

```
Cursor Remote SSH는 glibc 기반 바이너리를 사용한다.

Termux는 Android의 bionic libc를 사용하기 때문에 직접 연결이 불가능하다.

Termux에 SSH로 붙으면 비밀번호 입력 후 무한 로딩이 걸리는 것이 정상이다.

proot-distro로 Ubuntu 환경을 만들어 이 문제를 해결한다.
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

```
최초 1회만 root로 진행한다.

SSH 서버 설정, 사용자 생성 등 시스템 설정은 root 권한이 필요하다.
```

```bash
# 패키지 업데이트 및 SSH 서버 설치
apt update && apt install -y openssh-server sudo

# 사용자 생성
adduser codej625
# New password: 원하는 비밀번호 입력
# Retype new password: 재입력
# 이후 Full Name 등 추가 정보는 Enter로 건너뜀

# sudo 권한 부여
usermod -aG sudo codej625

# SSH 설정 (포트 8023)
# Termux sshd는 8022, Ubuntu sshd는 8023으로 분리한다.
echo "Port 8023" >> /etc/ssh/sshd_config

# 호스트 키 생성 (없으면 sshd가 시작되지 않음)
ssh-keygen -A

# SSH 서버 실행
/usr/sbin/sshd
```

<br />

```
PC에서 SSH 접속이 되는지 먼저 확인한다.

Cursor 연결 전에 이 단계가 통과되어야 한다.
```

```zsh
# PC에서
ssh codej625@192.168.x.x -p 8023
```

<br />
<br />
<br />

7. Ubuntu 개발 환경 설정

```
이후 설정은 codej625 계정으로 진행한다.

root로 설치하면 Cursor 접속 시 홈 디렉토리가 달라져서 설정이 꼬인다.
```

```bash
# codej625 계정으로 전환
su - codej625
```

```bash
# 기본 도구 설치 (sudo 필요)
sudo apt install -y git curl wget vim zsh build-essential libssl-dev python3

# git config 설정
git config --global user.name "codej625"
git config --global user.email "codej625@gmail.com"

# git config 내용 확인
git config --global --list
```

```bash
# zsh + oh-my-zsh 설치 (Ubuntu 셸용)
chsh -s $(which zsh)

sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

```bash
# nvm 설치
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash

# zshrc에 nvm 설정 추가
cat >> ~/.zshrc << 'EOF'
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"
EOF

source ~/.zshrc

# Node.js LTS 설치
nvm install --lts

# 버전 확인
node -v
npm -v
```

<br />
<br />
<br />

8. Cursor Remote SSH 연결

```
PC에서 ~/.ssh/config 파일에 아래 내용을 추가한다.

Android 기기의 IP는 Ubuntu 안에서 hostname -I 명령어로 확인한다.
```

```bash
# Android IP 확인 (Ubuntu 안에서)
hostname -I
```

```
# PC의 ~/.ssh/config 에 추가
Host android-server
  HostName 192.168.x.x  # Android IP
  User codej625
  Port 8023
```

```
Cursor에서
Ctrl+Shift+P → Remote-SSH: Connect to Host → android-server 선택
```

<br />
<br />
<br />

9. 매번 시작할 때

```
재부팅 또는 Termux 재시작 후에는 아래 한 줄만 실행하면 된다.

폰에서 Termux 앱을 직접 열어서 실행해야 한다. (PC SSH 세션에서 실행하면 안 됨)
```

```zsh
# Termux에서
proot-distro login ubuntu -- /usr/sbin/sshd
```

```
이후 Cursor에서 바로 접속 가능하다.
```

<br />
<br />
<br />

10. 작업 디렉토리

```
workspace는 codej625 홈 디렉토리에 두는 것이 성능상 가장 유리하다.

/sdcard/ 등 Android 파일시스템은 chmod, symlink 등이 제한되어
Node.js 프로젝트에서 문제가 생길 수 있다.
```

```bash
mkdir ~/workspace
```
