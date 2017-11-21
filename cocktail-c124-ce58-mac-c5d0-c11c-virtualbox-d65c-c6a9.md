# Cocktail Installation on virtualbox

MacOS에서 VirtualBox를 이용하여 Cocktail이 제공하는 기능을 간단하게 확인하고자 할 경우에 아래의 절차를 따라 설치한다.

1.먼저 설치를 위해 빈 디렉토리를 만든 후 해당 디렉토리로 이동한다

```
# mkdir /tmp/cubetest
# cd /tmp/cubetest
```

2.cube 명령을 이용하여 virtualbox용 설치 script를 download 받고 초기화 한다.

```
# cube init -p virtualbox
```

3.cube.yam 파일을 편집기로 열어서 설치하고자 하는 VM 정보를 기입한다. 아래는 예시임.

```
# vi cube.yaml
---
cloud_provider: "virtualbox"


# (required) Master node ips(comma separated). Example: ["192.168.50.11", "192.168.50.12"]
master_ip: ["192.168.50.11"]

# (required) Worker node ips(comma separated). Example: ["192.168.50.13", "192.168.50.14", "192.168.50.15"]
worker_ip: ["192.168.50.12"]

# (required) vCpu number
cpus: 2

# (required) Memory size (MB)
memory: 2048


# (required) Set true if high-availability is required.
high_availability: false

# (conditional) Set load-balancer ip.
lb_ip:

# (required) Path to an SSH private key file to access server.
private_key_path: "/cubetest/id_rsa"

# (required) Path to an SSH public key file to be provisioned as the SSH key.
key_path: "/cubetest/id_rsa.pub"

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
# (optional) if nfs server available
nfs_ip: "192.168.50.12"
nfs_mountdir: "/nfs"
```

상기 항목에서 private\_key\_path  와 key\_path 는 각각 VM에 ssh key로 접속하기 위한 private key와 public key의 경로를 기입한다. 이미 존재하는 경우에는 해당 경로를 기입하면 되고, 신규로 생성할 경우에는 아래 절차대로 실행하면 된다.

&lt; ssh key 신규 발급 방법 &gt;

```
# ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/cloud/.ssh/id_rsa): /tmp/cubetest/id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /Users/cloud/git/cubedeploy/virtualbox/5/id_rsa.
Your public key has been saved in /Users/cloud/git/cubedeploy/virtualbox/5/id_rsa.pub.
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

4.cube deploy 명령을 이용하여 실제 VM에 cocktail을 설치한다. -v debug옵션을 주면 설치되는 세부 내용을 확인할 수 있다.

```
# cube deploy [-v debug]
```

5.오류없이 설치가 완료되면 자동으로 browser가 기동되어 k8s dashboard로 접속하게 된다.

![](/assets/k8s_dashboard_1.jpeg)

고급 link를 클릭하고 아래 이동 버튼을 클릭한다.

![](/assets/k8s_dashboard_2.jpeg)

사용자이름과 비밀번호를 입력하면 k8s dashboard로 접속할 수 있다.

Namespace를 cocktail-system으로 선택하고 Services메뉴에서 cocktail-client-node-port를 선택한다

![](/assets/k8s_dashboard_4.jpeg)

connection의 internal endpoints에서 cocktail client의 node port를 확인한다. 아래 예에서는 31876 port임.

![](/assets/k8s_dashboard_5.jpeg)

1. 브라우저로 [http://{VM의](http://{VM의) masterip}:31876으로 접속하면 cocktail login 화면으로 접속할 수 있다. \(User Id, Password는 별도 문의\) ![](/assets/cocktail_login.jpeg)

2. 프로바이더 등록

프로바이더는 클라우드 리소스로 사용 할 Public/Private 클라우드 계정 정보를 등록, 편집, 삭제할 수 있다.

프로바이더 계정을 등록함으로써 해당 프로바이더의 리소스, 미터링 정보를 가져 오거나 서버를 생성 할 수 있다. 단 로컬 테스트 환경에서는 클러스터를 생성하기 위한 정보로만 사용됨.

![](/assets/cocktail_conf_provider.jpeg)

| 프로바이더  | **설명** |
| :--- | :--- |
| Amazon Web Service | Amazon사의 public 클라우드 서비스 |
| Google Cloud Platform | Google사의 public클라우스 서비스 |
| Microsoft cloud Service | Microsoft사의 public 클라우드 서비스 |
| Rovius Cloud | Rovius사의 private 클라우드 서비스 |
| Onpremise | Baremetal환경의 private 클라우드 서비스 |

| **유형** | **설명** |
| :--- | :--- |
| User | 프로바이더 등록을 위한 기본 값. 미터링 아닌 경우 User 선택 |
| Metering | Public Cloud를 사용시, 리소스 사용량을 받아오고자 할 때 선택 |



