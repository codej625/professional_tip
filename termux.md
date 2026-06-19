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


`명령어 실행`

```zsh
# ADB(Android Debug Bridge) 설치
brew install --cask android-platform-tools
```

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

마지막으로 termux를 완전히 종료(강제 중지 또는 최근 앱 목록에서 제거)한 후 다시 실행한다.
```

<br />

`성공 확인`

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

2. Termux 초기 설정

```
Termux는 proot Ubuntu를 실행하기 위한 부트스트랩 역할만 한다.

git, node, nvm 등 개발 도구는 Termux가 아니라 Ubuntu 안에 설치한다.

Termux openssh는 패키지 의존성으로 함께 설치되지만,
Cursor 연결에는 사용하지 않는다. (호환성 때문에 Ubuntu 에서는 dropbear 사용)
```

<br />

`공통 세팅 (proot, proroot등을 사용 안 하더라도 필요한 기본 패키지)`

```zsh
# 기본 패키지 설치
pkg update && pkg upgrade -y
```

```zsh
# android 내부 저장소 접근 권한 부여
termux-setup-storage
```

```zsh
# ip addr 명령어로 아이피 확인
pkg install iproute2
```

```zsh
# openssh 설치 (원격 접속을 위해)
pkg install openssh
```

```zsh
# 비밀번호 설정 (SSH 접속 시 비밀번호 필요)
passwd
```

```zsh
# 리모트 접속 준비
sshd
```

<br />

`termux 만 사용 시 개발 도구 설치 (선택)`

```zsh
# 개발 도구 설치
pkg install -y git curl wget vim nodejs-lts
```

```zsh
# build-essential, libssl-dev, python3 대응
pkg install -y build-essential openssl python
```

```zsh
# git 전역 설정
git config --global user.name "아이디"
git config --global user.email "메일"

# 설정 확인
git config --global --list
```

```zsh
# node 버전 관리자 (n) 설치
npm install -g n

# N_PREFIX 설정 (.bashrc 또는 .zshrc에 추가)
echo 'export N_PREFIX=$PREFIX' >> ~/.bashrc
source ~/.bashrc

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
```

```zsh
# zsh 설치 (선택)
pkg install -y zsh

# 기본 쉘을 zsh로 변경
chsh -s zsh

# termux 재시작
# 처음 켤 때 선택 화면이 나오면 0번을 입력하여 ~/.zshrc 파일을 생성

# 깃 설치 (위에서 설치 안 했을 시)
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

```zsh
# Next를 사용한다면 안드로이드용 터보 팩 설치 (Next 버전 확인해야 함 패키지와 버전 동기화 필요)
pkg install turbopack

# 터보 팩 경로 찾기
dpkg -L turbopack

# 해당 경로가 존재해야 함
/data/data/com.termux/files/usr/etc/profile.d/turbopack.sh

# 바로 적용
source /data/data/com.termux/files/usr/etc/profile.d/turbopack.sh

# 출력 되는 결과가 있으면 성공
echo $NEXT_TEST_NATIVE_DIR
```

<br />
<br />
<br />

3. proot Ubuntu 설치 (필요 시)

```
Cursor, VSC Remote SSH는 glibc 기반 바이너리를 사용한다.

Termux는 Android의 bionic libc를 사용하기 때문에 직접 연결이 불가능하다.
(Termux에 SSH로 연결하면 비밀번호 입력 후 무한 로딩이 걸리는 것이 정상)

proot-distro로 Ubuntu 환경을 구성해 이 문제를 해결한다.

* Termux 안에서 실행을 전제로 함
```

<br />

```zsh
# proot-distro 설치
pkg install proot-distro -y
```

```zsh
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
```

```zsh
# ubuntu 진입
proot-distro login ubuntu
```

<br />

`우분투에서 초기 설정`

```zsh
# 패키지 저장소 업데이트 및 업그레이드
apt update && apt upgrade -y
```

```zsh
# 기존 충돌 패키지 정리
apt remove --purge openssh-server -y
apt autoremove -y
```

```zsh
# dropbear(경량 SSH) 설치
apt install dropbear -y
```

```zsh
# 필수 네트워크 도구 설치
apt install iproute2 -y
```

```zsh
# root 최고 관리자 비밀번호 설정 (Cursor 접속 시 사용)
passwd root
```

```zsh
# Dropbear 수동 실행 테스트
dropbear -p <PORT> -F -E
```

```zsh
# root로 테스트 (예시)
ssh -p <PORT> root@<IP>
```

<br />

`개발 도구 설치`

```zsh
# git, curl, wget, vim 등 필요한 패키지를 설치
apt install -y git curl wget vim build-essential libssl-dev python3
```

```zsh
# git 전역 설정
git config --global user.name "아이디"
git config --global user.email "메일"

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

```
workspace는 codej625 홈 디렉토리에 두는 것이 성능상 가장 유리하다.

/sdcard/ 등 Android 파일시스템은 chmod, symlink 등이 제한되어
Node.js 프로젝트에서 문제가 생길 수 있다.
```

```zsh
# 추가로 termux 에서 ubuntu으로 login을 해줘야 외부에서 ubuntu으로 접속이 가능하다.

# termux -> proot ubuntu 로그인
proot-distro login ubuntu

# Dropbear 실행
dropbear -p 2222 -F -E
```

<br />
<br />
<br />

4. 데이터베이스 서버 만들기

```
예시에서 데이터베이스 서버는 postgres를 사용한다.

2번 Termux 초기 설정과 공통사항까지만 설치하고 proot-distro는 필요없다.
```

<br />

```zsh
# postgres 설치
pkg install postgresql
```

```zsh
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

# 혹시 접속이 혹시 안 되면
psql -h 127.0.0.1 -p 5432 -U $(whoami) -d postgres
```

```zsh
# 유저 생성
CREATE USER <USER> WITH PASSWORD <'PASSWORD'>;

# 데이터베이스 생성
CREATE DATABASE <DATABASE> OWNER <USER>;

# 테스트
psql -h <IP> -p <PORT> -U <USER> -d <DATABASE>
```

<br />

`termux 재실행 시 자동 시작하기`

```zsh
# 쉘 설정 열기 (.bashrc 또는 .zshrc에 추가)
vim ~/.bashrc
```

```zsh
# 열리면 맨 아래에 추가 (옵션 1)
pg_ctl -D $PREFIX/var/lib/postgresql start

# 혹은 서버가 실행 중이면 스킵, 꺼져있으면 실행 - 둘 중 하나만 적용 (옵션 2)
pg_ctl -D $PREFIX/var/lib/postgresql status | grep -q "server is running" || pg_ctl -D $PREFIX/var/lib/postgresql start
```

```zsh
# 바로 적용 (zsh는 source ~/.zshrc)
bashsource ~/.bashrc
```

<br />

```zsh
# 데이터베이스 서버 작동 명령어 모음

# 서버 시작
pg_ctl -D $PREFIX/var/lib/postgresql start

# 서버 재시작 (설정 변경 후)
pg_ctl -D $PREFIX/var/lib/postgresql restart

# 서버 종료
pg_ctl -D $PREFIX/var/lib/postgresql stop

# 덤프 개별
pg_dump -Fc 디비명 > backup.dump

# 덤프 전체
pg_dumpall | gzip > all_backup.sql.gz

# 복원 개별
pg_restore -d 디비명 backup.dump

# 복원 전체
gunzip -c all_backup.sql.gz | psql
```

<br />
<br />
<br />

5. proot-distro 오버헤드 문제 해결 (편법?)

```
proot-distro 를 사용해서 ubuntu를 사용하면 오버헤드 문제가 반드시 생긴다.

하지만 Cursor Remote를 termux에는 바로 붙일 수 있는 방법이 없어
rsync를 사용해 코드를 동기화하는 방법을 공유해본다.
```

<br />

`기본 구조`

```
맥 -> cursor (코드 수정, 파일 검색, AI)
    |
    | -> rsync (코드 동기화)
    |
termux (npm run dev -> 개발 서버 실행)
    |
    |
    |
맥 브라우저 -> http://<TERMUX_IP>:<PORT>
```

<br />

`termux`

```zsh
# 패키지 업데이트
pkg update
```

```zsh
# rsync 설치
pkg install rsync

# 이후 사용할 프로젝트 클론
```

<br />

`mac`

```zsh
# 맥에서는 기본으로 rsync 설치되어 있음

# 파일 변경 자동 감지용으로 필요
brew install fswatch

# 이후 사용할 프로젝트 클론
```

<br />

`파일 동기화`

```
파일을 동기화 시킬것이기 때문에,
termux 에서는 dev 서버를 실행시킨다.
```

<br />

`맥에서 스크립트 만들기 (sync-to-termux.sh)`

```zsh
#!/bin/zsh

rsync -avz \
  --exclude node_modules \
  --exclude .next \
  --exclude .git \
  --exclude .env \
  -e "ssh -p <SSH_PORT>" \
  ~/<MAC_PATH>/<MAC_REPOSITORY>/ \
  <TERMUX_IP>:~/<TERMUX_PATH>/<TERMUX_REPOSITORY>/
  
echo "termux sync completed"
```

```zsh
# 레포별이 아닌 워크스페이스 통합버전

#!/bin/zsh

RSYNC_EXCLUDES=()

# npm 패키지 (mac/termux 바이너리 다름 → termux에서 npm install)
RSYNC_EXCLUDES+=(--exclude node_modules)
# next.js 빌드 캐시
RSYNC_EXCLUDES+=(--exclude .next)
# next.js static export
RSYNC_EXCLUDES+=(--exclude out)
# nestjs 빌드 결과
RSYNC_EXCLUDES+=(--exclude dist)
# git 히스토리
RSYNC_EXCLUDES+=(--exclude .git)
# 테스트 커버리지
RSYNC_EXCLUDES+=(--exclude coverage)
# turborepo 캐시
RSYNC_EXCLUDES+=(--exclude .turbo)
# vercel 캐시
RSYNC_EXCLUDES+=(--exclude .vercel)
# prisma generated (npm install 시 재생성)
RSYNC_EXCLUDES+=(--exclude '**/src/generated')
# 로컬 DB 데이터
RSYNC_EXCLUDES+=(--exclude postgres-data)
RSYNC_EXCLUDES+=(--exclude pgdata)
# mac 메타데이터
RSYNC_EXCLUDES+=(--exclude .DS_Store)
# ts 빌드 캐시
RSYNC_EXCLUDES+=(--exclude '*.tsbuildinfo')
# python 가상환경
RSYNC_EXCLUDES+=(--exclude .venv)
RSYNC_EXCLUDES+=(--exclude venv)
RSYNC_EXCLUDES+=(--exclude .tox)
RSYNC_EXCLUDES+=(--exclude .nox)
# python 바이트코드 · 캐시
RSYNC_EXCLUDES+=(--exclude __pycache__)
RSYNC_EXCLUDES+=(--exclude '*.py[cod]')
RSYNC_EXCLUDES+=(--exclude .pytest_cache)
RSYNC_EXCLUDES+=(--exclude .mypy_cache)
RSYNC_EXCLUDES+=(--exclude .ruff_cache)
RSYNC_EXCLUDES+=(--exclude .hypothesis)
# python 빌드 · 패키징 산출물
RSYNC_EXCLUDES+=(--exclude build)
RSYNC_EXCLUDES+=(--exclude '*.egg-info')
RSYNC_EXCLUDES+=(--exclude .eggs)
RSYNC_EXCLUDES+=(--exclude '*.so')
# python 테스트 커버리지
RSYNC_EXCLUDES+=(--exclude htmlcov)
RSYNC_EXCLUDES+=(--exclude .coverage)
# docker volume 바인드 마운트 (폴더명이 다르면 아래에 --exclude 한 줄씩 추가)
RSYNC_EXCLUDES+=(--exclude docker-data)

# MAC_PATH 와 TERMUX_PATH는 workspace으로 맞추면 편함 (안에 repository 저장)
rsync -avz --delete \
  "${RSYNC_EXCLUDES[@]}" \
  -e "ssh -p <PORT>" \
  ~/<MAC_PATH>/ \
  <TERMUX_IP>:~/<TERMUX_PATH>/

echo "termux sync completed"
```

<br />

```zsh
# 파일 권한 주기
chmod +x ~/<PATH>/sync-to-termux.sh

# 동기화 스크립트 실행
~/<PATH>/sync-to-termux.sh
```

<br />

`alias 설정 및 싱크 자동 감지 스크립트 ~/.zshrc 에 추가 (선택)`

```zsh
# vim ~/.zshrc

# 한 번 수동 실행
alias syncreponame='~/workspace/repo/sync-to-termux.sh'
# 싱크 감지 자동 실행
alias watchreponame='fswatch -o ~/workspace/repo | xargs -n1 ~/workspace/repo/sync-to-termux.sh'

# 워크스페이스 통합 스크립트트 실행 시 사용 (위에 두 개는 사용 안 함)
alias sync='~/workspace/sync-to-termux.sh'
alias watch='fswatch -o ~/workspace/ | xargs -n1 ~/workspace/sync-to-termux.sh'

# 쉘에 변경 내용 적용
source ~/.zshrc

# 자동 감지 시작 (터미널에서 alias 사용)
watchphone

# 강제 종료
pkill -f fswatch
```

<br />

`매 번 비밀번호를 입력할수 없으니 SSH 키로 인증한다. (맥에서)`

```zsh
# SSH KEY 생성
ssh-keygen -t rsa -b 4096
```

```zsh
# termux에 공개키를 키를 복사
ssh-copy-id -p <TERMUX_PORT> <TERMUX_IP>
```

```zsh
# 접속 테스트 (맥에서)
ssh -p <TERMUX_PORT> <TERMUX_IP>
```

<br />
<br />
<br />

6. 기타

```
여러 가지 문제 해결 방법
혹은 기타사항 등을 정리
```

<br />

`아래처럼 SHH 접속이 안 될 때 조치`

```
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
@    WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED!     @
@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
```

```zsh
# ssh-keygen -R '<IP>:<PORT>' 입력 예시
ssh-keygen -R '[192.168.0.11]:8022'

# [] 안에 아이피를 넣는 이유는
# 비표준 포트(8022)를 쓰면 known_hosts에는 보통 [192.168.0.11]:8022 이렇게 저장
```
