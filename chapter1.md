# 기존 Cluster에 Node 추가하기

기 설치된 Cluster에 worker node를 추가하기 위해서  아래의 절차대로 수행한다.

Master node는 추가할 수 없으며 baremetal 경우에는 추가할 node의 private ip, public ip를 각각 -r, -u 옵션 값으로 지정하고, public cloud의 경우에는 worker node의 총수를 -c 옵션 다음에 기입한다.

```
// baremetal에 node(private ip: 10.10.10.11, public ip: 192.168.10.11)을 추가할 경우
# cube add node -r 10.10.10.11 -u 192.168.10.11

// aws나 azure에 worker node 총 개수를 3으로 맞출 경우
# cube add node -c 3
```

Upgrade가 정상 완료되었는지 확인하기 위해 cube status 명령으로 node의 버전을 조회한다.

```
# cube status
```



