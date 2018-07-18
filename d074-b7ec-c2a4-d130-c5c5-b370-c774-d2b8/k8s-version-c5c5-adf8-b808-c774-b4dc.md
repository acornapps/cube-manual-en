# Kubernetes cluster version upgrade

Follow the procedure below to upgrade Kubernetes in use with Cocktail.

Only upgrades are allowed; downgrades to a lower version are not possible. Currently, the following versions can be designated for upgrades: 1.8.13 or later, 1.9.x, and 1.10.x.

```
# cube upgrade -v version
```

To verify whether an upgrade was completed successfully, query the node version via the cube status command.

```
# cube status
```



