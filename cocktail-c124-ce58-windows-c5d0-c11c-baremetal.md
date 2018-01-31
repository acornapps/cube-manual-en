# Cocktail Installation to baremetal on windows - in progress

Windows 설치 PC에서 baremetal 장비에 Cocktail를 설치하는 과정은 다음과 같다.

### **사전준비**

설치 전 아래와 같은 프로그램들이 미리 설치 되어 있어야 하며 설치 되어 있지 않을 경우 에러 메시지가 발생한다.

1\) Docker 다운로드 후 설치

[https://store.docker.com/editions/community/docker-ce-desktop-windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)

2\) Hyper-v : \[제어판\] - \[프로그램\] - \[windows 기능 겨기/끄기\] 에서 \[Hyper-V\] 기능을 활성화

3\) cube.exe 파일 환경변수 path 설정

4\) SSH key 생성

설치 pc에서 각 서버\(baremetal 장비\)에 ssh로 접속하여 설치하기 때문에 ssh-key 생성이 필요하다. 기존에 가지고 있는 ssh-key를 사용해도 무방하다. 신규로 생성할 경우에는 git을 다운로드 받아 bash shell을 실행한 후 아래 절차대로 실행하면 된다. 

\(git 다운로드 링크 : [https://git-for-windows.github.io/](https://git-for-windows.github.io/) \)

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

5\) ssh-key 복사

설치 pc에서 각 서버\(baremetal 장비\)에 ssh로 접속할 수 있도록 앞서 발급한 ssh Public키를 각 서버에 복사한다.

```
# ssh-copy-id -i id_rsa.pub root@ip
```

6\) NFS 설치 \(마스터, 워커 노드로 사용될 서버 모두에 nfs를 설치한다.\)

```
# yum install -y nfs-utils
```

#### 

### 설치

1.먼저 설치를 위해 CMD창에서 빈 디렉토리를 만든 후 해당 디렉토리로 이동한다.\(**CMD는 반드시 관리자 모드로 실행한다.**\)

```
# mkdir c:\tmp\cubetest
# cd c:\tmp\cubetest
```

**2.cube 명령을 이용하여 baremetal용 설치 script를 download 받고 초기화 한다.**

```
# cube init -p baremetal
```

**3.cube.yaml 파일을 열어서 설치하고자 하는 VM 정보를 기입한다. 아래는 master 1ea, worker 1ea, nfs server로 구성하는 예임.**

만약 외부 LoadBalancer가 가용하여 master를 이중화 할 경우 ib\_ip에 해당 load balancer ip를 기재하면 됨.

```
---
cloud_provider: "baremetal"


# (required) Master node ips(comma separated). Example: ["192.168.50.11", "192.168.50.12"]
master_ip: "104.199.151.160"  -> 마스터 ip

# (required) Worker node ips(comma separated). Example: ["192.168.50.13", "192.168.50.14", "192.168.50.15"]
worker_ip: "104.154.140.17"  -> 워커 ip

# (required) Set true if high-availability is required.  -> haproxy 사용여부(true of false)
haproxy: false

# (conditional) Set load-balancer ip.  -> LB사용 시 해당 아이피 입력
lb_ip:

# (required) ssh username to access server.
ssh_user_id: "cloud"

# (required) Path to an SSH private key file to access server.
private_key_path: "C:\\Users\\acornsoft\\.ssh\\id_rsa"

# (required) Path to an SSH public key file to be provisioned as the SSH key.
key_path: "C:\\Users\\acornsoft\\.ssh\\id_rsa.pub"

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
nfs_ip: "35.201.132.14"  -> nfs서버 ip
nfs_mountdir: "/nfs/data"  -> nfs서버 경로
```

**4.cube deploy 명령을 이용하여 실제 VM에 cocktail을 설치한다. -v debug옵션을 주면 설치되는 세부 내용을 확인할 수 있다.**

```
# cube deploy [-v debug]
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



