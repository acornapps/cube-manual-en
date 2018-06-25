# k8s cluster의 health check

Cube로 설치한 장비에서 Kubernetes와 Cocktail의 주요 구성 요소에 대해 상태 체크를 하기 위한 방법은 아래와 같다.

이는 cube로 설치후 k8s 주요 구성 요소의 정상 동작 여부를 확인하는데 사용할 수 있다.



* cube status 명령을 통한 etcd, k8s pod, monitoring pod, cocktail pod 상태를 체크할 수 있다.

etcd daemon은 kubernetes에서 생성되는 각종 object의 데이터 저장소로서 HA환경일 경우 etcd cluster로 구성\(최소 3대\)되며, leader

```
etcd cluster의 정상 동작여부를 확인할 수 있다.
특정 node가 unhealth상태일 경우 해당 메시지가 출력된다.
# export ETCDCTL_API=3
# etcdctl --cert /etc/kubernetes/pki/etcd-peer.crt --key /etc/kubernetes/pki/etcd-peer.key 
--cacert /etc/kubernetes/pki/etcd-ca.crt 
--endpoints=https://k8s-elb-bb5c1a1a1159199b.elb.ap-northeast-2.amazonaws.com:2379 endpoint health
member 343f53e92dacc502 is healthy: got healthy result from http://192.168.0.172:2379
member c9e93f4efc1a9813 is healthy: got healthy result from http://192.168.0.173:2379
member f95af587a997f2bf is healthy: got healthy result from http://192.168.0.171:2379
cluster is healthy

etcd cluster를 구성하는 node의 ip, port와 어느 node가 leader로 구성되었는지 확인할 수 있다.
# etcdctl --cert /etc/kubernetes/pki/etcd-peer.crt --key /etc/kubernetes/pki/etcd-peer.key 
--cacert /etc/kubernetes/pki/etcd-ca.crt 
--endpoints=https://k8s-elb-bb5c1a1a1159199b.elb.ap-northeast-2.amazonaws.com:2379 member list
2ff8ca0b39f4ce02, started, ip-10-0-0-37, https://10.0.0.37:2380, https://10.0.0.37:2379
40e0ff5ee27c98d0, started, ip-10-0-0-6, https://10.0.0.6:2380, https://10.0.0.6:2379
f1ada6cc179c0400, started, ip-10-0-0-17, https://10.0.0.17:2380, https://10.0.0.17:237
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



