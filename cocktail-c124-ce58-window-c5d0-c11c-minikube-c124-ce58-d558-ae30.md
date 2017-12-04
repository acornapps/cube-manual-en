# Cocktail Installation on Minikube\(windows\)

Windows에서 minikube를 이용하여 Cocktail을 설치하고자 할 경우 아래의 절차에 따라 설치한다.

### **사전준비**

설치 전 아래와 같은 프로그램들이 미리 설치 되어 있어야 하며 설치 되어 있지 않을 경우 에러 메시지가 발생한다.

\(에러 메시지에대한 해결책은 문서 마지막 TroubleShooting 에서 확인할 수 있다.\)

1\) Docker 다운로드 후 설치

[https://store.docker.com/editions/community/docker-ce-desktop-windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)

2\) Hyper-v : \[제어판\] - \[windows 기능 겨기/끄기\] 에서 \[Hyper-V\] 기능을 활성화

3\) kubectl.exe 다운로드 후 환경설정 path설정

[https://kubernetes.io/docs/tasks/tools/install-kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl)

4\) minikube.exe 다운로드 후 환경설정 path설정

[https://github.com/kubernetes/minikube/releases](https://github.com/kubernetes/minikube/releases)

#### 

### **설치 **

1.먼저 설치를 위해 CMD창에서 빈 디렉토리를 만든 후 해당 디렉토리로 이동한다.\(**CMD는 반드시 관리자 모드로 실행한다.**\)

```
# mkdir /tmp/cubetest
# cd /tmp/cubetest
```

2.cube 명령을 이용하여 minikube용 설치 script를 download 받고 초기화 한다. \(cube init --help 명령어를 통해 제공하는 프로바이더들을 확인할 수 있다.\)

```
# cube init -p minikube
```

3.정상적으로 script가 다운로드 되면 해당 폴더에 cube.yaml파일이 생성되고 파일을 열어 필요한 정보를 입력 한다.

```
cloud_provider: "minikube"
(required) vCpu number
cpus: 2
(required) Memory size (MB)
memory: 4096
(required) Hyper-V switch name for initializing vm instance.
hyperv_switch_name: "ExternalSwitch"
Kubernetes
k8s_version: "1.8.0"
(required) cocktail service
cocktail: true
(required) if cocktail service true
builder_server_type:
builder_ca_pem:
builder_cert_pem:
builder_key_pem:
```

4..cube deploy 명령을 이용하여 cocktail을 설치한다. -v debug옵션을 주면 설치되는 세부 내용을 확인할 수 있다.

```
# cube deploy [-v debug]
```

ㅤ

5.오류없이 설치가 완료되면 자동으로 browser가 기동되어 k8s dashboard로 접속하게 된다.

ㅤ

### **TroubleShooting**

**1.도커가 설치되어 있지 않은 경우**

```
C:\Users\acornsoft\minitest>cube init -p minikube
Current Working directory : C:\Users\acornsoft\minitest
Checking pre-requisition [windows]
exec: "docker": executable file not found in %PATH%
kubectl is not found. please install kubectl before proceeding
Visit 
https://store.docker.com/editions/community/docker-ce-desktop-windows
```

다운로드 경로로 이동하여 도커 설치 후 도커 실행하면 아래와 같은 알림창이 뜬다. Hyper-v 기능을 켤 것인지 물어보는 알림창으로

ok 를 눌러 Hyper-v 활성화 시킨다.

![](/assets/도커하이퍼v.png)

#### ㅤ

**2.kubectl.exe 파일이 없는 경우 **

```
C:\Users\acornsoft\minitest>cube init -p minikube
Current Working directory : C:\Users\acornsoft\minitest
Checking pre-requisition [windows]
exec: "kubectl": executable file not found in %PATH%
kubectl is not found. please install kubectl before proceeding
Visit https://kubernetes.io/docs/tasks/tools/install-kubectl
```

다운로드 경로로 이동하여 아래 그림과 같이 파일 다운로드 후 환경변수 path를 설정한다.![](/assets/kubectl다운로드1.PNG)  
ㅤㅤ

**3.minikube.exe 파일이 없을 경우**

```
C:\Users\acornsoft\minitest>cube init -p minikube
Current Working directory : C:\Users\acornsoft\minitest
Checking pre-requisition [windows]
exec: "minikube": executable file not found in %PATH%
minikube is not found. please install minikube before proceeding
Visit 
https://github.com/kubernetes/minikube/releases
```

다운로드 경로로 이동하여 아래와 같이 파일 다운로드 후 minikube.exe로 파일이름을 수정한다. 이후 환경변수 path를 설정한다.![](/assets/minikube다운로드.PNG)

#### ㅤ

4.**hyper-v 에 가상 스위치가 설정되어있지 않은경우 **

```
E1130 13:54:26.070118   11236 start.go:150] Error starting host: Error creating host: Error executing step: Running precreate checks.
: vswitch "ExternalSwitch" not found.
Retrying.
E1130 13:54:26.071982   11236 start.go:156] Error starting host:  Error creating host: Error executing step: Running precreate checks.
: vswitch "ExternalSwitch" not found
```

\[Hyper-V 관리자\] -ㅤ \[가상스위치관리자\] - \[가상스위치만들기\] 를 선택한다. ![](/assets/가상네트워크생성1.png)

가상 스위치 이름을 \[ExternalSwitch\]로 설정하고 연결형식을 외부네트워크로 설정한다.  
![](/assets/가상네트워크생성2.PNG)

#### ㅤ

**5. cube deploy -v debug 명령어 실행 시 윈도우 리소스가 부족한 경우 **

```
E1130 14:12:17.468288    6000 start.go:150] Error starting host: Error creating host: Error executing step: Creating VM.
: exit status 1.
```

cocktail 설치에 필요한 리스가 부족한 경우 발생하는 에러메시지로 설치 pc 사양 업그레이드가 필요하다.

ㅤ

**6.cube deploy -v debug 명령어 실행 시 minikube에 config.json 파일을 찾지 못할경우 **

```
E1130 14:32:05.030589   14024 start.go:150] Error starting host: Error loading existing host. Please try running [minikube delete], then run [minikube start] again.: Error loading host from store: open C:\Users\acornsoft\.minikube\machines\minikube\config.json: The system cannot find the file specified..
```

.minikube 폴더를 완전히 삭제 후 다시 설치를 시도한다.

