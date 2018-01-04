# Cocktail Installation to AWS on macos

[Amazon Web Services Cloud](https://aws.amazon.com/free/)에 Cocktail를 설치하는 과정은 다음과 같다.

### **사전준비**

설치 전 아래와 같은 프로그램들이 미리 설치 되어 있어야 하며 설치 되어 있지 않을 경우 에러 메시지가 발생한다.

1\) 제공받은 cube 바이너리를 어느 디렉토리에서든 사용할 수 있도록 환경변수 path 설정을 한다.

2\) Docker 다운로드 후 설치

[https://store.docker.com/editions/community/docker-ce-desktop-windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)

3\) AWS 설치를 위해서는 Access Key와 Secret Key가 필요하니 아래와 같이 조회하여 메모해 놓는다.

* **Access Key와 Secret Key를 발급받기 위해 AWS 서비스 콘솔에서 상단 우측 카테고리에서 'Security Credentials'를 클릭합니다.**![](/assets/aws-add.png)

* **첫 화면에서 아래와 같은 경고창을 만나는데 IAM User를 생성할 것인지에 대한 내용이고, 여기서는 왼쪽 Continue to Security Credentials 버튼을 눌러 계속 진행합니다.**![](/assets/aws-add1.png)

* **이후 화면에서 Access Keys\(Access Key ID and Secret Access Key\) 를 선택 후 Create New Access Key를 눌러 키를 발급 받는다.**![](/assets/aws-add2.png)

* **키가 생성되면 아래와 같은 창을 볼 수 있고, show Access Key를 통해 Access Key와 Credit Key를 확인할 수 있습니다. 또한 Download Key File을 눌러 키를 저장할 수 있습니다.**![](/assets/aws-add3.png)![](/assets/aws-add4.png)

### 설치

**1.먼저 설치를 위해 빈 디렉토리를 만든 후 해당 디렉토리로 이동한다**

```
# mkdir /Desktop/cubetest
# cd /Desktop/cubetest
```

**2.cube 명령을 이용하여 aws용 설치 script를 download 받고 초기화 한다.**

```
# cube init -p aws
```

3**.cube.yaml 파일을 열어서 설치하고자 하는 VM 정보를 기입한다. 아래는 master 1ea, worker 2ea 로 구성하는 예임.**

```
---
cloud_provider: "aws"

## (required) When azure is used, you need to set the following variables.
access_key: "********************"

secret_key: "*****************************************"

# (optional) Instance size for the master node(s). Default: t2.medium.  -> AWS VM 사양 (마스터 노드) 
master_vm_size: "t2.medium"

# (optional) Instance size for the worker node(s). Default: t2.medium. -> AWS VM 사양 (워커 노드)
worker_vm_size: "t2.medium"

# (required) The number of master nodes to be created. Example: 2  -> 마스터 노드 수
master_node_count: 1

# (required) The number of worker nodes to be created. Example: 3  -> 워커 노드 수 
worker_node_count: 2

# (required) The region name. Example: ap-northeast-2  -> AWS region
region: "ap-northeast-1"

# (required) Path to an SSH private key file to access server.
private_key_path: "/Users/minhona/.ssh/id_rsa"

# (required) Path to an SSH public key file to be provisioned as the SSH key.
key_path: "/Users/minhona/.ssh/id_rsa.pub"

# Kubernetes
k8s_version: "1.6.7"
cluster_name: "cube"
domain_name: "acornsoft.io"
addons:
  ingress: true
  monitoring: true
  logging: true

# (required) cocktail service
cocktail: true
```

상기 항목에서 private\_key\_path  와 key\_path 는 서버에 ssh key로 접속하기 위한 private key와 public key의 경로를 기입한다. 이미 존재하는 경우에는 해당 경로를 기입하면 되고, 신규로 생성할 경우에는 아래 절차대로 실행하면 된다.

&lt; ssh key 신규 발급 방법 &gt;

```
# ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/cloud/.ssh/id_rsa): /tmp/cubetest/id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /tmp/cubetest/id_rsa.
Your public key has been saved in /tmp/cubetest/id_rsa.pub.
The key fingerprint is:
SHA256:liTKyW/l3eU9+mBzyksL0AKpYXRsvsQ793nWJiUgJC0 cloud@Clouds-MacBook-Pro.local
The key's randomart image is:
+---[RSA 2048]----+
|     ....        |
|    . .E.o       |
|     o=o=        |
|   o.oo*.o..     |
|    =.. So... .  |
|     . B oo. + o |
|      o + o.o==o.|
|     .     o=+Bo.|
|            o*=. |
+----[SHA256]-----+
```

4**.cube deploy 명령을 이용하여 실제 VM에 cocktail을 설치한다. -v debug옵션을 주면 설치되는 세부 내용을 확인할 수 있다.**

```
# cube deploy [-v debug]
```

5**.오류없이 설치가 완료되면 master 장비에 ssh로 접속하여 cocktail-system를 구성하는 컨테이너가 정상적으로 기동하는지 확인한다.**

```
# ssh -i ~/cube/pki/id_rsa root@{master1_ip}
# watch -n1 "kubectl get pods -n cocktail-system"
```

### **삭제**

1. 더 이상 필요하지 않을 경우에는 아래 명령으로 삭제할 수 있다.  
   디폴트로 옵션을 주지 않는 경우에는 생성한 k8s cluster만 삭제하고 설치 스크립트는 그대로 유지하며, -f 옵션을 추가하면 cube.yaml 파일을 cube.yaml.org로 백업파일을 생성한 후 설치스크립트도 모두 삭제하게 된다.

   따라서, 이 명령어는 주의해서 실행해야 한다.

```
# cd /Desktop/cubetest

// k8s만 삭제하고 설치 스크립트는 그대로 유지 
# cube destroy

// k8s와 설치 스크립트를 삭제하고 cube.yaml을 cube.yaml.org파일로 백업함. 
# cube destroy -f
```

삭제한 후에 다시 설치하기 위해서는 cube init 명령을 통해 설치 스크립트를 다시 다운로드 받은 후 설치하면 된다.



