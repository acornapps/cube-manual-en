# Cocktail Installation on windows

Windows에서 minikube를 이용하여 Cocktail을 설치하고자 할 경우에 아래의 절차를 따라 설치한다.

설치 전 아래와 같은 프로그램들이 미리 설치되어 있어야 한다. 설치되어 있지 않을 경우 설치

1\) Docker

[https://store.docker.com/editions/community/docker-ce-desktop-windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)

2\) Hyper-v

3\) kubectl.exe

4\) minikube.exe

1.먼저 설치를 위해 CMD창에서 빈 디렉토리를 만든 후 해당 디렉토리로 이동한다.\(CMD는 반드시 관리자 모드로 실행한다.\)

```
# mkdir /tmp/cubetest
# cd /tmp/cubetest
```

2.cube 명령을 이용하여 minikube용 설치 script를 download 받고 초기화 한다. \(cube init --help 명령어를 통해 제공하는 프로바이더들을 확인할 수 있다.\)

```
# cube init -p minikube
```

3.생성된 cube.yaml파일을 열어 필요한 정보를 입력 한다.

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

1. cube 명령어를 이용하여 칵테일을 설치한다.

```
# cube deploy -v debug
```

3.생성된 c

```
# cube init -p minikube
```

3.생성된 c

cube.exe 환경변수로 잡아 어디 위치에서든 실행시킬 수 있도록 한다.

1. 작업할 폴더에서 cube init -p minikube 명령어 입력
2. 도커 설치 안됨
3. 도커 다운로드 후 도커 실행하면 하이퍼v 필요하다며 윈도우 재부팅
4. 다시 cube init -p minikube하면 kubectl 필요
5. kubectl다운로드 후 path잡아줌
6. minikube필요 다운로드 후 path잡아줌. minikube-windows-amd64.exe 파일이름 minikube.exe 로 수

**docker**

C:\Users\acornsoft\minitest&gt;cube init -p minikube

Current Working directory : C:\Users\acornsoft\minitest

Checking pre-requisition \[windows\]

exec: "docker": executable file not found in %PATH%

kubectl is not found. please install kubectl before proceeding

Visit [https://store.docker.com/editions/community/docker-ce-desktop-windows](https://www.gitbook.com/book/acornsoft/cube-manual/edit#)

**kubectl**

C:\Users\acornsoft\minitest&gt;cube init -p minikube

Current Working directory : C:\Users\acornsoft\minitest

Checking pre-requisition \[windows\]

exec: "kubectl": executable file not found in %PATH%

kubectl is not found. please install kubectl before proceeding

Visit [https://kubernetes.io/docs/tasks/tools/install-kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl)

**minikube**

C:\Users\acornsoft\minitest&gt;cube init -p minikube

Current Working directory : C:\Users\acornsoft\minitest

Checking pre-requisition \[windows\]

exec: "minikube": executable file not found in %PATH%

minikube is not found. please install minikube before proceeding

Visit [https://github.com/kubernetes/minikube/releases](https://github.com/kubernetes/minikube/releases)

cube.yaml 열면

---

cloud\_provider: "minikube"

\# \(required\) vCpu number

cpus: 2

\# \(required\) Memory size \(MB\)

memory: 4096

\# \(required\) Hyper-V switch name for initializing vm instance.

hyperv\_switch\_name: "ExternalSwitch"

\# Kubernetes

k8s\_version: "1.8.0"

\# \(required\) cocktail service

cocktail: true

\# \(required\) if cocktail service true

builder\_server\_type:

builder\_ca\_pem:

builder\_cert\_pem:

builder\_key\_pem:

.

.

.

**hyper-v error**

C:\Users\acornsoft\minitest&gt;cube deploy -v debug

Start provisioning for cloud infrastructure \[minikube\]

\[37mDEBU\[0m\[0000\] minikube \[start --vm-driver hyperv --cpus 2 --memory 4096 --hyperv-virtual-switch ExternalSwitch --kubernetes-version v1.8.0\]

\[37mDEBU\[0m\[0000\] Starting local Kubernetes v1.8.0 cluster...

\[37mDEBU\[0m\[0000\] Starting VM...

\[37mDEBU\[0m\[0000\] Downloading Minikube ISO

139.09 MB / 139.09 MB  100.00% 0s

DEBU\[0054\] ================================================================================

DEBU\[0054\] An error has occurred. Would you like to opt in to sending anonymized crash

DEBU\[0054\] information to minikube to help prevent future errors?

DEBU\[0054\] To opt out of these messages, run the command:

DEBU\[0054\]      minikube config set WantReportErrorPrompt false

DEBU\[0054\] ================================================================================

E1130 13:47:42.242545   13912 start.go:150\] Error starting host: Error creating host: Error executing step: Running precreate checks.

: Hyper-v commands have to be run as an Administrator.

Retrying.

E1130 13:47:42.243493   13912 start.go:156\] Error starting host:  Error creating host: Error executing step: Running precreate checks.

: Hyper-v commands have to be run as an Administrator

.

.

.

hyper-v externalswitch not found

C:\Users\acornsoft\minitest&gt;cube deploy -v debug

Start provisioning for cloud infrastructure \[minikube\]

\[37mDEBU\[0m\[0000\] minikube \[start --vm-driver hyperv --cpus 2 --memory 4096 --hyperv-virtual-switch ExternalSwitch --kubernetes-version v1.8.0\]

\[37mDEBU\[0m\[0000\] Starting local Kubernetes v1.8.0 cluster...

\[37mDEBU\[0m\[0000\] Starting VM...

DEBU\[0006\] ================================================================================

DEBU\[0006\] An error has occurred. Would you like to opt in to sending anonymized crash

DEBU\[0006\] information to minikube to help prevent future errors?

DEBU\[0006\] To opt out of these messages, run the command:

DEBU\[0006\]      minikube config set WantReportErrorPrompt false

DEBU\[0006\] ================================================================================

E1130 13:54:26.070118   11236 start.go:150\] Error starting host: Error creating host: Error executing step: Running precreate checks.

: vswitch "ExternalSwitch" not found.

Retrying.

E1130 13:54:26.071982   11236 start.go:156\] Error starting host:  Error creating host: Error executing step: Running precreate checks.

: vswitch "ExternalSwitch" not found

.

.

.

**creating VM error -자원부족**

C:\Users\acornsoft\minitest&gt;cube deploy -v debug

Start provisioning for cloud infrastructure \[minikube\]

\[37mDEBU\[0m\[0000\] minikube \[start --vm-driver hyperv --cpus 2 --memory 4096 --hyperv-virtual-switch ExternalSwitch --kubernetes-version v1.8.0\]

\[37mDEBU\[0m\[0000\] Starting local Kubernetes v1.8.0 cluster...

\[37mDEBU\[0m\[0000\] Starting VM...

DEBU\[0072\] ================================================================================

E1130 14:12:17.468288    6000 start.go:150\] Error starting host: Error creating host: Error executing step: Creating VM.

: exit status 1.

Retrying.

E1130 14:12:17.470160    6000 start.go:156\] Error starting host:  Error creating host: Error executing step: Creating VM.

: exit status 1

.

.

.

**json.error**

C:\Users\acornsoft\minitest&gt;cube deploy -v debug

Start provisioning for cloud infrastructure \[minikube\]

\[37mDEBU\[0m\[0000\] minikube \[start --vm-driver hyperv --cpus 2 --memory 4096 --hyperv-virtual-switch ExternalSwitch --kubernetes-version v1.8.0\]

\[37mDEBU\[0m\[0003\] Starting local Kubernetes v1.8.0 cluster...

\[37mDEBU\[0m\[0003\] Starting VM...

\[37mDEBU\[0m\[0003\] ================================================================================

\[37mDEBU\[0m\[0003\] An error has occurred. Would you like to opt in to sending anonymized crash

\[37mDEBU\[0m\[0003\] information to minikube to help prevent future errors?

\[37mDEBU\[0m\[0003\] To opt out of these messages, run the command:

\[37mDEBU\[0m\[0003\]     minikube config set WantReportErrorPrompt false

\[37mDEBU\[0m\[0003\] ================================================================================

E1130 14:32:05.030589   14024 start.go:150\] Error starting host: Error loading existing host. Please try running \[minikube delete\], then run \[minikube start\] again.: Error loading host from store: open C:\Users\acornsoft.minikube\machines\minikube\config.json: The system cannot find the file specified..

Retrying.

E1130 14:32:05.035590   14024 start.go:156\] Error starting host:  Error loading existing host. Please try running \[minikube delete\], then run \[minikube start\] again.: Error loading host from store: open C:\Users\acornsoft.minikube\machines\minikube\config.json: The system cannot find the file specified.

