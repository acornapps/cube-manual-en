# Cluster backup & restore

k8s cluster를 어떤 이유로 재설치 경우, etcd snapshot과 cocktail cmdb, builderdb를 restore하여 기존 상태로 복원하는 절차를 기술한다.

이때, 반드시 etcd 인증서는 기존 인증서를 사용해야 하며, 그렇지 않을 경우 복원할 수 없다.

* etcd 인증서 디렉토리: /etc/kubernets/pki
* etcd 설정파일: /etc/etcd/etcd.conf

**1.etcd, cocktail cmdb, builderdb backup**

아래와 같이 etcd snapshot 생성, cocktail db backup을 위한 shell script를 자신의 환경에 맞게 수정한 후 backup을 주기적으로 실시한다.

    # vi cocktail_backup.sh
    #!/bin/sh

    export ETCDCTL_API=3

    ETCD_CERT="/etc/kubernetes/pki/etcd-peer.crt"
    ETCD_KEY="/etc/kubernetes/pki/etcd-peer.key"
    ETCD_CACERT="/etc/kubernetes/pki/etcd-ca.crt"

    ETCD_EP="https://192.168.0.202:2379"
    CURRENT_DATE=`date '+%Y%m%d'`
    CURRENT_TIME=`date '+%Y%m%d_%H%M%S.db'`

    ETCD_BACKDIR="/nas/BACKUP/etcd"
    COCKTAIL_BACKDIR="/nas/BACKUP/db"

    SOMAC_CMDB_DIR=`kubectl get pvc -n cocktail-system | grep cocktail-cmdb | awk '{print "cocktail-system-"$1"-"$3}'`
    SOMAC_BUILDERDB_DIR=`kubectl get pvc -n cocktail-system | grep builder-db | awk '{print "cocktail-system-"$1"-"$3}'`

    /bin/etcdctl --cert "$ETCD_CERT" --key "$ETCD_KEY" --cacert "$ETCD_CACERT" --endpoints="$ETCD_EP" snapshot save "$ETCD_BACKDIR/etcd_$CURRENT_DATE"

    cp -a /nas/"$SOMAC_CMDB_DIR" "$COCKTAIL_BACKDIR/$SOMAC_CMDB_DIR"_"$CURRENT_DATE"
    cp -a /nas/"$SOMAC_BUILDERDB_DIR" "$COCKTAIL_BACKDIR/$SOMAC_BUILDERDB_DIR"_"$CURRENT_DATE"

    cp -a /nas/"$CMDB_DIR1" "$COCKTAIL_BACKDIR/$CMDB_DIR1"_"$CURRENT_DATE"
    cp -a /nas/"$BUILDERDB_DIR1" "$COCKTAIL_BACKDIR/$BUILDERDB_DIR1"_"$CURRENT_DATE"

    cp -a /nas/"$CMDB_DIR2" "$COCKTAIL_BACKDIR/$CMDB_DIR2"_"$CURRENT_DATE"
    cp -a /nas/"$BUILDERDB_DIR2" "$COCKTAIL_BACKDIR/$BUILDERDB_DIR2"_"$CURRENT_DATE"

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



