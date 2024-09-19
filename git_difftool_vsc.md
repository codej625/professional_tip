# Git difftool을 VSC으로 변경하는 방법.

<br /><br /><br />

1. Command line 에서 code --help 가 동작하는지 확인.

<br /><br />

2. 동작하지 않을 시 

```
맥 기준
VSC를 실행시키고 ⇧⌘P를 누른 후 Shell Command: Install ‘code’ command in PATH를 선택
```

```
윈도우 기준
“Add to PATH”를 선택 or PowerShell을 열고 ~/.gitconfig 입력
```

<br /><br />

3. Command line 에서 다음을 입력
```
git config --global core.editor "code --wait"
```
```
이제 다음과 같이 입력하면 VSC에서 Git config를 사용할 수 있다.
git config --global -e
```

<br /><br />

4. Git configuration
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
