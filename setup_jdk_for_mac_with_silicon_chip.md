# 실리콘 칩셋이 탑재 된 맥의 jdk(+환경변수)를 셋팅해 보자!

<br /><br />

```
* 여기서는 brew, zsh, jdk, 환경변수 설정을 다룬다.
```

<br /><br /><br />

1. zsh를 설치하기 위해 brew를 설치한다. (zsh는 자바가 설치되어 있지 않으면 설치되지 않는데, 자바를 쉽게 설치하려면 zsh가 필요하다.)

```
// terminal에 밑에 script를 입력한다.

/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

<br /><br />

2. brew가 설치 됬다면, zsh를 설치한다.

```
// 1) zsh 설치

brew install zsh
```

<br />

```
// 2) oh-my-zsh(확장팩) 설치

sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

<br /><br />

3. JDK 설치.

```
// openjdk11 버전을 설치

https://www.azul.com/downloads/?version=java-11-lts&os=macos&architecture=arm-64-bit&package=jdk#zulu
```

<br />

```
// JDK를 삭제하고 싶다면 밑에 url을 참고

https://docs.azul.com/core/uninstall/macos
```

<br />

```
terminal에 "java --version"을 실행하면 자바 버전이 출력된다.

하지만 echo $JAVA_HOME를 실행하면 환경변수가 나오지 않는다.

즉, 환경변수를 설정해 줘야 한다.
```

<br /><br />

5. JDK가 설치 된 경로를 찾아야 한다. (zsh쉘을 기준)

```
// 밑에 경로로 이동하면 JDK를 확인할 수 있다.

cd /Library/Java/JavaVirtualMachines
```

<br />

```
// 이제 ls와 cd를 통해 밑에와 같이 Home의 경로를 찾으면 된다.

/Library/Java/JavaVirtualMachines/zulu-11.jdk/Contents/Home (JDK 11)
/Library/Java/JavaVirtualMachines/zulu-17.jdk/Contents/Home (JDK 17)
```

<br /><br />

5. 환경변수 설정

```
// 환경변수 파일을 연다.

vim ~/.zshrc
```

<br />

```
// "i"를 눌러 Insert 모드로 전환 후 가장 아래에 밑과 같이 입력한다.(위에서 찾은 경로를 입력)

# Set JDK
export JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-11.jdk/Contents/Home
export PATH=${PATH}:$JAVA_HOME/bin
```

<br />

```
// "esc"를 누르고 :wq를 입력 후 빠져나온다. 변경 사항을 적용하기 위해 밑에 스크립트를 입력한다.

source ~/.zshrc
```

<br />

```
// 최종 확인

echo $JAVA_HOME
java -version
```
