# `delete` command

delete는 Kubernetes cluster에서 node 및 monitoring, logging 등의 add-on을 제거한다.

현재 버전에서 node 제거만 지원한다.

_delete는 node를 cluster에서 제거할 뿐 물리적으로 없애지는 않는다_.

---

### Prerequisite

노드를 제거할 수 있는 상태. `create` command or `create infra` command

### Usage

* `cube delete [node] [flags]`

### Sub command

##### node

* Kubernetes cluster에서 노드를 제거한다. 

### Flags

##### name

* `--name, -n`

* 추가할 노드의 이름. 노드의 이름은 [status command](/commands/status-command.md)를 통해 알아낼 수 있다.

* 필수: 필수.

##### 



