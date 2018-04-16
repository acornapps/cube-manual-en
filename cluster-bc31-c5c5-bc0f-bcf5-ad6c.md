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

    ETCD_EP="https://192.168.0.202:2379"        // etcd endpoint 값 지정
    CURRENT_DATE=`date '+%Y%m%d'`
    CURRENT_TIME=`date '+%Y%m%d_%H%M%S.db'`

    ETCD_BACKDIR="/nas/BACKUP/etcd"            // etcd snapshot을 백업할 위치
    COCKTAIL_BACKDIR="/nas/BACKUP/db"          // cocktail cmddb, builderdb를 백업할 위치

    SOMAC_CMDB_DIR=`kubectl get pvc -n cocktail-system | grep cocktail-cmdb | awk '{print "cocktail-system-"$1"-"$3}'`
    SOMAC_BUILDERDB_DIR=`kubectl get pvc -n cocktail-system | grep builder-db | awk '{print "cocktail-system-"$1"-"$3}'`

    /bin/etcdctl --cert "$ETCD_CERT" --key "$ETCD_KEY" --cacert "$ETCD_CACERT" --endpoints="$ETCD_EP" \
    snapshot save "$ETCD_BACKDIR/etcd_$CURRENT_DATE"        // etcd snapshot backup

    cp -a /nas/"$SOMAC_CMDB_DIR" "$COCKTAIL_BACKDIR/$SOMAC_CMDB_DIR"_"$CURRENT_DATE"    // cmdb backup
    cp -a /nas/"$SOMAC_BUILDERDB_DIR" "$COCKTAIL_BACKDIR/$SOMAC_BUILDERDB_DIR"_"$CURRENT_DATE"    // builderdb backup

**2.추가할 etcd node에 etcd 설치 및 환경설정**

추가할 etcd node에 etcd 설치 및 환경파일을 설정한다.

```
# etcdctl --cert /etc/kubernetes/pki/etcd-peer.crt --key /etc/kubernetes/pki/etcd-peer.key \
--cacert /etc/kubernetes/pki/etcd-ca.crt --endpoints=https://10.0.0.3:2379 --name=master  \
--initial-advertise-peer-urls="https://10.0.0.3:2380" --initial-cluster="master=https://10.0.0.3:2380" \
--initial-cluster-token="etcd-k8-cluster" --data-dir=“/data/etcd” snapshot restore /root/backup/etcd_20180322


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



