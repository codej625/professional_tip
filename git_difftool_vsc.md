# Git difftool을 VSC으로 변경하는 방법.

<br />
<br />
<br />
<br />

* `VSC를 Difftool로 사용하기`
---

```
git의 기본 difftool은 가독성과 편리성 안 좋아서,
VSC를 difftool로 설정해서 사용해보자.
```

<br />
<br />
<br />
<br />

1. `터미널 or Git bash 실행 커맨드가 작동작하는지 확인`

```
code --help or code .
```

```
// 작동하지 않을 시

Shift + Command + P 키를 눌러 Command Palette를 연다.

입력 창에 Shell Command를 입력하고,
나타나는 옵션 중 Shell Command: Install 'code' command in PATH를 선택한다.
```

<br />
<br />
<br />

2. `Command line 에서 다음을 입력`

```
git config --global core.editor "code --wait"
```

```
이제 다음과 같이 입력하면 VSC에서 Git Config를 설정할 수 있다.
git config --global -e
```

<br />
<br />
<br />

3. `Git configuration`
```
[diff]
    tool = default-difftool
[difftool "default-difftool"]
    cmd = code --wait --diff $LOCAL $REMOTE

# 선택 사항(머지 툴로 사용 가능)
[merge]
    tool = vscode
[mergetool "vscode"]
    cmd = code --wait $MERGED
```
