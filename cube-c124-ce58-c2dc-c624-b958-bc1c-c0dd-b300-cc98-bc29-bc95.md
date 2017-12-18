# Cocktail 설치 오류발생 대처 방법 - in progress

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



