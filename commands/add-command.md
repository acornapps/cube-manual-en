# `add` command

The add command adds add-ons (nodes, monitoring, logging, etc.) to a Kubernetes cluster. Only node add-ons are supported in the current version.

---

### Prerequisite

노드를 추가할 수 있는 상태. `create` command or `create infra` command

### Usage

* `cube add [node] [flags]`

### Sub command

##### node

* Kubernetes cluster에 노드를 추가한다. aws, azure provider는 새로운 서버 인스턴스를 추가하지만, gce, virtaulbox, baremetal provider는 미리 준비된 서버가 필요하다.

### Flags

##### public ip

* `--public-ip, -u`

* 추가할 노드의 public ip 주소.

* 필수: provider가 gce, virtualbox, baremetal이면 필수, aws, azure provider는 사용하지 않는다.

##### private ip

* `--private-ip, -u`

* 추가할 노드의 private ip 주소.

* 필수: provider가 gce, virtualbox, baremetal이면 필수, aws, azure provider는 사용하지 않는다.

##### node count

* `--node-count, -c`

* 기존 노드와 추가할 노드를 합친 전체 노드의 수.

* 필수: provider가 aws, azure이면 필수,  gce, virtualbox, baremetal provider는 사용하지 않는다.



