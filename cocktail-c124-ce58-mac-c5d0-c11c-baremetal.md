# Cocktail Installation to baremetal on mac os, linux

The procedure for installing Cocktail on a bare-metal server from a macOS- or Linux-based computer is as follows.

### **Preparations**

The following programs must be installed before installing Cocktail. Otherwise, an error will occur.

1\) Set the environment variable path so that the provided Cube binary can be used in any directory.

2\) Download and install Docker

\(Download link : [https://store.docker.com/search?offering=community&q=&type=edition ](https://store.docker.com/search?offering=community&q=&type=edition)\)

3\) Generate SSH keys

ㅤRefer to "Create and add private & public SSH keys" section  
in Overview

4\) Copy SSH Keys

Copy the generated SSH keys to each server \(bare-metal equipment\) for access.

```
# ssh-copy-id -i id_rsa.pub root@ip
```

5\) If there is no NFS server, install an NFS server on a specific node. Install nfs-util on all remaining nodes via yum.

```
# yum install -y nfs-utils
# vi /etc/exports        // Include the following in the file and save. 
/nfs 203.236.100.0/24(rw,sync,no_root_squash,no_all_squash)

// Add rpcbind and nfs-server in service and start.
# systemctl enable rpcbind
# systemctl enable nfs-server
# systemctl start rpcbind
# systemctl start nfs-server
```

#### ㅤ

### Installation

**1.Create an empty directory for installation and move to that directory**

```
# mkdir /Desktop/baremetal
# cd /Desktop/baremetal
```

**2.Use the cube command to download the baremetal installation script.**

```
# cube init -p baremetal
```

**3.Open the previously-generated cube.yam script file with an editor, enter the information of the VM to be installed, and save.**

\( Below is an example of a 2-master, 3-worker, and nfs server configuration.\)

If an external load balancer is used and the master is duplicated, enter the corresponding load balancer ip at lb\_ip.

```
---
cloud_provider: "baremetal"


# (required) Master node ips(comma separated).
master_ip: ["203.236.100.10","203.236.100.11"]  -> Enter Master Node ip 

# (optional) Master node private ips(comma separated).
master_private_ip: ["10.0.0.2","10.0.0.3"]  -> Enter Master Node private ip

# (required) Worker node ips(comma separated).
worker_ip: ["203.236.100.12", "203.236.100.13", "203.236.100.14"] -> Enter Worker Node ip

# (optional) Worker node private ips(comma separated).
worker_private_ip: ["10.0.0.4","10.0.0.5","10.0.0.6"] -> Enter Worker server private ip 기입

# (required) Set true if high-availability is required.  -> Set haproxy usage(true of false 기입 )
haproxy: false

# (conditional) Set load-balancer ip. -> If using an LB, fill in the corresponding IP; If not, leave blank:

# (required) ssh username to access server.
ssh_user_id: "root" 

# (required) Absolute Path to an SSH private key file to access server.. -> Enter ssh-private key path
private_key_path: "/path/to/ssh_private_key"

# (required) Absolute Path to an SSH public key file to be provisioned as the SSH key. -> Enter ssh-public key path 
key_path: "/path/to/ssh_public_key"

# (required) Data directory for docker, kubelet, etcd, log.
data_dir: "/cocktail"

# Kubernetes  -> Default data; do not change.
k8s_version: "1.9.8"
cluster_name: "cube"
domain_name: "acornsoft.io"
addons:
  cm: true # cocktail + prometheus monitoring(without grafana)
  km: false # kube dashboard + prometheus monitoring(with grafana)

# (optional) if nfs server available
nfs_ip: "203.236.100.15"  -> Enter nfs server ip
nfs_mountdir: "/nfs"      -> Enter nfs server's shared directory path
```

**4.Install Cocktail on actual VM using the cube create command in the path where the cube.yaml file is located.**

```
# cube create
```

**5.If the installation completes without errors, access the master device via SSH and verify that the containers that constitute the cocktail-system are running normally.**

```
# ssh -i ~/cube/pki/id_rsa root@203.236.100.10
# watch -n1 "kubectl get pods -n cocktail-system"

NAME                                 READY     STATUS    RESTARTS   AGE
apollomq-59fbfbb7c4-nzpls            1/1       Running   0          3d
cocktail-api-7c9bb84c46-dgw8c        1/1       Running   0          3d
cocktail-client-5869fb7975-tmckz     1/1       Running   0          3d
cocktail-cmdb-fd87c6c5f-tfzlg        1/1       Running   1          3d
cocktail-dashboard-fb6f8f5b9-zn6sj   1/1       Running   0          3d
redis-cb9c6859f-qq4f8                1/1       Running   0          3d
```

**6.Go to **[http://Master-IP:30000](http://Master-IP:30000)** using your browser to access the Cocktail login page.**

### **Delete**

**1.Depending on the option, you may delete only k8s clusters or clean up the scripts and back up the cube.yaml file.**  
If an option is not provided by default, only generated k8s clusters are deleted and installation scripts remain intact. If the -f option is added, however, installation scripts are also deleted after creating a backup of the cube.yaml file to cube.yaml.org.

**Therefore, this command must be run with caution**.

```
# cd /Desktop/cubetest

// Only deletes k8s and keeps installation scripts intact 
# cube destroy

// Deletes k8s and installation scripts and backs up cube.yaml to cube.yaml.org. 
# cube destroy -f
```

To reinstall after deleting with the -f option, download the installation scripts again via the cube init command and reinstall.

