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
# vi cubescripts/roles/distributecert/worker/tasks/main.yml
---
- name: Create kubernetes cert directory
  file: path={{ cert_dir }} state=directory

- name: Slurp CA certificate
  slurp: src={{ master_cert_dir }}/{{ item }}
  with_items:
    - ca.crt
    - ca.key
    - etcd-ca.crt
    - etcd-ca.key
#    - etcd-peer.crt    // etcd ca 인증서만 배포되도록 주석으로 처리.
#    - etcd-peer.key
#    - etcd.crt
#    - etcd.key
  register: pki_certs
  run_once: true
  delegate_to: "{{ groups['sslhost'][0] }}"

- name: Write CA certificate to disk
  copy: dest={{ cert_dir }}/{{ item.item }} content="{{ item.content | b64decode }}"
  register: openssl_cert
  with_items: "{{ pki_certs.results }}"
  no_log: true

yum install -y etcd-3.2.15

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

**3.기존 etcd node에서 member를 join함.**

```
# etcdctl --cert /etc/kubernetes/pki/etcd-peer.crt --key /etc/kubernetes/pki/etcd-peer.key \
--cacert /etc/kubernetes/pki/etcd-ca.crt member add wworker01 --peer-urls=https://192.168.0.227:2380

# etcdctl --cert /etc/kubernetes/pki/etcd-peer.crt --key /etc/kubernetes/pki/etcd-peer.key \
--cacert /etc/kubernetes/pki/etcd-ca.crt member add wworker02 --peer-urls=https://192.168.0.228:2380
```

**4. 추가 후 etcd cluster member list 및 상태 확인**

```
# etcdctl --write-out=table --cert /etc/kubernetes/pki/etcd-peer.crt --key /etc/kubernetes/pki/etcd-peer.key \
--cacert /etc/kubernetes/pki/etcd-ca.crt --endpoints=https://192.168.0.226:2379 member list


+------------------+---------+-----------+----------------------------+----------------------------+
|        ID        | STATUS  |   NAME    |         PEER ADDRS         |        CLIENT ADDRS        |
+------------------+---------+-----------+----------------------------+----------------------------+
| fb6dacb1ff6a5471 | started | wworker01 | https://192.168.0.227:2380 | https://192.168.0.227:2379 |
| fddfca0140234d70 | started | wworker02 | https://192.168.0.228:2380 | https://192.168.0.228:2379 |
| fefe74d66ed53d79 | started | wmaster01 | https://192.168.0.226:2380 | https://192.168.0.226:2379 |
+------------------+---------+-----------+----------------------------+----------------------------+

# etcdctl --write-out=table --cert /etc/kubernetes/pki/etcd-peer.crt --key /etc/kubernetes/pki/etcd-peer.key \
--cacert /etc/kubernetes/pki/etcd-ca.crt --endpoints=https://192.168.0.226:2379 endpoint status

https://192.168.0.226:2379 is healthy: successfully committed proposal: took = 1.83824ms
https://192.168.0.227:2379 is healthy: successfully committed proposal: took = 2.419152ms
https://192.168.0.228:2379 is healthy: successfully committed proposal: took = 2.068545ms
```



