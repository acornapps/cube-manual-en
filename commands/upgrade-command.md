# `upgrade` command

upgrade는 설치된 Kubernetes를 upgrade한다. **1.8.x 버전에서 1.10.x 버전으로의 upgrade는 지원하지 않으며**, 먼저 1.9.x로 upgrade한 후 다시 1.10.x로 upgrade 한다.

_현재 버전에서 downgrade는 지원하지 않는다_.

---

### Prerequisite

Kubernetes 설치 상태.`create` command or `create k8s` command

### Usage

* `cube upgrade [flags]`

### Flags

##### version

* `--version, -v`

* Kubernetes version을 지정한다.

* 필수: 필수

* 현재 버전에서 지원하는 version 목록은 아래와 같다.

| flag 값 |
| :--- |
| 1.8.13 |
| 1.9.0 ~ 1.9.8 |
| 1.10.0 ~ 1.10.3 |



