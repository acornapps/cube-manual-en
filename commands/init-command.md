# `init` command

The init command prepares and initializes the scripts, settings files, and other required resources for the platform on which Kubernetes and Cocktail are to be installed.

---

### Usage

`cube init [flags]`

### Flags

##### provider

* `--provider, -p`

* Specifies the name of the platform on which Kubernetes and Cocktail will be installed.

* Requirement: Required

* The list of providers supported by the current version are as follows.

| flag 값 | Provider |
| :--- | :--- |
| baremetal | Bare metal machine |
| virtualbox | VirtualBox |
| aws | Amazon Web Service |
| azure | Microsoft Azure |
| gce | Google Compute Engine (not yet supported) |



