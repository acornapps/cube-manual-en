# Cluster startup & shutdown

Startup and shutdown of the cluster where Cocktail is installed must be performed sequentially.

### Startup

For startups, master nodes must be activated before worker nodes.

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

Conversely, worker nodes must be deactivated before master nodes for shutdowns.

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



