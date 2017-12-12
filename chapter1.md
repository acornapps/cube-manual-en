# 기존 Cluster에 Node 추가하기

기존 cluster에 node를 추가하기 위해 아래의 절차대로 수행한다.

Cube로 설치한 directory를 $CUBE\_DEPLOY라고 하면 

```
# cd $CUBE_DEPLOY/cubescripts/inventories

# vi inventory
[etcd]
192.168.50.10

[etcd-private]
192.168.50.10

[masters]
192.168.50.10

[sslhost]
192.168.50.10

[node]             # 추가하고자 하는 Node의 ip주소를 [node] 항목 밑에 추가로 입력한다.
192.168.50.11
192.168.50.12 

# cd ..            # CUBE_DEPLOY/cubescripts로 이동        
# ansible-playbook -i inventories/inventory node.yml     #ansible을 이용하여 node.yml 실
```

주의 사항.

추가되는 node에 firewall daemon이 실행되고 있으면 추후 POD 배치시에 정상동작하지 않는다.

[https://docs.docker.com/v1.6/installation/centos/](https://docs.docker.com/v1.6/installation/centos/)

FirewallD

CentOS-7 introduced firewalld, which is a wrapper around iptables and can conflict with Docker.

Whenfirewalldis started or restarted it will remove theDOCKERchain from iptables, preventing Docker from working properly.

When using Systemd,firewalldis started before Docker, but if you start or restartfirewalldafter Docker, you will have to restart the Docker daemon.

