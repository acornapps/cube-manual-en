# Cocktail Installation to baremetal on macos

Mac 설치 PC에서 baremetal 장비에 Cocktail를 설치하는 과정은 다음과 같다.

### **사전준비**

설치 전 아래와 같은 프로그램들이 미리 설치 되어 있어야 하며 설치 되어 있지 않을 경우 에러 메시지가 발생한다.

1\) 제공받은 cube 바이너리를 어느 디렉토리에서든 사용할 수 있도록 환경변수 path 설정을 한다.

2\) Docker 다운로드 후 설치

\( 다운로드 링크 : [https://store.docker.com/editions/community/docker-ce-desktop-mac](https://store.docker.com/editions/community/docker-ce-desktop-mac) \)![](/assets/docker설치.png)

3\) SSH key 생성

설치 pc에서 각 서버\(baremetal 장비\)에 ssh로 접속하여 설치하기 때문에 ssh-key 생성이 필요하다. 기존에 가지고 있는 ssh-key를 사용해도 무방하다.

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

4\) ssh-key 복사

설치 pc에서 각 서버\(baremetal 장비\)에 ssh로 접속할 수 있도록 앞서 발급한 ssh Public키를 각 서버에 복사한다.

```
# ssh-copy-id -i id_rsa.pub root@ip
```

5\) 제공받은 cube 바이너리를 어느 디렉토리에서든 사용할 수 있도록 환경변수 path 설정을 한다.

6\) NFS 설치 \(마스터, 워커 노드로 사용될 서버 모두에 nfs를 설치한다.\)

```
# yum install -y nfs-utils
```

#### ㅤ

### 설치

**1.먼저 설치를 위해 맥 터미널을 열고 아래와 같이 빈 디렉토리를 만든 후 해당 디렉토리로 이동한다**

```
# mkdir /Desktop/cubetest
# cd /Desktop/cubetest
```

**2.cube 명령을 이용하여 baremetal용 설치 script를 download 받는다.**

```
# cube init -p baremetal
```

**3.앞서 생성된 스크립트 중 cube.yaml 파일을 편집기로 열고, 설치하고자 하는 VM 정보를 기입한 후 저장한다.**

\( 아래는 master 1, worker 3대, nfs server로 구성하는 예임.\)

만약 외부 LoadBalancer 가용하여 master를 이중화 할 경우 lb\_ip에 해당 load balancer ip를 기재하면 됨.

```
---
cloud_provider: "baremetal"


# (required) Master node ips(comma separated). Example: ["192.168.50.11", "192.168.50.12"]
master_ip: ["203.236.100.10","203.236.100.11"]  -> 마스터 서버 ip 기입 

# (required) Worker node ips(comma separated). Example: ["192.168.50.13", "192.168.50.14", "192.168.50.15"]
worker_ip: ["203.236.100.12", "203.236.100.13", "203.236.100.14"] -> 워커 서버 ip 기입 

# (required) Set true if high-availability is required.  -> haproxy 사용여부(true of false 기입 )
haproxy: false

# (conditional) Set load-balancer ip. -> LB사용 시 해당 아이피 기입하고 아닐 시 공란
lb_ip:

# (required) ssh username to access server.
username: root 

# (required) Path to an SSH private key file to access server. -> ssh-private 키 경로 기입
private_key_path: "/cubetest/id_rsa"

# (required) Path to an SSH public key file to be provisioned as the SSH key. -> ssh-public 키 경로 기입 
key_path: "/cubetest/id_rsa.pub"


# Kubernetes  -> 기본사항으로 변경하지 않음.
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
nfs_ip: "203.236.100.15"  -> nfs서버의 ip 기입
nfs_mountdir: "/nfs"      -> nfs서버의 공유 디렉토리 경로 기입
```

**4.cube.yaml 파일이 있는 경로에서 cube deploy 명령을 이용하여 실제 VM에 cocktail을 설치한다. -v debug 옵션을 주면 설치 되는 세부 내용을 확인할 수 있다.**

```
# cube deploy -v debug
```

**5.오류없이 설치가 완료되면 master 장비에 ssh로 접속하여 cocktail-system를 구성하는 컨테이너가 정상적으로 기동하는지 확인한다.**

```
# ssh -i ~/cube/pki/id_rsa root@203.236.100.10
# watch -n1 "kubectl get pods -n cocktail-system"

NAME                                     READY     STATUS    RESTARTS   AGE
apollomq-3231363346-77ltx                1/1       Running   0          2m
builder-api-517225361-c9j86              1/1       Running   0          2m
builder-db-2825750227-fwqlz              1/1       Running   0          2m
cocktail-api-67592375-63k43              1/1       Running   1          2m
cocktail-client-4046445963-rrwhh         2/2       Running   1          2m
cocktail-cmdb-520687312-rhm8s            1/1       Running   0          2m
cocktail-metering-aws-3487533297-tw1x7   1/1       Running   0          2m
redis-3766055555-1lzmh                   1/1       Running   0          2m
```

**6.프로바이더 등록**

환경설정-&gt;프로바이더 -&gt;우측상단+ 클릭. 프로바이더는 Onpremise, 유형은 User로 선택하고 생성버튼을 클릭한다.

![](/assets/cocktail_conf_provider.jpeg)

| **프로바이더** | **설명** |
| :--- | :--- |
| Amazon Web Service | Amazon사의 public 클라우드 서비스 |
| Google Cloud Platform | Google사의 public클라우스 서비스 |
| Microsoft cloud Service | Microsoft사의 public 클라우드 서비스 |
| Onpremise | Baremetal환경의 private 클라우드 서비스 |

| **유형** | **설명** |
| :--- | :--- |
| User | 프로바이더 등록을 위한 기본 값. 미터링 아닌 경우 User 선택 |
| Metering | Public Cloud를 사용시, 리소스 사용량을 받아오고자 할 때 선택 |

**7.클러스터 등록**

클러스터 등록을 위해 환경설정 -&gt; 클러스터 -&gt; 우측상단+ 클릭 후 정보를 기입한다.![](/assets/클러스터1.png)

| 클러스터 옵션 | **설명** |
| :--- | :--- |
| 인그레스 지원 | 인그레스 호스트에 subpath로 서비스 노출하는 방식 |
| 로드밸런서 지원 | 로드밸런스 기능 지원 |
| 퍼시스턴트 볼륨 지원 | Public cloud스토리지 및 외부 스토리지 사용 가능 |
| 노드포트 지원 | 노드에 포트를 붙여 서비스 노출하는 방식 |

* **인그레스 지원**![](/assets/클러스터2.png)인그레스 방식에 사용할 host ip 주소로 마스터 노드 ip주소를 입력한다.\(마스터 노드 L4 구성 시 L4 VIP 입력\)

* **노드포트 지원 **![](/assets/클러스터3.png)노드 포트 방식에 사용할 ip 주소로 마스터 노드 ip주소를 입력한다.\(마스터 노드 L4 구성 시 L4 VIP 입력\)

* **클러스터유형 \(기본값으로 cube선택\)**![](/assets/클러스터4.png)

| **목록 ** | **설명** |
| :--- | :--- |
| k8s 버전 | 1.6.7\(칵테일에서 사용중인 쿠버네티스 버전\) |
| 마스터 URL | 쿠버네티스 마스터 IP \(L4 구성 시 L4 VIP\) -[https://마스터ip:6443](https://www.gitbook.com/book/acornsoft/cocktail-manual/edit#)형식을 따른다. |
| 모니터링 호스트 | InfluxDB 설치 ip\(칵테일 모니터링을 위해 influxDB 사용\) |
| 모니터링 포트 | 30315\(influxDB 기본 포트\) |
| 모니터링 사용자 | root\(influxDB 사용자 ID\) |
| 모니터링 비밀번호 | root\(influxDB 사용자 passwd\) |

* **큐브 클러스터 유형**

MANAGED 선택

인증유형 : certification![](/assets/클러스터5.png)

| **목록** | **설명** |
| :--- | :--- |
| 사용자아이디 | admin |
| 패스워드 | AdminPass |
| Cluster CA Certification | 마스터 서버 접속 후 /etc/kubernetes/pki 경로 이동 후 ca.pem파일 값 입력 |
| Client Certificate Data | 마스터 서버 접속 후 /etc/kubernetes/pki 경로 이동 후 apiserver.pem 파일 값 입력 |
| Client Key Data | 마스터 서버 접속 후 /etc/kubernetes/pki 경로 이동 후 apiserver-key.pem 파일 값 입력 |

**8.서비스등록 **

서비스 등을 위해 환경설정 -&gt; 서비스  -&gt; 우측상단 + 클릭 후 이름, 설명, 색상 및 사용자, 클러스터, 레지스트리 정보를 입력한다. ![](/assets/서비스2.png)

* 사용자편집

![](/assets/서비스3.png)

* 클러스터 편집 

![](/assets/서비스4.png)

* 레지스트리 편집 

![](/assets/서비스5.png)

**9.볼륨 등록 **

볼륨 등록을 위해 환경설정 -&gt; 볼륨 -&gt; 우측상단 + 를 클릭한다.

입력값은 아래 이미지와 같은 값으로 등록하면 된다. \(주의. 스토리지클래스 이름은 반드시 "cocktail-nfs"라고 기입해야 한다.\)

![](/assets/cocktail_volume.jpeg)

### **삭제**

1. 테스트가 완료되어 더 이상 필요하지 않을 경우에는 아래 명령으로 삭제할 수 있다.  
   디폴트로 옵션을 주지 않는 경우에는 생성한 k8s cluster만 삭제하고 설치 스크립트는 그대로 유지하며, -f 옵션을 추가하면 cube.yaml 파일을 cube.yaml.org로 백업파일을 생성한 후 설치스크립트도 모두 삭제하게 된다.

   따라서, 이 명령어는 주의해서 실행해야 한다.

```
# cd /Desktop/cubetest

// k8s만 삭제하고 설치 스크립트는 그대로 유지 
# cube destroy

// k8s와 설치 스크립트를 삭제하고 cube.yaml을 cube.yaml.org파일로 백업함. 
# cube destroy -f
```

1. 삭제한 후에 다시 설치하기 위해서는 cube init 명령을 통해 설치 스크립트를 다시 다운로드 받은 후 설치하면 된다.



