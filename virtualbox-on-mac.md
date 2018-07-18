# Cocktail Installation to Virtualbox on Mac OS X

To check the functionality provided by Cocktail via VirtualBox on macOS, follow the installation steps below.

### **Preparations**

Refer to [Cocktail Installation](/cocktail-c124-ce58-mac-c5d0-c11c-virtualbox-d65c-c6a9.md)for details on preparations.

The following programs must be installed before installing Cocktail. Otherwise, an error will occur.

1\) Download and install Docker

[https://store.docker.com/editions/community/docker-ce-desktop-mac](https://store.docker.com/editions/community/docker-ce-desktop-mac)

2\) Download and install Vagrant

[https://www.vagrantup.com/downloads.html](https://www.vagrantup.com/downloads.html)

3\) Download VirtualBox. \(v. 5.1.x\)

[https://www.virtualbox.org/wiki/Download\_Old\_Builds\_5\_1](https://www.virtualbox.org/wiki/Download_Old_Builds_5_1)

4\) Create and add private & public SSH keys

ㅤRefer to "Create and add private & public SSH keys" section
 in Overview 

**1. Create an empty directory for installation and move to that directory**

```
# mkdir /Desktop/virtualbox
# cd /Desktop/virtualbox
```

**2.Use the cube command to download and initialize the virtualbox installation script.**

```
# cube init -p virtualbox
```

**3.Open the cube.yam file with an editor and enter the information of the VM to be installed. Below is an example.**

```
# vi cube.yaml
---
cloud_provider: "virtualbox"


# (required) Master node ips(comma separated).
master_ip: ["192.168.50.10"]

# (required) Worker node ips(comma separated).
worker_ip: ["192.168.50.20", "192.168.50.100"]

# (required) Master node vcpu number
master_cpus: 4

# (required) Master node memory size (MB)
master_memory: 4096

# (required) Worker node vcpu number
worker_cpus: 1

# (required) Worker node memory size (MB)
worker_memory: 2048


# (required) Set true if haproxy is required.
haproxy: false

# (conditional) Set load-balancer ip.
lb_ip:

# (required) Absolute Path to an SSH private key file to access server.
private_key_path: "/path/to/ssh_private_key"

# (required) Absolute Path to an SSH public key file to be provisioned as the SSH key.
key_path: "/path/to/ssh_public_key"

# Kubernetes
k8s_version: "1.9.8"
cluster_name: "cube"
domain_name: "acornsoft.io"
addons:
  cm: true # cocktail + prometheus monitoring(without grafana)
  km: false # kube dashboard + prometheus monitoring(with grafana)

# (required) if cocktail service true
nfs_ip: "192.168.50.10"
nfs_mountdir: "/nfs"
```

As shown above, private\_key\_path and key\_path are private and public SSH key paths, respectively, for accessing VMs and **must be entered using absolute paths**. If the keys already exist, simply enter the corresponding paths. To generate new keys, follow the procedure below

**&lt; Generating New SSH Keys  &gt;**



**4.Install Cocktail on actual VM using the cube create command.**

```
# cube create
```

**5.If the installation completes without errors, access the master device via SSH and verify that the containers that constitute cocktail-system is running normally.**

```
# ssh -i ~/cube/pki/id_rsa root@192.168.50.11
# watch -n1 "kubectl get pods -n cocktail-system"
NAME                                     READY     STATUS    RESTARTS   AGE
apollomq-3231363346-77ltx                1/1       Running   0          2m
cocktail-api-67592375-63k43              1/1       Running   1          2m
cocktail-client-4046445963-rrwhh         2/2       Running   1          2m
cocktail-cmdb-520687312-rhm8s            1/1       Running   0          2m
cocktail-metering-aws-3487533297-tw1x7   1/1       Running   0          2m
redis-3766055555-1lzmh                   1/1       Running   0          2m
```

**6.Go to **[http://Master-IP:30000](http://Master-IP:30000)** using your browser to access the Cocktail login page. \(Contact us about having a user ID and password issued\)**![](/assets/cocktail_login.jpeg)

### **Delete**

**1.Depending on the option, you may delete only k8s clusters or generated VMs.**  
If an option is not provided by default, only generated k8s clusters are deleted and installation scripts remain intact. If the -f option is added, however, generated VMs are deleted and installation scripts are also deleted after creating a backup of the cube.yaml file to cube.yaml.org.  
**Therefore, this command must be run with caution**.

```
# cd /Desktop/cubetest

// Only deletes k8s and keeps installation scripts intact 
# cube destroy

// Deletes k8s and installation scripts and backs up cube.yaml to cube.yaml.org. 
# cube destroy -f
```

To reinstall after deleting with the -f option, download the installation scripts again via the cube init command and reinstall.

