# `create` command

The create command installs Kubernetes and/or Cocktail on the platform you prepared via the `init` command.

---

### Prerequisite

`init` command

### Usage

* `cube create [infra | k8s]`
* If a sub-command is not used, Kubernetes \(k8s\) is installed after creating a server instance \(infra\).

### Sub command

##### infra

* Creates a server instance for constructing Kubernetes clusters. In cases of VirtualBox and other cloud providers \(AWS, Azure, GCE\), `infra` add virtual machines. But, bare metal dose not add server(s).

##### k8s

* Installs Kubernetes. To use this command, you must first execute the `infra` command.



