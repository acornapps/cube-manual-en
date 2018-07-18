# Adding a Node to an Existing Cluster

Follow the procedure below to add a worker node to an existing cluster.

You may not add master nodes. For bare-metal, specify the private and public IPs of the node to be added using the -r and -u options, respectively. For public clouds, enter the total number of worker nodes after the -c option.

```
// If adding a node to baremetal (private ip: 10.10.10.11, public ip: 192.168. 10.11)
# cube add node -r 10.10.10.11 -u 192.168.10.11

// If adding a total of three worker nodes to aws or azure
# cube add node -c 3
```

To verify whether an upgrade was completed successfully, query the node version via the cube status command.

```
# cube status
```



