# `delete` command

The `delete` command deletes node and add-ons (monitoring, Cocktail, etc.) from a Kubernetes cluster.


_The delete command for baremetal cluster only removes a node from a cluster - it does not destroy it physically_.

---

### Prerequisite

Node able to be deleted. After `create` command or `create infra` command

### Usage

* `cube delete [node] [flags]`

### Sub command

##### node

* Deletes a node from a Kubernetes cluster. 

##### addon

* Deletes a add-on from a Kubernetes cluster. 

### Flags

##### name

* `--name, -n`

* Deleting node: Name of node to be deleted. The node name can be found via the `status` command
* Deleting add-on: Name of addon to be deleted. The node name can be found via the `status addon` command 
* Requirement: Required.

##### 



