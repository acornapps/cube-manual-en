# Cube cli 를 이용한 Cocktail Installation

```
# cube init -p baremetal
# vi cube.yaml
---
cloud_provider: "baremetal"


# (required) Master node ips(comma separated). Example: ["192.168.50.11", "192.168.50.12"]
master_ip: ["192.168.0.171", "192.168.0.172"]

# (required) Worker node ips(comma separated). Example: ["192.168.50.13", "192.168.50.14", "192.168.50.15"]
worker_ip: ["192.168.0.173", "192.168.0.174", "192.168.0.175", "192.168.0.176", "192.168.0.177"]

# (required) Set true if high-availability is required.
high_availability: true

# (conditional) Set load-balancer ip.
lb_ip: "192.168.0.202"

# (required) Path to an SSH public key file to be provisioned as the SSH key.
key_path: "/Users/cloud/git/cubescripts/cert/id_rsa.pub"

# Kubernetes
k8s_version: "1.6.7"
cluster_name: "cube"
domain_name: "acornsoft.io"
addons:
  ingress: false
  monitoring: true
  logging: true

# (required) cocktail service
cocktail: true
# (optional) if nfs server available
nfs_ip: "192.168.0.175"
nfs_mountdir: "/nfs"
# cube deploy -v debug
```



