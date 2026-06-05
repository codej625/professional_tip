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

3. 초기 설정

```zsh
# 기본 세팅
pkg update && pkg upgrade -y
pkg install -y openssh
```

```zsh
# 비밀번호 설정
passwd
# New password: 원하는비밀번호 입력
# Retype new password: 재입력
```

```zsh
# sshd 실행
sshd
```
