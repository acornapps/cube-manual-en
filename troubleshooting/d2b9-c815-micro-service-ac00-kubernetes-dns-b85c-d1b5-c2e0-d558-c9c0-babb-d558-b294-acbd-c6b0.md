# 사용자의 micro service가 k8s dns로 서로 통신하지 못하는 경우.

일반적으로 k8s에서 각 Container들은 연관된 Container와 통신하기 이해 Kubernetes DNS를 사용한다.

먼저 k8s의 DNS 서비스가 정상 동작 중인지 확인하는 방법은 busybox를 생성하고 컨테이너에 접속해서 nslookup명령으로 k8s의 DNS에 접속되는지 확인한다. 

* busybox container 생성

아래는 busybox의 yaml 파일로 이를 busybox.yaml로 저장한다.

```
apiVersion: v1
kind: Pod
metadata:
  name: busybox
spec:
  containers:
  - image: busybox
    command:
      - sleep
      - "3600"
    imagePullPolicy: IfNotPresent
    name: busybox
  restartPolicy: Always
```

* 마스터 서버에 ssh로 접속하여 kubectl 명령으로 busybox container를 생성한다.

```
// busybox 생성
# kubectl create -f busybox.yaml

// busybox container가 생성되는지 확인
# kubectl get pods
[root@master1 test]# kubectl get pods
NAME                                      READY     STATUS    RESTARTS   AGE
busybox                                   1/1       Running   0          1m
...
```

* Busybox container에 접속하여 nslookup 명령으로 kubernetes의 DNS 접속여부를 확인한다.

```
// busybox에 접속후 shell 실행
# kubectl exec -it busybox -- /bin/sh

// kubernetes.default dns 접속여부 확인
/ # nslookup kubernetes.default
Server:    100.64.0.10
Address 1: 100.64.0.10 kube-dns.kube-system.svc.cube

Name:      kubernetes.default
Address 1: 100.64.0.1 kubernetes.default.svc.cube

// 다른 서비스 접속 여부 확인 (아래는 예시로 cocktail component중 api server를 lookup한 예임)
/ # nslookup cocktail-api.cocktail-system
Server:    100.64.0.10
Address 1: 100.64.0.10 kube-dns.kube-system.svc.cube

Name:      cocktail-api.cocktail-system
Address 1: 100.72.213.63 cocktail-api.cocktail-system.svc.cube
```



