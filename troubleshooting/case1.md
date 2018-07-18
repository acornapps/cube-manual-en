# k8s Cluster Health Check

The procedure for checking the status of the main Kubernetes and Cocktail components on a Cube-installed device are as follows.

This can be used to check that the main k8s components are operating properly after installation with Cube.



* You can check etcd, k8s pod, monitoring pod, and cocktail pod status via the cube status command.

As a data repository for various objects created via Kubernetes, etcd daemon is comprised of etcd clusters (at least three units) for HA environments.

```
Check the operational health of etcd cluster.
If a specific node is determined to be 'unhealthy,' a corresponding message will be displayed.
# export ETCDCTL_API=3
# etcdctl --cert /etc/kubernetes/pki/etcd-peer.crt --key /etc/kubernetes/pki/etcd-peer.key 
--cacert /etc/kubernetes/pki/etcd-ca.crt 
--endpoints=https://k8s-elb-bb5c1a1a1159199b.elb.ap-northeast-2.amazonaws.com:2379 endpoint health
member 343f53e92dacc502 is healthy: got healthy result from http://192.168.0.172:2379
member c9e93f4efc1a9813 is healthy: got healthy result from http://192.168.0.173:2379
member f95af587a997f2bf is healthy: got healthy result from http://192.168.0.171:2379
cluster is healthy

You can verify the IPs and ports of nodes that constitute an etcd cluster and check which node is configured as the leader.
# etcdctl --cert /etc/kubernetes/pki/etcd-peer.crt --key /etc/kubernetes/pki/etcd-peer.key 
--cacert /etc/kubernetes/pki/etcd-ca.crt 
--endpoints=https://k8s-elb-bb5c1a1a1159199b.elb.ap-northeast-2.amazonaws.com:2379 member list
2ff8ca0b39f4ce02, started, ip-10-0-0-37, https://10.0.0.37:2380, https://10.0.0.37:2379
40e0ff5ee27c98d0, started, ip-10-0-0-6, https://10.0.0.6:2380, https://10.0.0.6:2379
f1ada6cc179c0400, started, ip-10-0-0-17, https://10.0.0.17:2380, https://10.0.0.17:237
```

* Docker Status Check

```
# systemctl status docker
```

* Kubelet Daemon Status Check

```
# systemctl status kubelet
```

* Check system log message

The system log records in real-time the log information of basic k8s components. Should a problem arise, the most effective method of checking it would be to start from the tail for the most recent error.

```
# tail -f /var/log/messages
```

* Master-to-Node Connection Check

You can query the connection status of nodes contained in a cluster via the kubectl command from a master server.

```
# kubectl get nodes
```



