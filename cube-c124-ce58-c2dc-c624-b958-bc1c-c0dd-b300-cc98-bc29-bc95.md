# Cocktail 설치 오류발생 대처 방법

**1.Docker가 설치되어 있지 않은 경우**

```
# cube init -p baremetal
Current Working directory : /Users/minhona/Desktop/cubetest
Checking pre-requisition [darwin]
exec: "docker": executable file not found in $PATH
docker is not found. please install docker before proceeding
Visit https://store.docker.com/editions/community/docker-ce-desktop-mac
```

다운로드 링크로 이동하여 Docker 설치 후 cube를 재실행 한다.

**2.cube 실행중에 Ctrl+C를 누른 경우**

```
# docker ps -a                  // cubetool container가 running상태인지 확인
# docker stop cube              // cubetool container가 running상태일 경우 stop함.
# docker rm cube                // cubetool container 삭제.
```

이런식으로 cubetool docker를 중지 및 삭제한 후 작업을 계속한다.

**3.local-**_**hostname과 ansible-hostname이 다른 경우**_

```
TASK [addon : assign master roles]
Monday 30 October 2017  10:55:37 +0900 (0:00:00.042)     0:02:29.762
failed: [192.168.50.11] (item=master1) => ["changed": true, "cmd": "kubectl label nodes master1 
role=master1 --overwrite", "delta":"0:00:00.117665", "end": "2017-10-30 10:54:30.111133", "failed":
true, "item": "master1", "rc" 1, "start": "2017-10-30 10:54:29.993468", "stderr": Error from server
(NotFound): nodes \"master1\" not found", "stderr_lines": ["Error from server (NotFound): nodes \
"master1\" not found"]
```

서버에서 직접 hostname을 검색하고 그 값을 \[cubescripts-&gt;roles-&gt;addon-&gt;tasks-&gt;main.yml\]의 with\_items 값을 수정하고 재실행합니다.

```
name: assign master roles
  when: kube_dash and cloud_provider != "aws"
  shell: "kubectl label nodes {{ item }} role=master --overwrite"
  with_items: "{{ ansible_hostname|lower }}"
  register: assign_master_roles
```

5**. Minikube에 설치시 Virtualbox가 설치되어 있지 않은 경우 **

```
MinHoui-MacBook-Pro:cubetest minhona$ cube init -p minikube
Current Working directory : /Users/minhona/Desktop/cubetest
Checking pre-requisition [darwin]
exit status 126
virtualbox 5.1.x not found. please install virtualbox before proceeding
Visit https://www.virtualbox.org/wiki/Download_Old_Builds_5_1
```

다운로드 링크로 이동하여 Virtualbox 설치 후 cube를 재실행 한다.

6**. Minikube에 설치시 kubectl이 설치되어 있지 않은 경우 **

```
MinHoui-MacBook-Pro:cubetest minhona$ cube init -p minikube
Current Working directory : /Users/minhona/Desktop/cubetest
Checking pre-requisition [darwin]
exec: "kubectl": executable file not found in $PATH
kubectl is not found. please install kubectl before proceeding
Visit https://kubernetes.io/docs/tasks/tools/install-kubectl
```

다운로드 링크로 이동하여 kubectl 설치 후 cube를 재실행 한다.

7**. Minikube에 설치시  minikube가 설치되어 있지 않은 경우 **

```
MinHoui-MacBook-Pro:cubetest minhona$ cube init -p minikube
Current Working directory : /Users/minhona/Desktop/cubetest
Checking pre-requisition [darwin]
exec: "minikube": executable file not found in $PATH
minikube is not found. please install minikube before proceeding
Visit https://github.com/kubernetes/minikube
```

다운로드 링크로 이동하여 minikube 설치 후 cube를 재실행 한다.

8**.Vagrant가 설치되어 있지 않은 경우**

```
MinHoui-MacBook-Pro:cubetest minhona$ cube init -p virtualbox
Current Working directory : /Users/minhona/Desktop/cubetest
Checking pre-requisition [darwin]
exec: "vagrant": executable file not found in $PATH
vagrant not found. please install vagrant before proceeding
Visit https://www.vagrantup.com/downloads.html
```

다운로드 링크로 이동하여 Vagrant 설치 후 cube를 재실행 한다.

9**.Virtualbox가 설치되어 있지 않은 경우 **

```
MinHoui-MacBook-Pro:cubetest minhona$ cube init -p virtualbox
Current Working directory : /Users/minhona/Desktop/cubetest
Checking pre-requisition [darwin]
exit status 126
virtualbox 5.1.x not found. please install virtualbox before proceeding
Visit https://www.virtualbox.org/wiki/Download_Old_Builds_5_1
```



