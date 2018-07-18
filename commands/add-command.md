# `add` command

The add command adds add-ons (nodes, monitoring, logging, etc.) to a Kubernetes cluster. Only node add-ons are supported in the current version.

---

### Prerequisite

After `create` command or `create infra` command

### Usage

* `cube add [node] [flags]`

### Sub command

##### node

* Adds a node to a Kubernetes cluster. While aws and azure add a new server instance, gce, virtualbox, and baremetal require server preparation.

### Flags

##### public ip

* `--public-ip, -u`

* Public IP address of the node to be added.

* Requirement: Required if provider is gce, virtualbox, or baremetal; Unused if provider is aws or azure.

##### private ip

* `--private-ip, -u`

* Private IP address of the node to be added.

* Requirement: Required if provider is gce, virtualbox, or baremetal; Unused if provider is aws or azure.

##### node count

* `--node-count, -c`

* Total number of nodes (existing nodes and nodes to be added).

* Requirement: Required if provider is aws or azure; Unused if provider is gce, virtualbox, or baremetal.



