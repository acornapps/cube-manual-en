# `create` command

create는 Kubernetes, Cocktail\(전체 또는 각각\)을 `init`로 준비한 플랫폼에 설치한다.

---

### Prerequisite

`init` command

### Usage

* cube create \[ infra \| k8s \]
* sub command 없이 실행하면 서버 인스턴스 생성\(infra\) 후 Kubernetes\(k8s\)를 설치한다.

### Sub command

##### infra

* Kubernetes cluster를 구성할 서버 인스턴스를 생성한다. VirtualBox 및 cloud provider\(AWS, Azure, GCE\)는 가상 머신을 추가하지만, baremetal은 별다른 동작을 하지 않는다.

##### k8s

* Kubernetes를 설치한다. 이 명령을 실행하려면 반드시 infra 명령을 먼저 실행 해야 한다.





