# k8s cluster의 health check

Cube로 설치한 장비에서 Kubernetes와 Cocktail의 주요 구성 요소에 대해 상태 체크를 하기 위한 방법은 아래와 같다.

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
// etcd에 각 node의 overlay network 리스트 조회
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

# etcdctl get /atomic.io/network/subnets/10.10.31.0-24
{"PublicIP":"192.168.0.171","BackendType":"vxlan","BackendData":{"VtepMAC":"72:e7:a7:c6:6d:1e"}}


// flanneld의 상태 조회
# systemctl status flanneld
● flanneld.service - Flanneld overlay address etcd agent
   Loaded: loaded (/usr/lib/systemd/system/flanneld.service; enabled; vendor preset: disabled)
   Active: active (running) since 화 2017-11-07 16:57:40 KST; 3 weeks 2 days ago
  Process: 2744 ExecStartPost=/usr/libexec/flannel/mk-docker-opts.sh -k DOCKER_NETWORK_OPTIONS -d /run/flannel/docker (code=exited, status=0/SUCCESS)
 Main PID: 2716 (flanneld)
   CGroup: /system.slice/flanneld.service
           └─2716 /usr/bin/flanneld -etcd-endpoints= -etcd-prefix= -etcd-endpoints=http://192.168.0.171:2379,http://192.168.0.172:2379,http://1...

11월 30 16:57:40 cocktail01 flanneld-start[2716]: I1130 16:57:40.901477    2716 network.go:160] Lease renewed, new expiration: 2017-12-...000 UTC
Warning: Journal has been rotated since unit was started. Log output is incomplete or unavailable.
Hint: Some lines were ellipsized, use -l to show in full.
```



