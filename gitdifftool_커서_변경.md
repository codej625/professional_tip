# Git difftool을 Cursor로 변경하는 방법

<br />
<br />
<br />
<br />

* `Cursor를 Difftool로 사용하기`
---

```
git의 기본 difftool은 가독성과 편리성이 안 좋아서,
Cursor를 difftool로 설정해서 사용해보자.

*로컬 환경(Mac/Windows)과 리눅스 서버 환경 모두 동일하게 적용 가능
```

<br />
<br />
<br />
<br />

1. `터미널에서 Cursor 명령어가 작동하는지 확인`

```zsh
cursor --version
# 또는
cursor --help
```

```
// 작동하지 않을 시

Cursor가 PATH에 등록되어 있는지 확인
which cursor
```

<br />
<br />
<br />

2. `Git configuration`

```zsh
git config --global diff.tool cursor
git config --global difftool.cursor.cmd 'cursor --wait --diff $LOCAL $REMOTE'
git config --global difftool.prompt true
```


```zsh
# 또는 Git config 파일에 직접 추가
git config --global -e
```


```
# 다음 내용 추가

[diff]
    tool = cursor
[difftool "cursor"]
    cmd = cursor --wait --diff $LOCAL $REMOTE
    prompt = true
```

<br />
<br />
<br />

3. `사용 방법`

```zsh
# 모든 변경된 파일 비교 (각 파일마다 Y/n 프롬프트)
git difftool

# 특정 파일만 비교
git difftool <파일명>

# 프롬프트 없이 모든 파일 자동으로 열기
git difftool --no-prompt
# 또는
git difftool -y
```

<br />
<br />
<br />

4. 만약 "file was not found" 에러가 발생하면, wrapper 스크립트를 사용

```bash
# ~/bin/cursor-diff.sh 생성 (절대 경로 변환용)
mkdir -p ~/bin
cat > ~/bin/cursor-diff.sh << 'EOF'
#!/bin/bash
LOCAL_ABS=$(readlink -f "$1" 2>/dev/null || realpath "$1" 2>/dev/null || echo "$1")
REMOTE_ABS=$(readlink -f "$2" 2>/dev/null || realpath "$2" 2>/dev/null || echo "$2")
exec cursor --wait --diff "$LOCAL_ABS" "$REMOTE_ABS"
EOF
chmod +x ~/bin/cursor-diff.sh

# Git 설정 변경
git config --global difftool.cursor.cmd '$HOME/bin/cursor-diff.sh "$LOCAL" "$REMOTE"'
```

