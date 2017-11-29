# 사용자의 micro service가 k8s dns로 서로 통신하지 못하는 경우.

일반적으로 Kubernetes에서 각 Container들은 연관된 Container와 통신하기 이해 Kubernetes DNS를 사용한다.

먼저 Kubernetes의 DNS 서비스가 정상 동작 중인지 확인하는 방법은 busybox를 생성하고 컨테이너에 접속해서 nslookup명령으로 k8s의 DNS에 접속되는지 확인한다.

* etcd 데몬 상태 체크

etcd daemon은 kubernetes에서 생성되는 각종 object의 데이터 저장소로서 아래와 같이 상태를 체크할 수 있다.

```
# etcdctl cluster-health
member 343f53e92dacc502 is healthy: got healthy result from http://192.168.0.172:2379
member c9e93f4efc1a9813 is healthy: got healthy result from http://192.168.0.173:2379
member f95af587a997f2bf is healthy: got healthy result from http://192.168.0.171:2379
cluster is healthy
```

* flannel 데몬 상태 체크

flannel daemon은 kubernetes에서 내부적인 network으로 사용되는 것으로, POD끼리 kubernetes DNS로 접속이 안될 경우 해당 flannel설정파일과 etcd 데이터의 정합성을 체크해 본다.

```
# etcdctl ls -r
/atomic.io
/atomic.io/network
/atomic.io/network/config
/atomic.io/network/subnets
/atomic.io/network/subnets/10.10.19.0-24
/atomic.io/network/subnets/10.10.25.0-24
/atomic.io/network/subnets/10.10.31.0-24
/atomic.io/network/subnets/10.10.63.0-24
/atomic.io/network/subnets/10.10.92.0-24
/atomic.io/network/subnets/10.10.27.0-24
/atomic.io/network/subnets/10.10.73.0-24

# systemctl status flanneld
```



