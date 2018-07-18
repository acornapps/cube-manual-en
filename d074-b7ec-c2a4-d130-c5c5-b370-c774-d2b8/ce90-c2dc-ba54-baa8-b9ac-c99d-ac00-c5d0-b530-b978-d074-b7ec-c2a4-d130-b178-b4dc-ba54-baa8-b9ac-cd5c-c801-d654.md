# Cluster Node Memory Optimization According to Increase in Cache Memory

The cache memory usage of nodes that constitute a cluster increases gradually.

When cluster memory utilization is shown to be more than the actual utilization in Cocktail, the cache memory is initialized by periodically executing the following command per node.

```
# sync; echo 1 > /proc/sys/vm/drop_caches  or
# sync; sysctl -w vm.drop_caches=1

```



