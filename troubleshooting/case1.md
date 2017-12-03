# k8s cluster의 health check

Cube로 설치한 장비에서 Kubernetes와 Cocktail의 주요 구성 요소에 대해 상태 체크를 하기 위한 방법은 아래와 같다.

이는 cube로 설치후 k8s 주요 구성 요소의 정상 동작 여부를 확인하는데 사용할 수 있다.

* etcd 데몬 상태 체크

etcd daemon은 kubernetes에서 생성되는 각종 object의 데이터 저장소로서 아래와 같이 상태를 체크할 수 있다.

```
etcd cluster의 정상 동작여부를 확인할 수 있다.
특정 node가 unhealth상태일 경우 해당 메시지가 출력된다.
# etcdctl cluster-health
member 343f53e92dacc502 is healthy: got healthy result from http://192.168.0.172:2379
member c9e93f4efc1a9813 is healthy: got healthy result from http://192.168.0.173:2379
member f95af587a997f2bf is healthy: got healthy result from http://192.168.0.171:2379
cluster is healthy

etcd cluster를 구성하는 node의 ip, port와 어느 node가 leader로 구성되었는지 확인할 수 있다.
# etcdctl member list
343f53e92dacc502: name=cocktail02 peerURLs=http://192.168.0.172:2380 clientURLs=http://192.168.0.172:2379 isLeader=false
c9e93f4efc1a9813: name=cocktail03 peerURLs=http://192.168.0.173:2380 clientURLs=http://192.168.0.173:2379 isLeader=false
f95af587a997f2bf: name=cocktail01 peerURLs=http://192.168.0.171:2380 clientURLs=http://192.168.0.171:2379 isLeader=true
```

* flannel 데몬 상태 체크

flannel daemon은 kubernetes에서 사용하는 overlay network으로 사용되는 것으로, POD간 이 network을 통해 통신하게 된다. 일반적으로 OD간에는 Service 이름으로 상태 POD를 찾게 되는데 이때 사용되는 DNS 서버가 kubernetes DNS로, POD간 접속 에러가 발생하는 경우에 해당 flannel설정파일과 etcd 데이터의 정합성을 체크해 본다.

아래는 master node 2대, worker node 5대 일 경우에 예시를 든 것이다.

```
etcd에 각 node의 overlay network 리스트 조회
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

* Docker 상태 체크

```
# systemctl status docker
```

* kubelet 데몬 상태 체크

```
# systemctl status kubelet
```

* system log message 조회

시스템 로그에는  k8s 기본 컴포넌트의 로그정보가 실시간으로 기록되기 때문에 문제 발생시 해당 log를 tail로 조회하여 오류 사항을 먼저 파악하는 것이 효과적이다.

```
# tail -f /var/log/messages
```

* master와 node간 정상 접속 여부 확인

kubectl 명령을 활용하여 master 서버에서 cluster에 포함되어 있는 node 연결 상태를 조회할 수 있다.

```
# kubectl get nodes
```



