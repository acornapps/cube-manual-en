# `destroy` command

The destroy command deletes Kubernetes cluster nodes and Cocktail.

---

### Prerequisite

Kubernetes and Cocktail already installed

### Usage

* `cube destroy [flags]`
* Only Cocktail is deleted if called without a flag.

### Flags

##### pre-destroy

* `--pre-destroy`

* When an aws or azure provider is deleted, the associated load balancers and volumes will also be deleted. **It must be called **.

* Requirement: Required

##### force

* `--force, -f`

* The nodes \(server instance\) are also deleted on aws and azure.

* Requirement: Optional



