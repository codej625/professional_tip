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

<br />

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

<br />

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
Cursor 연결에는 사용하지 않는다. (호환성 때문에 Ubuntu 에서는 dropbear 사용)
```

<br />

```zsh
# 기본 패키지 설치
pkg update && pkg upgrade -y

# android 내부 저장소 접근 권한 부여
termux-setup-storage

# openssh 설치 (원격 접속을 위해)
pkg install openssh

# 비밀번호 설정 (SSH 접속 시 비밀번호 필요)
passwd

# 리모트 접속 준비
sshd

# ==============================
# termux 만 사용 시 (선택)

pkg install -y git curl wget vim nodejs-lts

# build-essential, libssl-dev, python3 대응
pkg install -y build-essential openssl python

# node 버전 관리자 (n) 설치
npm install -g n

# 버전 설치 및 전환 (설치 + 전환 동시)
# n 18        Node 18 설치 + 전환
# n 20        Node 20 설치 + 전환
# n lts       최신 LTS 설치 + 전환
# n latest    최신 버전 설치 + 전환

# 이미 설치된 버전으로 전환 (재다운로드 없이 캐시에서 즉시 전환)
# n use 18    Node 18로 전환
# n use 20    Node 20으로 전환

# 버전 관리
# n ls        설치된 버전 목록
# n rm 18     특정 버전 삭제

# node.js 버전 확인
node -v && npm -v
# ==============================
```

```zsh
# zsh 설치 (선택)
pkg install -y zsh

# 기본 쉘을 zsh로 변경
chsh -s zsh

# termux 재시작
# 처음 켤 때 선택 화면이 나오면 0번을 입력하여 ~/.zshrc 파일을 생성

# 깃 설치
pkg install -y git

# oh-my-zsh 설치
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# 현재 기본 쉘이 뭔지 확인
echo $SHELL

# zsh가 제대로 설치됐는지
which zsh

# 쉘 환경 설정 새로고침
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

<br />

```zsh
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
```

```zsh
# ubuntu 설치
proot-distro install ubuntu

# ubuntu 진입
proot-distro login ubuntu
```

<br />
<br />
<br />

6. Ubuntu 초기 설정

```zsh
# 패키지 저장소 업데이트 및 업그레이드
apt update && apt upgrade -y

# 기존 충돌 패키지 정리
apt remove --purge openssh-server -y
apt autoremove -y

# dropbear(경량 SSH) 설치
apt install dropbear -y

# 필수 네트워크 도구 설치
apt install iproute2 -y

# root 최고 관리자 비밀번호 설정 (Cursor 접속 시 사용)
passwd root

# Dropbear 수동 실행 테스트
dropbear -p 2222 -F -E
```

```zsh
# root로 테스트 (예시)
ssh -p 2222 root@192.168.0.11
```

<br />
<br />
<br />

7. Ubuntu 개발 환경 설정

```zsh
# 기본 도구 설치
apt install -y git curl wget vim build-essential libssl-dev python3

# git 전역 설정
git config --global user.name "codej625"
git config --global user.email "codej625@gmail.com"

# 설정 확인
git config --global --list
```

```zsh
# zsh 설치
apt install zsh

# 기본 쉘을 zsh로 변경 (PRoot 환경 호환성 설정)
cat >> /etc/profile << 'EOF'

## Force switch to internal ZSH
if [ -x /bin/zsh ]; then
    export SHELL=/bin/zsh
    exec /bin/zsh -l
fi
EOF

# oh-my-zsh 설치
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# zsh가 제대로 설치됐는지
which zsh

# zsh 실행
zsh

# 현재 기본 쉘이 뭔지 확인
echo $SHELL
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

# node.js lts 설치 및 버전 확인
nvm install --lts
node -v && npm -v
```

<br />
<br />
<br />

8. 작업 디렉토리 및 실행

```
workspace는 codej625 홈 디렉토리에 두는 것이 성능상 가장 유리하다.

/sdcard/ 등 Android 파일시스템은 chmod, symlink 등이 제한되어
Node.js 프로젝트에서 문제가 생길 수 있다.
```

<br />

```zsh
# termux 에서 ubuntu으로 login을 해줘야 외부에서 ubuntu으로 접속이 가능
proot-distro login ubuntu
dropbear -p 2222 -F -E
```

<br />
<br />
<br />

9. 보너스 (데이터베이스 서비스 만들기)

```
예시에서 데이터베이스 서버는 postgres를 사용한다.

이번에는 termux까지만 설치하고 proot-distro는 필요없다.
```

<br />

```zsh
# postgres 설치
pkg install postgresql

# 데이터 디렉터리 초기화 (termux 권장 경로)
mkdir -p $PREFIX/var/lib/postgresql
initdb -D $PREFIX/var/lib/postgresql
```

```zsh
# postgresql.conf 일부 수정 ($PREFIX/var/lib/postgresql/postgresql.conf)
listen_addresses = '*'
port = 5432
```

```zsh
# pg_hba.conf 일부 수정 ($PREFIX/var/lib/postgresql/pg_hba.conf) 파일 맨 아래에 추가

# Wi-Fi 대역만 허용
host    all             all             192.168.0.0/24          md5

# 혹은 전체 허용 (둘 중 하나만 적용)
host    all             all             0.0.0.0/0               md5
```

```zsh
# 서버 시작
pg_ctl -D $PREFIX/var/lib/postgresql start
```

```zsh
# postgres 접속
psql -U $(whoami) -d postgres

# 접속이 혹시 안 되면
psql -h 127.0.0.1 -p 5432 -U $(whoami) -d postgres

# 유저 생성
CREATE USER 아이디명 WITH PASSWORD '비밀번호';

# 데이터베이스 생성
CREATE DATABASE 데이터베이스명 OWNER 아이디;

# 테스트
psql -h IP입력 -p 5432 -U 아이디명 -d 데이터베이스명
```

```zsh
# termux 재실행 시 자동 시작하기

# bash 버전
vim ~/.bashrc

# 열리면 맨 아래에 추가 (옵션 1)
pg_ctl -D $PREFIX/var/lib/postgresql start

# 혹은 서버가 실행 중이면 스킵, 꺼져있으면 실행 - 둘 중 하나만 적용 (옵션 2)
pg_ctl -D $PREFIX/var/lib/postgresql status | grep -q "server is running" || pg_ctl -D $PREFIX/var/lib/postgresql start

# 바로 적용
bashsource ~/.bashrc

===============================================

# zsh 버전
vim ~/.zshrc

# 열리면 맨 아래에 추가 (옵션 1)
pg_ctl -D $PREFIX/var/lib/postgresql start

# 혹은 서버가 실행 중이면 스킵, 꺼져있으면 실행 - 둘 중 하나만 적용 (옵션 2)
pg_ctl -D $PREFIX/var/lib/postgresql status | grep -q "server is running" || pg_ctl -D $PREFIX/var/lib/postgresql start

# 바로 적용
source ~/.zshrc
```

```zsh
# 서버 작동 명령어 모음

# 서버 시작
pg_ctl -D $PREFIX/var/lib/postgresql start

# 서버 재시작 (설정 변경 후)
pg_ctl -D $PREFIX/var/lib/postgresql restart

# 서버 종료
pg_ctl -D $PREFIX/var/lib/postgresql stop
```

<br />
<br />
<br />

10. 기타

```
아래처럼 SHH 접속이 안 될 때 조치

@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
```

```zsh
# ssh-keygen -R '[ip]:port' 입력 예시
ssh-keygen -R '[192.168.0.11]:8022'
```
