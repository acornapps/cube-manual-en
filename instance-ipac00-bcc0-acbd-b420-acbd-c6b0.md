# Cube로 설치한 node의 ip 정보가 변경될 경우.

cube로 설치한 master node, worker node에서 ip 정보가 변경되면 현재로서는 cube를 이용하여 재설치하는 방식을 권장한다.

이는 k8s의 component중 api server, scheduler, control manager가 ip 인증서 기반으로 통신하며, etcd의 flannel subnetwork정보도 ip정보가 저장되기 때문이다.





