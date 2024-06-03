# About

Project to create a local environment to run kubernetes server.

The vagrant creates a Kubernetes Cluster with N control planes and N workers.

To Control Plane works in HA, we need a loadbalancing to them. The haproxy will be this loadbalancig with a new instance for him.

All server configuration is made by Ansible.

# Prerequisites

- Vagrant
- Ansible

# Version


## Vagrantfile

Verify the Vagrantfile to get the version of all used components.

On Vagrantfile you can change the variables as needed.

```ssh
IMAGE_NAME = "ubuntu/jammy64" # ubuntu 22.04
QTD_CONTROL_PLANES = 2
QTD_WORKER_NODES = 2
```

IMAGE_NAME: Image name to Vagrant file

QTD_NODES: Quantity of worker nodes

## Ansible / Kubernetes Configuration

Edit the kubernetes-setup/vars.yml as needed


```yaml
# k8s_version: "1.30" # You can use this way to install the latest minor version
k8s_version: "1.30.1-1.1" # You can use this way to install exacly version
k8s_major_version: "1.30"
calico_version: "3.28.0"
```

K8S_VERSION: Version of kubelet/kubeadm/kubectl to install. You can use exacly version or Major and the script will select for you based on SO repository.

K8S_MAJOR_VERSION: Major kubernetes version (needs be based on K8S_VERSION minor)

CALICO_VERSION: Version of CALICO (Kubernetes Network Add-on)

# How to manage the environment

When the vagrant run, it will create the servers and provision the configuration.

Only needs to run:

## Starting environment

```ssh
vagrant up
```

or to be quickly, you can create simulteously the instances:

```ssh
vagrant up haproxy &
vagrant up controlplane-1 &
vagrant up controlplane-2 &
vagrant up node-1 &
vagrant up node-2
```


## Stop

```ssh
vagrant halt
```

## Suspend

```ssh
vagrant suspend
```

## Resume

```ssh
vagrant resume
```

## Clear / Destroy

```ssh
vagrant destroy
```

# Ansible

To configure the VM created by vagrant, run the ansible with the command:

```
ansible-playbook main.yml
```

## About Ansible

The ansible configuration is on ansible.cfg

You can check the inventory in hosts.ini file.

When the ansible runs, it will execute main.yml, who will call the kuebrentes-setup/k8s_setup.yml.

The playbooks called by kuebrentes-setup/k8s_setup.yml load the vars.yml.

haproxy.yml: Configure Control Plane Loadbalance with HAProxy

k8s_common.yml: Configure Kubernetes packages and CRI

k8s_control_plane.yml: Configure the First Control Plane

k8s_control_plane_join.yml: Configure Others Control Planes by kubeadm join command

k8s_worker_node.yml: Configure worker nodes by kubeadm command



# Kubectl

The ansible script configure the user vagrant inside k8s-master VM (Control Plane) and copy the configuration to admin.conf file.

## Test inside Control plane

Access the k8s-master VM

```ssh
vagrant ssh k8s-master
```

Execute the kubectl command

```ssh
vagrant@k8s-master:~$ kubectl  get nodes
NAME             STATUS   ROLES           AGE     VERSION
controlplane-1   Ready    control-plane   26m     v1.30.1
controlplane-2   Ready    control-plane   25s     v1.30.1
node-1           Ready    <none>          9m17s   v1.30.1
node-2           Ready    <none>          6m5s    v1.30.1
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
