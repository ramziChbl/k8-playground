# K8s Playground

Create a virtualized infrastructure to locally deploy multi-node kubernetes clusters using Vagrant.

This repo was created in parallel to my study of kubernetes to easily spin up an experimentation environment.

This is used for learning puposes only. To deploy a fully functional and tested production ready kubernetes cluster, there's the excellent [kubesrpay](https://kubespray.io).

## Vagrant

[Vagrant](https://www.vagrantup.com/) is currently used with [Virtualbox](https://www.virtualbox.org/) to create [Ubuntu 16.04](https://app.vagrantup.com/ubuntu/boxes/xenial64) nodes.


### Vagrantfile

Most of the cluster options are specified in the Vagrantfile :
 - Number of worker nodes
 - (TODO) Number of master nodes (not yet implemented HA)
 - Kubernetes distribution :
   1. Vanilla k8s with kubeadm
   2. K3s
 - (TODO) CNI to use

An option can be chosen by assigning a value to the appropriate variable in the first part of the Vagrantfile :

```
MASTER_NODES_NUMBER = 1
WORKER_NODES_NUMBER = 1

# Kubernetes distribution used to create the cluster
# Accepted values : kubernetes, k3s
K8_DISTRIBUTION = "k3s" 
```

## Tests

The following had been tested  :

| K8s Distribution | Master Nodes | Worker Nodes | CNI | Notes |
| --- | --- | --- | --- | --- |
| kubeadm | 1 | 2 | Flannel | Some prerequisites and configurations are required on the nodes to have a functional cluster |
| k3s | 1 | 2 | Flannel | Very easy to install. Only need to execute a script that takes care of everything |



## Roadmap
 - Try ingress controllers and load balancers : Nginx, Traefik.
 - Deploy Highly available clusters.
 - Use other CNIs : Calico, linkerd
 - Try other kubernetes distributions : Rancher.
 - Use different provisioners : Salt, Chef, Puppet.
 - Use libvirt instead of Virtualbox