---
layout: default
title: Kubernetes 1.24 Installation
parent: Kubernetes
nav_order: 1
date: 2023-02-05
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>

<details>
<summary>Basic Kubernetes 1.24 Installation</summary>
{% highlight bash %}
# Install Packages
# Containerd is needed
# Before installing containerd load configuration files
# Disable swap
# Install dependency packages (apt-transport-https, curl,ca-certifcate, gnupg,  lsb-release)
# Install kubeadm, kubectl, kubelet
# Initialize the Cluster
Sudo kubeadm init --pod-network-cidr 1927.168.0.0/16 --kubernetes-version 1.23.0
# Kubeconfig
# Network Add On
# Calico, vpc, etc.
# Join worker nodes
# Sudo kubeadm join 

# Upgrading kubeadm refernce: https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/
# Upgrade the control plane
# Check the version
# Drain the control plane node
kubectl drain node-name --ignore-daemonsets
# Upgrade kubectl kubelet
		
# Uncordon the node
# Upgrade the worker node
# Upgrade kubeadm
# $ sudo kubeadm upgrade node
# Upgrade kubelet and kubectl
# Restart the service

# https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster
# Download etcd
# Run etcd
# etcd --listen-client-urls=http://$IP1:2379,http://$IP2:2379,http://$IP3:2379,http://$IP4:2379,http://$IP5:2379 --advertise-client-urls=http://$IP1:2379,http://$IP2:2379,http://$IP3:2379,http://$IP4:2379,http://$IP5:2379
# List etcd serveres
# ETCDCTL_API=3 etcdctl --endpoints 10.2.0.9:2379 \
  --cert=/etc/kubernetes/pki/etcd/server.crt \
  --key=/etc/kubernetes/pki/etcd/server.key \
  --cacert=/etc/kubernetes/pki/etcd/ca.crt \
  member list
# Backup etcd
# ETCDCTL_API=3etcdctl --endpoints $ENDPOINT snapshot save snapshotdb



## Worker Nodes
## From https://kubernetes.io/docs/setup/production-environment/container-runtimes/
# containerd preinstall configuration
cat <<EOF | sudo tee /etc/modules-load.d/containerd.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# Setup required sysctl params, these persist across reboots.
cat <<EOF | sudo tee /etc/sysctl.d/99-kubernetes-cri.conf
net.bridge.bridge-nf-call-iptables  = 1
net.ipv4.ip_forward                 = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system

# Install containerd
## Set up the repository
### Install packages to allow apt to use a repository over HTTPS
sudo apt-get update
sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    gnupg \
    lsb-release

OR 

Sudo apt-get update && sudo apt-get install -y containerd

## Add Docker’s official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

## Add Docker apt repository.
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

## Install packages
sudo apt-get update
sudo apt-get install -y \
  containerd.io

# Configure containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml

# Restart containerd
sudo systemctl restart containerd

Resource: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/

# Update the package
sudo apt-get update
# install needed packages
sudo apt-get install -y apt-transport-https ca-certificates curl
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
# Install kubelet, kubelet, and kubeadm
sudo apt-get install -y kubelet=1.20.7-00 kubeadm=1.20.7-00 kubectl=1.20.7-00
# stop automatic update for kubernetes packages
sudo apt-mark hold kubelet kubeadm kubectl

Resource: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/

# Switch to Control Plane and start kubeadm
$ sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --cri-socket unix:///run/containerd/containerd.sock

# copy and paste what kubeadm output says
$ sudo kubeadm join 172.16.1.11:6443 --token h8vno9.7eroqaei7v1isdpn \
    --discovery-token-ca-cert-hash sha256:44f1def2a041f116bc024f7e57cdc0cdcc8d8f36f0b942bdd27c7f864f645407 --cri-socket unix:///run/containerd/containerd.sock

# Configure kubectl access, add kubeconfig
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Deploy Flannel as a network plugin
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

$ kubectl get nodes



https://github.com/alijahnas/CKA-practice-exercises/blob/CKA-v1.20/cluster-architecture-installation-configuration.md#:~:text=https%3A//kubernetes.io/docs/tasks/administer%2Dcluster/kubeadm/kubeadm%2Dupgrade/

########## Switch to Master Node #########
# Upgrade kubeadm
sudo apt-mark unhold kubeadm
sudo apt-get update && sudo apt-get install -y kubeadm=1.21.1-00
sudo apt-mark hold kubeadm

# Upgrade controlplane node
kubectl drain k8s-controlplane --ignore-daemonsets
sudo kubeadm upgrade plan
sudo kubeadm upgrade apply v1.21.1

# Update Flannel
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

# Upgrade kubelet and kubectl
sudo apt-mark unhold kubelet kubectl
sudo apt-get update && sudo apt-get install -y kubelet=1.21.1-00 kubectl=1.21.1-00
sudo apt-mark hold kubelet kubectl
sudo systemctl daemon-reload
sudo systemctl restart kubelet

# Make master node reschedulable
kubectl uncordon k8s-controlplane

####### Swtich to Worker Node
# Upgrade kubeadm
sudo apt-mark unhold kubeadm
sudo apt-get update && sudo apt-get install -y kubeadm=1.21.1-00
sudo apt-mark hold kubeadm

# Upgrade worker node
kubectl drain k8s-node-1 --ignore-daemonsets
sudo kubeadm upgrade node

# Upgrade kubelet and kubectl
sudo apt-mark unhold kubelet kubectl
sudo apt-get update && sudo apt-get install -y kubelet=1.21.1-00 kubectl=1.21.1-00
sudo apt-mark hold kubelet kubectl
sudo systemctl daemon-reload
sudo systemctl restart kubelet

# Make worker node reschedulable
kubectl uncordon k8s-node-1

# Hold kubernetes from upgrading
sudo apt-mark hold kubeadm kubelet kubectl

# Upgrade node
kubectl drain k8s-node-1 --ignore-daemonsets
sudo apt update && sudo apt upgrade -y # Be careful about container runtime (e.g., docker) upgrade.

# Reboot node if necessary
sudo reboot

# Make worker node reschedulable
kubectl uncordon k8s-node-1



https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster

## Check the version
$ kubectl exec-it -n kube-system etcd-k8s-controlplane -- etcd --version
etcd Version: 3.4.13
Git SHA: ae9734ed2
Go Version: go1.12.17
Go OS/Arch: linux/amd64

##  Download etcd client
wget https://github.com/etcd-io/etcd/releases/download/v3.4.13/etcd-v3.4.13-linux-amd64.tar.gz
tar xzvf etcd-v3.4.13-linux-amd64.tar.gz
sudo mv etcd-v3.4.13-linux-amd64/etcdctl /usr/local/bin

## save etcd snapshot
sudo ETCDCTL_API=3 etcdctl snapshot save --endpoints 172.16.1.11:2379 snapshot.db --cacert /etc/kubernetes/pki/etcd/server.crt --cert /etc/kubernetes/pki/etcd/ca.crt --key /etc/kubernetes/pki/etcd/ca.key

# View the snapshot
ETCDCTL_API=3 sudo etcdctl --write-out=table snapshot status snapshot.db 
+----------+----------+------------+------------+
|   HASH   | REVISION | TOTAL KEYS | TOTAL SIZE |
+----------+----------+------------+------------+
| 4056f9fc |    18821 |        809 |     4.1 MB |
+----------+----------+------------+------------+

{% endhighlight %}
</details>