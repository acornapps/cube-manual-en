# Add etcd member to existing etcd cluster

This section describes how to add an etcd member while the etcd cluster is running.

* etcd certificate directory: /etc/kubernets/pki
* etcd settings file: /etc/etcd/etcd.conf

**1.Generate an etcd certificate from the etcd node to be added based on the etcd ca file.**

```
# etcd node to be added via ssh

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

**2.Install etcd package and setup configuration file**

Install etcd and set up environment on etcd node to be added.

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

// Add new node including existing etcd cluster's ETCD_INITIAL_CLUSTER value. Comma seperated
ETCD_INITIAL_CLUSTER=wmaster01=https://192.168.0.226:2380,wworker01=https://192.168.0.227:2380

// ETCD_INITIAL_CLUSTER_STATE should be set as "existing" 
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

**3.Add new nodes to existing etcd cluster.**

```
# etcdctl --cert /etc/kubernetes/pki/etcd-peer.crt --key /etc/kubernetes/pki/etcd-peer.key \
--cacert /etc/kubernetes/pki/etcd-ca.crt member add wworker01 --peer-urls=https://192.168.0.227:2380

# etcdctl --cert /etc/kubernetes/pki/etcd-peer.crt --key /etc/kubernetes/pki/etcd-peer.key \
--cacert /etc/kubernetes/pki/etcd-ca.crt member add wworker02 --peer-urls=https://192.168.0.228:2380
```

**4.When added, verify etcd cluster member list and status**

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



