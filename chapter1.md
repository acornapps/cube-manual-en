# 기존 Cluster에 Node 추가하기

기존 cluster에 node를 추가하기 위해 아래의 절차대로 수행한다.

Cube로 설치한 directory를 $CUBE\_DEPLOY라고 하면

```
# cd $CUBE_DEPLOY/cubescripts 
# vi node.yaml
---
# This playbook deploys a kubernetes cluster with the default addons.

- hosts: node
  roles:
    - yum-repo
  tags:
    - yum-repo

# Install flannel
- hosts: node
  roles:
   - flannel

# Install docker
- hosts: node
  roles:
    - docker
  tags:
    - docker

# install kubernetes on the nodes
- hosts: node
  roles:
    - node
  tags:
    - node
# ansible-playbook -i inventories/node node.yaml
```

주의 사항.

추가되는 node에 firewall daemon이 실행되고 있으면 추후 POD 배치시에 정상동작하지 않는다.

[https://docs.docker.com/v1.6/installation/centos/](https://docs.docker.com/v1.6/installation/centos/)

FirewallD

CentOS-7 introduced firewalld, which is a wrapper around iptables and can conflict with Docker.

Whenfirewalldis started or restarted it will remove theDOCKERchain from iptables, preventing Docker from working properly.

When using Systemd,firewalldis started before Docker, but if you start or restartfirewalldafter Docker, you will have to restart the Docker daemon.





