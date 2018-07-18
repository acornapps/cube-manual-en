# `add` command

The add command adds add-ons (nodes, monitoring, logging, etc.) to a Kubernetes cluster. 

_`add` command work with sub command_
  
---

### Prerequisite

After `create` command or `create infra` command

### Usage

* `cube add [node | addon] [flags]`

### Sub command

##### node

* Adds a node to a Kubernetes cluster. While aws and azure add a new server instance, gce, virtualbox, and baremetal require server preparation.

##### addon  
* Add Kubernetes addon. Available addons is list below:
  * cm - Cocktail and Prometheus monitoring
  * km - Kube dashboard, Prometheus monitoring and Grafana

### Flags

##### public ip

* command: `node` sub command

* `--public-ip, -u`

* Public IP address of the node to be added.

* Requirement: Required if provider is gce, virtualbox, or baremetal; Unused if provider is aws or azure.

##### private ip

* command: `node` sub command

* `--private-ip, -u`

* Private IP address of the node to be added.

* Requirement: Required if provider is gce, virtualbox, or baremetal; Unused if provider is aws or azure.

##### node count
* command: `node` sub command

* `--node-count, -c`

* Total number of nodes (existing nodes and nodes to be added).

* Requirement: Required if provider is aws or azure; Unused if provider is gce, virtualbox, or baremetal.

##### addon name
* command: `addon` sub command

* `--name, -n`

* Addon name

