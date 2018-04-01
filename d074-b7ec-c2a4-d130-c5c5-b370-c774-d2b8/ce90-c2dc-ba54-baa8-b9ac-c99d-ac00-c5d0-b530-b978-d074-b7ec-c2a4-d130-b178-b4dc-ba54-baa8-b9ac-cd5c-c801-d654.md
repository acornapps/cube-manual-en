# 캐시 메모리 증가에 따른 클러스터 노드 메모리 최적화 방법

클러스터를 구성하는 각 node는 cache memory가 점차적으로 증가하게 되어 있다.

Cocktail에서 클러스터의 메모리 사용량이 실제 사용량보다 많게 표시가 되면 주기적으로 각 node에서 아래 명령을 수행함으로써 cache memory를 초기화 한다.

```
# sync; echo 1 > /proc/sys/vm/drop_caches  또는
# sync; sysctl -w vm.drop_caches=1

```



