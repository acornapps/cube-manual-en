# If user microservices cannot connect to each other.

Generally, the containers on k8s use Kubernetes DNS for inter-container communications. The internal cluster IP of a pod changes when creation and deletion occurs, and pods generally use k8s DNS to look for the required container in a microservice.

The method used to verify the operational status of the k8s DNS service involves the creation of a busybox to access a container and checking the connection to k8s DNS via the nslookup command.

* Creating a Busybox Container

The following is a busybox yaml file that is saved as busybox.yaml.

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

* Connect to the master server via SSH and create a busybox container via the kubectl command.

```
// Creates busybox
# kubectl create -f busybox.yaml

// Verifies whether the busybox container was created successfully
# kubectl get pods
[root@master1 test]# kubectl get pods
NAME                                      READY     STATUS    RESTARTS   AGE
busybox                                   1/1       Running   0          1m
...
```

* Connect to the busybox container via the nslookup command and check the Kubernetes DNS connection status.

```
// Connects to busybox and runs shell
# kubectl exec -it busybox -- /bin/sh

// Checks kubernetes.default dns connection
# nslookup kubernetes.default
Server:    100.64.0.10
Address 1: 100.64.0.10 kube-dns.kube-system.svc.cube

Name:      kubernetes.default
Address 1: 100.64.0.1 kubernetes.default.svc.cube

// Checks connection to other services. (The following is an example of an api server lookup among cocktail components.)
/ # nslookup cocktail-api.cocktail-system
Server:    100.64.0.10
Address 1: 100.64.0.10 kube-dns.kube-system.svc.cube

Name:      cocktail-api.cocktail-system
Address 1: 100.72.213.63 cocktail-api.cocktail-system.svc.cube
```



