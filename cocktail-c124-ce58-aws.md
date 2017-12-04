# Cocktail Installation on AWS

[Amazon Web Services Cloud](https://aws.amazon.com/free/)에 Cocktail를 설치하는 과정은 다음과 같다.

### **사전준비**

설치 전 아래와 같은 프로그램들이 미리 설치 되어 있어야 하며 설치 되어 있지 않을 경우 에러 메지가 발생한다.

\(에러 메시지에대한 해결책은 문서 마지막 TroubleShooting 에서 확인할 수 있다.\)

1\) Docker 다운로드 후 설치

[https://store.docker.com/editions/community/docker-ce-desktop-mac](https://store.docker.com/editions/community/docker-ce-desktop-mac)

AWS 계정 및 application 과 관련된 값들을 아래와 같이 조회해서 메모해 놓는다.

* TenantId : Home &gt; Azure Active Directory &gt; App registrations &gt; Endpoints &gt; OAUTH2.0 AUTHORIZATION에 포함된 값이 TenantId 임.

### **TroubleShooting**

**1.Docker 설치되어 있지 않은 경우**

```
MinHoui-MacBook-Pro:cubetest minhona$ cube init -p aws
Current Working directory : /Users/minhona/Desktop/cubetest
Checking pre-requisition [darwin]
exec: "docker": executable file not found in $PATH
docker is not found. please install docker before proceeding
Visit https://store.docker.com/editions/community/docker-ce-desktop-mac
```

다운로드 링크로 이동하여 Docker 설치 후 cube를 재실행 한다.

