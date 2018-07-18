# Cocktail Installation Overview

## Preparations

The following programs must be installed before installing Cocktail. Otherwise, an error will occur.

1\) Download and install Docker \(Required\)

[https://store.docker.com/editions/community/docker-ce-desktop-mac](https://store.docker.com/editions/community/docker-ce-desktop-mac)

2\) Download and install Vagrant \(If building a local testing environment using VirtualBox\)

[https://www.vagrantup.com/downloads.html](https://www.vagrantup.com/downloads.html)

3\) Download VirtualBox \(5.1.x version\) \(If building a local testing environment using VirtualBox\)

[https://www.virtualbox.org/wiki/Download\_Old\_Builds\_5\_1](https://www.virtualbox.org/wiki/Download_Old_Builds_5_1)

4\) Create and add private & public SSH keys \(Required\)

* SSH keys are used to access server instances created via Cube.

* The SSH key paths are specified in the key path entry of the cube.yaml file. For more information, refer to the documentation for each installation environment.

* Refer to the below section for information on creating new keys. As with existing keys, the newly-generated keys must be specified in the cube.yaml file.

```
# ssh-keygen -f /path/to/file -t rsa -N ''
Generating public/private rsa key pair.
Your identification has been saved in /path/to/file.
Your public key has been saved in /path/to/file.pub.
The key fingerprint is:
SHA256:D5aprMbBbJFUPo7gKgLFXH8WGHsXCpm8HCKSQXO8gDE cloud@Clouds-MacBook-Pro.local
The key's randomart image is:
+---[RSA 2048]----+
|E*...o+=. .      |
|+=+ooo*o o .     |
| .*o.+=o= .      |
| o oooo= +       |
|. .o... S        |
|..  =. o o       |
|+  o .o   .      |
|o   o.           |
|   ..            |
+----[SHA256]-----+


# ssh-add id_rsa (user-added)
```

5\) Download kubectl \(Optional\)

[https://kubernetes.io/docs/tasks/tools/install-kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl)

#### 



