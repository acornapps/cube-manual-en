# Troubleshooting Cocktail Installation Errors

**1.If Docker is not installed**

```
# cube init -p baremetal
Current Working directory : /Users/minhona/Desktop/cubetest
Checking pre-requisition [darwin]
exec: "docker": executable file not found in $PATH
docker is not found. please install docker before proceeding
Visit https://store.docker.com/editions/community/docker-ce-desktop-mac
```

Use the download link to install Docker and then restart Cube.

**2.If Ctrl + C is pressed while running Cube**

```
# docker ps -a                  // Verify that the cubetool container is running
# docker stop cube              // If running, stop the cubetool container
# docker rm cube                // Delete the cubetool container
```

Stop and delete the cubetool container in the above manner and continue.

**3.If local-hostname and ansible-hostname are different**_

```
TASK [addon : assign master roles]
Monday 30 October 2017  10:55:37 +0900 (0:00:00.042)     0:02:29.762
failed: [192.168.50.11] (item=master1) => ["changed": true, "cmd": "kubectl label nodes master1 
role=master1 --overwrite", "delta":"0:00:00.117665", "end": "2017-10-30 10:54:30.111133", "failed":
true, "item": "master1", "rc" 1, "start": "2017-10-30 10:54:29.993468", "stderr": Error from server
(NotFound): nodes \"master1\" not found", "stderr_lines": ["Error from server (NotFound): nodes \
"master1\" not found"]
```

Search the hostname directly from the server and modify the with_items value from Cube Scripts > Roles > Addon > Tasks > main.yml with the search result.

```
name: assign master roles
  when: kube_dash and cloud_provider != "aws"
  shell: "kubectl label nodes {{ item }} role=master --overwrite"
  with_items: "{{ ansible_hostname|lower }}"
  register: assign_master_roles
```

**4. If VirtualBox is not installed when installing on Minikube **

```
MinHoui-MacBook-Pro:cubetest minhona$ cube init -p minikube
Current Working directory : /Users/minhona/Desktop/cubetest
Checking pre-requisition [darwin]
exit status 126
virtualbox 5.1.x not found. please install virtualbox before proceeding
Visit https://www.virtualbox.org/wiki/Download_Old_Builds_5_1
```

Use the download link to install VirtualBox and then restart Cube.

**5. If kubectl is not installed when installing on Minikube **

```
MinHoui-MacBook-Pro:cubetest minhona$ cube init -p minikube
Current Working directory : /Users/minhona/Desktop/cubetest
Checking pre-requisition [darwin]
exec: "kubectl": executable file not found in $PATH
kubectl is not found. please install kubectl before proceeding
Visit https://kubernetes.io/docs/tasks/tools/install-kubectl
```

Use the download link to install VirtualBox and then restart Cube.

**6. If minikube is not installed when installing on Minikube **

```
MinHoui-MacBook-Pro:cubetest minhona$ cube init -p minikube
Current Working directory : /Users/minhona/Desktop/cubetest
Checking pre-requisition [darwin]
exec: "minikube": executable file not found in $PATH
minikube is not found. please install minikube before proceeding
Visit https://github.com/kubernetes/minikube
```

Use the download link to install minikube and then restart Cube.

**7.If Vagrant is not installed**

```
MinHoui-MacBook-Pro:cubetest minhona$ cube init -p virtualbox
Current Working directory : /Users/minhona/Desktop/cubetest
Checking pre-requisition [darwin]
exec: "vagrant": executable file not found in $PATH
vagrant not found. please install vagrant before proceeding
Visit https://www.vagrantup.com/downloads.html
```

Use the download link to install Vagrant and then restart Cube.

**8.If VirtualBox is not installed **

```
MinHoui-MacBook-Pro:cubetest minhona$ cube init -p virtualbox
Current Working directory : /Users/minhona/Desktop/cubetest
Checking pre-requisition [darwin]
exit status 126
virtualbox 5.1.x not found. please install virtualbox before proceeding
Visit https://www.virtualbox.org/wiki/Download_Old_Builds_5_1
```



