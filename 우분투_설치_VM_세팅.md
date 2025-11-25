# VM에 Ubuntu를 Host OS로 사용하기

<br />
<br />

* Ubuntu를 Host OS로 사용하는 이유

---

```
설치 예시로 사용할 Ubuntu 24.04 LTS(Long Term Support)는
5년간 지원을 제공하는 Ubuntu의 최신 LTS 버전으로, 안정적이고 보안이 강화된 서버 운영체제를 제공한다.

Ubuntu Server는 네트워크 및 서비스 맞춤 Linux 배포판이다.

GUI가 없어서 가볍고 용량이 작다는 장점이 있다.
```

<br />
<br />
<br />
<br />

1. VM에 ios 이미지를 마운트하고 순서대로 운영체제를 설치한다. (설치 과정 생략)

```zsh
# 운영체제 설치 완료 시 아래를 차례대로 실행

# 최신 패키지와 보안 업데이트를 설치
sudo apt update && sudo apt upgrade -y

# OpenSSH 서버
sudo apt install openssh-server -y

# 한글 입력기 설치
sudo apt install ibus-hangul
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

<br />

`sudo vim /etc/ssh/sshd_config`

```zsh
# Root 로그인 차단
PermitRootLogin no (반드시 일반 유저 생성 후 sudo를 사용)

# 비밀번호 로그인 차단 (SSH Key 방식 사용 시)
PasswordAuthentication no

# (선택) 포트 변경: Port 22
Port 2222 등으로 변경 (스캔 공격 회피용)
```

```zsh
# 문법 오류 검사 (아무 내용 안 나오면 정상)
sudo sshd -t

# SSH 서비스 재시작
sudo systemctl restart ssh

# 포트 지정 접속 테스트 (새 터미널에서 확인)
ssh -p 2222 계정명@서버IP
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

# git, curl, zsh 설치
sudo apt install git curl zsh -y

# Zsh를 현재 사용자의 기본 쉘로 변경
chsh -s $(which zsh)

# Oh My Zsh 설치
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

```zsh
# 현재 쉘이 zsh인지 확인
echo $SHELL
```

```zsh
# 자동 완성과 명령어 하이라이팅 기능을 제공하는 플러그인 2개

# zsh-autosuggestions (이전에 쳤던 명령어 희미하게 추천)
git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions

# zsh-syntax-highlighting (명령어 오타 나면 빨간색으로 표시)
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

<br />

`vi ~/.zshrc # 설정 파일 적용`

```vim
# 파일 내용 중 plugins=(git) 이라고 되어 있는 부분을 찾아서 아래처럼 괄호 안에 추가한다.

# 수정 전
plugins=(git)

# 수정 후
plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
)
```

```zsh
# 변경 사항 반영
source ~/.zshrc
```

<br />
<br />
<br />

7. 아이피 고정

```
마지막으로,
아이피의 변경을 막기 위해 아이피를 고정해야 한다.
```

<br />

`sudo vim /etc/netplan/50-cloud-init.yaml`

```yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: no # DHCP를 사용하지 않고 고정 IP를 사용하도록 지정
      addresses: [192.168.0.201/24] # 이 VM의 고정 IP 주소는 **192.168.0.201**이고, 서브넷 마스크는 /24이다.
      routes:
        - to: default
          via: 192.168.0.1 # 외부로 나가는 모든 트래픽의 기본 경로(게이트웨이)는 **192.168.0.1**로 지정.
      nameservers:
        addresses: [8.8.8.8, 168.126.63.1]
```

```zsh
# 적용 확인을 위해 명령어 입력
sudo netplan apply

# eth0 인터페이스에 192.168.0.201/24 주소가 표시되는지 확인
ip a

# 게이트웨이와 DNS 설정이 올바르게 작동하여 외부 인터넷에 접속할 수 있어야 한다.
ping 8.8.8.8

# 모든 VM이 서로 통신할 수 있어야 한다. (예를 들어, k3s-master-2에서 k3s-master-1으로 핑 테스트를 필요)
ping 192.168.0.200
```
