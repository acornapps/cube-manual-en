# Cocktail Installation on windows

Windows에서 minikube를 이용하여 Cocktail을 설치하고자 할 경우에 아래의 절차를 따라 설치한다.

설치 전 아래와 같은 프로그램들이 미리 설치 되어 있어야 한다. 설치 되어 있지 않을 경우 에러메세지가 발생하며 문서 마지막에 TroubleShooting 에서 해결책을 확인할 수 있다.

1\) Docker 다운로드 [https://store.docker.com/editions/community/docker-ce-desktop-windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)

2\) Hyper-v : \[제어판\] - \[windows 기능 겨기/끄기\] 에서 \[Hyper-V\] 기능을 켜주면 된다.

3\) kubectl.exe 다운로드 [https://kubernetes.io/docs/tasks/tools/install-kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl)

4\) minikube.exe 다운로드 [https://github.com/kubernetes/minikube/releases](https://github.com/kubernetes/minikube/releases)

1.먼저 설치를 위해 CMD창에서 빈 디렉토리를 만든 후 해당 디렉토리로 이동한다.\(CMD는 반드시 관리자 모드로 실행한다.\)

```
# mkdir /tmp/cubetest
# cd /tmp/cubetest
```

2.cube 명령을 이용하여 minikube용 설치 script를 download 받고 초기화 한다. \(cube init --help 명령어를 통해 제공하는 프로바이더들을 확인할 수 있다.\)

```
# cube init -p minikube
```

3.정상적으로 script가 다운로드 받아지면 해당 폴더에 cube.yaml파일이 생성되고 파일을 열어 필요한 정보를 입력 한다.

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

4.cube 명령어를 이용하여 칵테일을 설치한다.

```
# cube deploy -v debug
```

**TroubleShooting**

**1.도커가 설치되어 있지 않은 경우 아래와 같은 에러메시지가 발생하며 도커 다운로드 경로로 이동하여 도커를 설치한다.**

```
C:\Users\acornsoft\minitest>cube init -p minikube
Current Working directory : C:\Users\acornsoft\minitest
Checking pre-requisition [windows]
exec: "docker": executable file not found in %PATH%
kubectl is not found. please install kubectl before proceeding
Visit 
https://store.docker.com/editions/community/docker-ce-desktop-windows
```

도커 설치 후 시작하면 아래와 같은 알림창이 뜨며 Hyper-v 기능을 켤 것인지 물어보고, ok 를 눌러 Hyper-v 활성화 시킨다.

![](/assets/도커하이퍼v.png)

**2.kubectl.exe 파일이 없을 경우 아래와 같은 메시지가 발생한다.**

```
C:\Users\acornsoft\minitest>cube init -p minikube
Current Working directory : C:\Users\acornsoft\minitest
Checking pre-requisition [windows]
exec: "kubectl": executable file not found in %PATH%
kubectl is not found. please install kubectl before proceeding
Visit https://kubernetes.io/docs/tasks/tools/install-kubectl
```

다운로드 경로로 이동하여 아래 그림과 같이 파일 다운로드 후 환경변수 path를 설정한다.![](/assets/kubectl다운로드1.PNG)

**3.minikube.exe 파일이 없을 경우 아래와 같은 메시지가 발생한다.**

```
C:\Users\acornsoft\minitest>cube init -p minikube
Current Working directory : C:\Users\acornsoft\minitest
Checking pre-requisition [windows]
exec: "minikube": executable file not found in %PATH%
minikube is not found. please install minikube before proceeding
Visit 
https://github.com/kubernetes/minikube/releases
```

다운로드 경로로 이동하여 아래와같파일 다운로드 후 minikube.exe로 파일이름을수정한다.이후환경변수 path를 설정한다.![](/assets/minikube다운로드.PNG)





4.**hyper-v 에 가상 스위치가 없을경우 발생 하는 에러메시지 **

```
E1130 13:54:26.070118   11236 start.go:150] Error starting host: Error creating host: Error executing step: Running precreate checks.
: vswitch "ExternalSwitch" not found.
Retrying.
E1130 13:54:26.071982   11236 start.go:156] Error starting host:  Error creating host: Error executing step: Running precreate checks.
: vswitch "ExternalSwitch" not found
```



**5.CMD 가 관리자 모드로 행되지 않을 경우**

```
E1130 13:54:26.070118   11236 start.go:150] Error starting host: Error creating host: Error executing step: Running precreate checks.
: vswitch "ExternalSwitch" not found.
```

  


6.**CMD 가 관리자 모드로 행되지 않을 경우**

```
E1130 13:54:26.070118   11236 start.go:150] Error starting host: Error creating host: Error executing step: Running precreate checks.
: vswitch "ExternalSwitch" not found.
```

  
7.**CMD 가 관리자 모드로 행되지 않을 경우**

```
E1130 14:12:17.468288    6000 start.go:150] Error starting host: Error creating host: Error executing step: Creating VM.
: exit status 1.
```



8.**CMD 가 관리자 모드로 행되지 않을 경우**

```
E1130 14:32:05.030589   14024 start.go:150] Error starting host: Error loading existing host. Please try running [minikube delete], then run [minikube start] again.: Error loading host from store: open C:\Users\acornsoft.minikube\machines\minikube\config.json: The system cannot find the file specified..
```



