# k8s cluster 인증서 update 방법

최초 cube로 cocktail 설치 이후에 kubernetes 내부 인증서를 업데이트하는 경우최이는 cube로 설치후 k8s 주요 구성 요소의 정상 동작 여부를 확인하는데 사용할 수 있다.

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

* 


