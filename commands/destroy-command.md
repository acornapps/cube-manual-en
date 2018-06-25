# `destroy` command

destroy는 Kubernetes cluster 노드,  Cocktail삭제한다.

---

### Prerequisite

Kubernetes 및 Cocktail이 설치된 상태

### Usage

* `cube destroy [flags]`
* flag없이 호출하면 Cocktail만 제거한다.

### Flags

##### pre-destroy

* `--pre-destroy`

* aws, azure provider 삭제 시 연결된 load balancer, volume을 삭제한다. **반드시 호출해야 한다**.

* 필수: 필수

##### force

* `--force, -f`

* aws, azure provider에서는 노드\(서버 인스턴스\)까지 삭제한다.

* 필수: 선택



