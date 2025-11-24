# VM에 Ubuntu를 Host OS로 사용하기

<br />
<br />

* Ubuntu를 Host OS로 사용하는 이유

---

```
Ubuntu 24.04 LTS(Long Term Support)는 5년간 지원을 제공하는 Ubuntu의 최신 LTS 버전으로,
안정적이고 보안이 강화된 서버 운영체제를 제공한다.

Ubuntu Server는 네트워크 및 서비스 맞춤 Linux 배포판이다.
```

<br />
<br />
<br />
<br />

1. VM에 ios 이미지를 마운트하고 순서대로 운영체제를 설치한다. (설치 과정 생략)

```zsh
# 운영체제 설치 완료 시 아래를 차례대로 실행

sudo apt update && sudo apt upgrade -y # 최신 패키지와 보안 업데이트를 설치

sudo apt install openssh-server -y # OpenSSH 서버

sudo apt install ibus-hangul # 한글 입력기 설치
```

<br />
<br />
<br />

2. 방화벽 (UFW) 설정

```
우분투는 ufw라는 방화벽 도구가 기본 내장되어 있지만 비활성화되어 있다.

방화벽을 활성화하고, SSH 포트(22)를 열어 외부 접속 환경을 만들어줘야 한다.
```

```zsh
# SSH 포트 허용 (기본 22번)
sudo ufw allow ssh

# (옵션) 웹서버를 돌릴 거라면
# sudo ufw allow 80/tcp  # HTTP
# sudo ufw allow 443/tcp # HTTPS

# 방화벽 활성화
sudo ufw enable

# 상태 확인
sudo ufw status
```

<br />
<br />
<br />

3. SSH 보안 설정 (권장)

```
서버 해킹 시도의 99%는 SSH 브루트 포스(무차별 대입) 공격이다.

최소한의 보안 설정을 해두는 것이 좋다.
```

```zsh
# 설정 파일 열기
sudo vim /etc/ssh/sshd_config

# Root 로그인 차단
# PermitRootLogin no (반드시 일반 유저 생성 후 sudo를 사용)
# 비밀번호 로그인 차단 (SSH Key 방식 사용 시)
# PasswordAuthentication no
# (선택) 포트 변경: Port 22
# Port 2222 등으로 변경 (스캔 공격 회피용)
```

```zsh
# SSH 서비스 재시작
sudo systemctl restart ssh
```

<br />
<br />
<br />

4. Fail2Ban 설치 (해킹 방어)

```
로그인 실패가 반복되면 해당 IP를 자동으로 차단해 주는 도구이다.
(서버라면 설치하는 것이 정신건강에 좋음)
```

```zsh
# 설치만 해도 기본 설정(SSH 5회 실패 시 10분 차단 등)이 적용되어 바로 작동한다.
sudo apt install fail2ban -y
```

<br />
<br />
<br />

5. 스왑(Swap) 메모리 설정 (RAM이 작다면)

```
만약 서버 램이 1GB~2GB 정도로 작다면,
메모리 부족으로 서버가 뻗는 것을 막기 위해 스왑 파일을 만들어주는 것이 좋다.
```

```zsh
# 2GB 스왑 파일 생성 예시
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile

# 재부팅 후에도 유지되도록 fstab 등록
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

<br />
<br />
<br />

6. Zsh 설치

```zsh
sudo apt update

sudo apt install git curl zsh -y # git, curl, zsh 설치
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)" # Oh My Zsh 설치
```

```
설치가 끝나면 프롬프트 모양이 바뀐다.

만약 바뀌지 않았다면 로그아웃 후 다시 로그인하거나 아래 명령어를 입력한다.

zsh # zsh로 바로 진입

echo $SHELL # 현재 쉘이 zsh인지 확인
```

```zsh
# 자동 완성과 명령어 하이라이팅 기능을 제공하는 플러그인 2개

# zsh-autosuggestions (이전에 쳤던 명령어 희미하게 추천)
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# zsh-syntax-highlighting (명령어 오타 나면 빨간색으로 표시)
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

# 설정 파일 적용
vi ~/.zshrc # .zshrc 파일을 열어서 플러그인 목록에 추가해야 한다.

# 파일 내용 중 plugins=(git) 이라고 되어 있는 부분을 찾아서 아래처럼 괄호 안에 추가한다.
# 수정 전
plugins=(git)

# 수정 후
plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
)

source ~/.zshrc # 변경 사항 반영
```
