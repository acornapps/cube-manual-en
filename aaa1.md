# Cocktail Installation to Minikube on macos

MacOS에서 Minikube를 이용하여 Cocktail이 제공하는 기능을 간단하게 확인하고자 할 경우에 아래의 절차를 따라 설치한다.

### **사전준비**

설치 전 아래와 같은 프로그램들이 미리 설치 되어 있어야 하며 설치 되어 있지 않을 경우 에러 메지가 발생한다.

\(에러 메시지에대한 해결책은 문서 마지막 TroubleShooting 에서 확인할 수 있다.\)

1\) Docker 다운로드 후 설치

[https://store.docker.com/editions/community/docker-ce-desktop-mac](https://store.docker.com/editions/community/docker-ce-desktop-mac)

2\) Virtualbox 다운로드 \(5.1.x version\)

[https://www.virtualbox.org/wiki/Download\_Old\_Builds\_5\_1](https://www.virtualbox.org/wiki/Download_Old_Builds_5_1)

3\) kubectl 다운로드

[https://kubernetes.io/docs/tasks/tools/install-kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl)

4\) minikube 다운로드

[https://github.com/kubernetes/minikube/releases](https://github.com/kubernetes/minikube/releases)

#### ㅤ

**1.먼저 설치를 위해 빈 디렉토리를 만든 후 해당 디렉토리로 이동한다**

```
# mkdir /Desktop/minikube
# cd /Desktop/minikube
```

**2.cube 명령을 이용하여 virtualbox용 설치 script를 download 받고 초기화 한다.**

```
# cube init -p minikube
```

**3.cube.yam 파일을 편집기로 열어서 설치하고자 하는 VM 정보를 기입한다. 아래는 예시임.**

```
---
cloud_provider: "minikube"

# (required) vCpu number    -> 노드 CPU 사양 
cpus: 2
# (required) Memory size (MB) -> 노드 Memory 사양 
memory: 4096
# (required) Hyper-V switch name for initializing vm instance. -> Hyper-V 스위치 명. default값 "ExternalSwitch"
hyperv_switch_name: "ExternalSwitch"
# Kubernetes
k8s_version: "1.8.0"
# (required) cocktail service
cocktail: true
```

hyperv-_switch-name는 windows에서 설치를 할 경우, hyperv 옵션이다.\(실행시 스킵\)_

**4.cube deploy 명령을 이용하여 실제 VM에 cocktail을 설치한다. -v debug옵션을 주면 설치되는 세부 내용을 확인할 수 있다.**

```
# cube deploy [-v debug]
```

**5.오류없이 설치가 완료되면 자동으로 browser가 기동되어 k8s dashboard로 접속하게 된다.**

* 만약 browser가 뜨지 않을 경우 터미널에서 다음 명령어를 수행하여 k8s dashboard를 열어준다.

```
# minikube dashboard
```

고급 link를 클릭하고 아래 이동 버튼을 클릭한다.

사용자이름과 비밀번호를 입력하면 k8s dashboard로 접속할 수 있다.

**6.cocktail cloud 접속 url 확인.**

해당 명령을 이용하여 cocktail 홈페이지 url과 port를 확인 할 수 있다.

```
# minikube service list -n cocktail-system | grep cocktail-client-node-port
| cocktail-system | cocktail-client-node-port | http://192.168.99.100:31203 |
```

Namespace를 cocktail-system으로 선택하고 Services메뉴에서 cocktail-client-node-port를 선택한다.

[http://192.168.99.100:31203이](http://192.168.99.100:31203이) cocktail cloud 접속 URL임을 확인 할 수 있다.

**7.cocktail cloud.**

브라우저로 [http://{VM의](http://{VM의) masterip}:{cocktail client node port}으로 접속하면 cocktail login 화면으로 접속할 수 있다. \(User Id, Password는 별도 문의\).

![](/assets/cocktail_login.jpeg)

### **삭제**

**1.삭제는  생성된  VM을 삭제만 하거나,  script를 모두 정리하고 cube.yaml파일을 백업할 수 있다.**  
  디폴트로 옵션을 주지 않는 경우에는 생성된 VM을 삭제만 하거나,  -f 옵션을 추가하여 생성된 VM을 삭제하고 cube.yaml 파일을 cube.yaml.org로 백업파일을 생성한 후 설치스크립트도 모두 삭제할 수 있다.

따라서, 이 명령어는 주의해서 실행해야 한다.

```
# cd /Desktop/cubetest

// k8s만 삭제하고 설치 스크립트는 그대로 유지 
# cube destroy

// k8s와 설치 스크립트를 삭제하고 cube.yaml을 cube.yaml.org파일로 백업함. 
# cube destroy -f
```

-f 옵션으로 삭제한 후에 다시 설치하기 위해서 cube init 명령을 통해 설치 스크립트를 다시 다운로드 받은 후 설치하면 된다.

