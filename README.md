# About

Project to create a local environment to run kubernetes server.

The vagrant creates a Kubernetes Cluster with the control plane and N workers.

All server configuration is made by Ansible.

# Prerequisites

- Vagrant
- Ansible

# Version

Verify the Vagrantfile to get the version of all used components.

On Vagrantfile you can change the variables as needed.

```ssh
IMAGE_NAME = "ubuntu/jammy64" # ubuntu 22.04
QTD_NODES = 2
# K8S_VERSION = "1.30" # You can use this way to install the latest minor version
K8S_VERSION = "1.30.1-1.1" # You can use this way to install exacly version
K8S_MAJOR_VERSION = "1.30"
CALICO_VERSION = "3.28.0"
```

IMAGE_NAME: Image name to Vagrant file

QTD_NODES: Quantity of worker nodes

K8S_VERSION: Version of kubelet/kubeadm/kubectl to install. You can use exacly version or Major

and the script will select for you based on SO repository.

K8S_MAJOR_VERSION: Major kubernetes version (needs be based on K8S_VERSION minor)

CALICO_VERSION: Version of CALICO (Kubernetes Network Add-on)

# How to manage the environment

When the vagrant run, it will create the servers and provision the configuration.

Only needs to run:

## Starting environment

```ssh
vagrant up
```

## Re-run

```ssh
vagrant provision
```

## Suspend

```ssh
vagrant suspend
```

## Resume

```ssh
vagrant suspend
```

## Clear / Destroy

```ssh
vagrant destroy
```

# Connecting on Kubernetes

The ansible script configure the user vagrant inside k8s-master VM (Control Plane) and copy the configuration to admin.conf file.

## Test on Control plane

Access the k8s-master VM

```ssh
vagrant ssh k8s-master
```

Execute the kubectl command

```ssh
vagrant@k8s-master:~$ kubectl  get nodes
NAME         STATUS   ROLES           AGE     VERSION
k8s-master   Ready    control-plane   12m     v1.30.1
node-1       Ready    <none>          9m17s   v1.30.1
node-2       Ready    <none>          6m5s    v1.30.1
```

## Test direct from your PC

Copy the admin.conf file to .kube folder to configure

```ssh
cp kubernetes-setup/admin.conf ~/.kube/config
```

Execute the kubectl command

```ssh
$ kubectl get pods -A
NAMESPACE         NAME                                 READY   STATUS    RESTARTS   AGE
kube-system       coredns-7db6d8ff4d-n55jj             1/1     Running   0          31m
kube-system       coredns-7db6d8ff4d-ssxhp             1/1     Running   0          31m
kube-system       etcd-k8s-master                      1/1     Running   0          31m
kube-system       kube-apiserver-k8s-master            1/1     Running   0          31m
kube-system       kube-controller-manager-k8s-master   1/1     Running   0          31m
kube-system       kube-proxy-48jdc                     1/1     Running   0          31m
kube-system       kube-proxy-5pr48                     1/1     Running   0          25m
kube-system       kube-proxy-vlgdx                     1/1     Running   0          28m
kube-system       kube-scheduler-k8s-master            1/1     Running   0          31m
tigera-operator   tigera-operator-76ff79f7fd-lshkn     1/1     Running   0          31
```
