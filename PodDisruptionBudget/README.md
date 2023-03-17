
### Enviornment Setup

We going to use k3d cluster with 4 nodes

```sh
k3d cluster create disruption01 -s 4
``` 
To test pod disruption budgets, we will deliberately drain a node and observe the termination of our pods. This could potentially cause downtime for some users, especially if traffic is high and even a single pod termination could result in downtime.

#####  Deploy microservices

```sh
kubectl apply -f manifest.yaml
```

##### Patch our main emoji service with podAntiAffinity so that all pods will spread on all nodes

```sh
kubectl patch deployment emoji -n emojivoto --type=json -p='[{"op": "add", "path": "/spec/template/spec/affinity", "value": {"podAntiAffinity": {"requiredDuringSchedulingIgnoredDuringExecution": [{"labelSelector": {"matchExpressions": [{"key": "app", "operator": "In", "values": ["emoji-svc"]}]}, "topologyKey": "kubernetes.io/hostname"}]}}}]'
```
