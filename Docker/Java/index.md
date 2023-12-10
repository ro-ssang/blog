# Java 개발환경 설정

## openJDK 설치

```sh
# home brew로 openjdk 설치
brew install openjdk@17

# 바로가기 설정
sudo ln -sfn /opt/homebrew/opt/openjdk@17/libexec/openjdk.jdk /Library/Java/JavaVirtualMachines/openjdk-17.jdk

# openjdk 환경변수 설정
echo 'export PATH="/opt/homebrew/opt/openjdk@17/bin:$PATH"' >> ~/.zshrc

# zshrc 파일 읽어들이기
source ~/.zshrc

# java 버전 확인
java --version
```

## Visual Studio Code

Extenstions > "Extension Pack for Java development" 검색 > 설치

## Build Java Project Using Gradle

https://velog.io/@kjyeon1101/Spring-VScode-Gradle-%ED%99%98%EA%B2%BD-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0



https://start.spring.io/



## Docker

```sh
# openjdk 이미지 내려받기
docker pull openjdk
```

