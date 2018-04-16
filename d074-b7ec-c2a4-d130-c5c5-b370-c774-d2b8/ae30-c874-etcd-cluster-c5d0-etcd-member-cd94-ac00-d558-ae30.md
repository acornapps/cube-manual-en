# Add etcd member to existing etcd cluster

Etcd cluster가 가동중인 상태에서 etcd member를 추가하는 방법에 대해 기술한다.

* etcd 인증서 디렉토리: /etc/kubernets/pki
* etcd 설정파일: /etc/etcd/etcd.conf

**1.etcd ca 파일을 기반으로 추가하고자 하는 etcd node에서 etcd 인증서를 생성한다.**

```
# ssh로 추가할 etcd node 접속

# cd /etc/kubernetes/pki
# scp root@{ip}:/etc/kubernetes/pki/etcd-ca.* .

# openssl genrsa -out /etc/kubernetes/pki/etcd.key 2048
# openssl req -new -key /etc/kubernetes/pki/etcd.key -subj '/CN=etcd' \
| openssl x509 -req -CA /etc/kubernetes/pki/etcd-ca.crt -CAkey /etc/kubernetes/pki/etcd-ca.key \
-CAcreateserial -out /etc/kubernetes/pki/etcd.crt -days 3650 -extensions \
v3_req -extfile /etc/kubernetes/pki/openssl.conf

# openssl genrsa -out /etc/kubernetes/pki/etcd-peer.key
# openssl req -new -key /etc/kubernetes/pki/etcd-peer.key -subj '/CN=etcd-peer' \
| openssl x509 -req -CA /etc/kubernetes/pki/etcd-ca.crt -CAkey /etc/kubernetes/pki/etcd-ca.key \
-CAcreateserial -out /etc/kubernetes/pki/etcd-peer.crt -days 3650 -extensions \
v3_req -extfile /etc/kubernetes/pki/openssl.conf
```

**2.추가할 etcd node에 etcd 설치 및 환경설정**

추가할 etcd node에 etcd 설치 및 환경파일을 설정한다.

```
# yum install -y etcd-3.2.15

# vi /etc/etcd/etcd.conf
#[member]
ETCD_NAME=wworker01

ETCD_DATA_DIR=/home/data/etcd
#ETCD_SNAPSHOT_COUNTER="10000"
#ETCD_HEARTBEAT_INTERVAL="100"
#ETCD_ELECTION_TIMEOUT="1000"
#ETCD_MAX_SNAPSHOTS="5"
#ETCD_MAX_WALS="5"
#ETCD_CORS=""

#[cluster]
ETCD_INITIAL_ADVERTISE_PEER_URLS=https://192.168.0.227:2380

// 기존 etcd intial_cluster url을 포함하여 쉼표(,)로 구분하여 지정함.
ETCD_INITIAL_CLUSTER=wmaster01=https://192.168.0.226:2380,wworker01=https://192.168.0.227:2380

// 기존 etcd cluster에 추가할 것임으로 initial_cluster_state 값을 "existing"으로 설정함.
ETCD_INITIAL_CLUSTER_STATE=existing

ETCD_INITIAL_CLUSTER_TOKEN=etcd-k8-cluster
#ETCD_DISCOVERY=""
#ETCD_DISCOVERY_SRV=""
#ETCD_DISCOVERY_FALLBACK="proxy"
#ETCD_DISCOVERY_PROXY=""
ETCD_LISTEN_PEER_URLS=https://0.0.0.0:2380
ETCD_ADVERTISE_CLIENT_URLS=https://192.168.0.227:2379
ETCD_LISTEN_CLIENT_URLS="https://0.0.0.0:2379"

#[proxy]
ETCD_PROXY="off"

#[security]
ETCD_CA_FILE=/etc/kubernetes/pki/etcd-ca.crt
ETCD_CERT_FILE=/etc/kubernetes/pki/etcd.crt
ETCD_KEY_FILE=/etc/kubernetes/pki/etcd.key
ETCD_PEER_CA_FILE=/etc/kubernetes/pki/etcd-ca.crt
ETCD_PEER_CERT_FILE=/etc/kubernetes/pki/etcd-peer.crt
ETCD_PEER_KEY_FILE=/etc/kubernetes/pki/etcd-peer.key
ETCD_PEER_CLIENT_CERT_AUTH="true"
ETCD_TRUSTED_CA_FILE="/etc/kubernetes/pki/etcd-ca.crt"
ETCD_CLIENT_CERT_AUTH="true"
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
ETCD_INITIAL_CLUSTER_STATE="existing"
ETCD_DATA_DIR="/home/data/etcd"
```

**3.k8s에 생성된 default token를 모두 삭제한다.**

```
# kubectl get secrets --all-namespaces | grep default-token | awk '{print $1, $2}' | xargs -n 2 kubectl delete secrets -n
```

**4. Worker node -&gt; Master Node 순으로 rebooting한다.**

```
# reboot
```



