# Kubernetes startup & shutdown

Cocktail이 설치된 cluster의 startup과 shutdown은 일련의 순서대로 진행해야 한다.

### Startup

Startup시에는 master node를 먼저 기동하고 worker node를 기동하는 순서로 진행한다.

1\) Master Node

```
# systemctl start etcd
# systemctl start flanneld
# systemctl start docker
# systemctl start kubelet
```



2\) Worker Node

```
# systemctl start flanneld
# systemctl start docker
# systemctl start kubelet
```



### Shutdown

Shutdown시에는 worker node를 먼저 종료하고 master node를 종료하는 순서로 진행한다.

1\) Worker Node

```
# systemctl stop kubelet
# systemctl stop docker
# systemctl stop flanneld
# systemctl stop etcd
```



2\) Master Node

```
# systemctl stop kubelet
# systemctl stop docker
# systemctl stop flanneld
```



