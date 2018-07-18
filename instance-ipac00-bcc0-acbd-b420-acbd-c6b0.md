# If the IP of a Cube-installed node changes

Currently, we recommend using Cube for reinstallation if the IP of a Cube-installed master or worker node changes.

This is because certain k8s components (api server, scheduler, and control manager) use IP certificate-based communications and the etcd flannel subnetwork stores IP information.





