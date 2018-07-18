# `delete` command

The `delete` command deletes add-ons (nodes, monitoring, logging, etc.) from a Kubernetes cluster.
Only node deletions are supported in the current version.

_The delete command merely removes a node from a cluster - it does not destroy it physically_.

---

### Prerequisite

Node able to be deleted. After `create` command or `create infra` command

### Usage

* `cube delete [node] [flags]`

### Sub command

##### node

* Deletes a node from a Kubernetes cluster. 

### Flags

##### name

* `--name, -n`

* Name of node to be deleted. The node name can be found via the `status` command
* Requirement: Required.

##### 



