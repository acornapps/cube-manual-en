# `upgrade` command

The upgrade command upgrades a Kubernetes installation. **Upgrading directly from version 1.8.x to version 1.10.x is not supported**. You must first upgrade to 1.9.x and then to 1.10.x.

_Downgrades are currently not supported_

---

### Prerequisite

Kubernetes already installed. Execute `create` command or `create k8s` command

### Usage

* `cube upgrade [flags]`

### Flags

##### version

* `--version, -v`

* Specifies the Kubernetes version.

* Requirement: Required

* The list of k8s versions supported by the current version are as follows.

| flag value |
| :--- |
| 1.8.13 ~ 1.8.14 |
| 1.9.0 ~ 1.9.8 |
| 1.10.0 ~ 1.10.3 |



