---
layout: default
title: Kubernetes 
has_children: true
permalink: /docs/Kubernetes
date: 2023-02-04
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>

# Objective 1: Cluster Architecture, Installation & Configuration

- [Objective 1: Cluster Architecture, Installation \& Configuration](#objective-1-cluster-architecture-installation--configuration)
  - [1.1 Manage Role Based Access Control (RBAC)](#11-manage-role-based-access-control-rbac)
    - [Lab Environment](#lab-environment)
    - [Lab Practice](#lab-practice)
  - [1.2 Use Kubeadm to Install a Basic Cluster](#12-use-kubeadm-to-install-a-basic-cluster)
    - [Kubeadm Tasks for All Nodes](#kubeadm-tasks-for-all-nodes)
    - [Kubeadm Tasks for Single Control Node](#kubeadm-tasks-for-single-control-node)
    - [Kubeadm Tasks for Worker Node(s)](#kubeadm-tasks-for-worker-nodes)
    - [Kubeadm Troubleshooting](#kubeadm-troubleshooting)
    - [Kubeadm Optional Tasks](#kubeadm-optional-tasks)
  - [1.3 Manage A Highly-Available Kubernetes Cluster](#13-manage-a-highly-available-kubernetes-cluster)
    - [HA Deployment Types](#ha-deployment-types)
    - [Upgrading from Single Control-Plane to High Availability](#upgrading-from-single-control-plane-to-high-availability)
  - [1.4 Provision Underlying Infrastructure to Deploy a Kubernetes Cluster](#14-provision-underlying-infrastructure-to-deploy-a-kubernetes-cluster)
  - [1.5 Perform a Version Upgrade on a Kubernetes Cluster using Kubeadm](#15-perform-a-version-upgrade-on-a-kubernetes-cluster-using-kubeadm)
    - [First Control Plane Node](#first-control-plane-node)
    - [Additional Control Plane Nodes](#additional-control-plane-nodes)
    - [Upgrade Control Plane Node Kubectl And Kubelet Tools](#upgrade-control-plane-node-kubectl-and-kubelet-tools)
    - [Upgrade Worker Nodes](#upgrade-worker-nodes)
  - [1.6 Implement Etcd Backup And Restore](#16-implement-etcd-backup-and-restore)
    - [Snapshot The Keyspace](#snapshot-the-keyspace)
    - [Restore From Snapshot](#restore-from-snapshot)
- [Objective 2: Workloads \& Scheduling](#objective-2-workloads--scheduling)
  - [2.1 Understand Deployments And How To Perform Rolling Update And Rollbacks](#21-understand-deployments-and-how-to-perform-rolling-update-and-rollbacks)
    - [Create Deployment](#create-deployment)
    - [Perform Rolling Update](#perform-rolling-update)
    - [Perform Rollbacks](#perform-rollbacks)
  - [2.2 Use Configmaps And Secrets To Configure Applications](#22-use-configmaps-and-secrets-to-configure-applications)
    - [Configmaps](#configmaps)
    - [Secrets](#secrets)
    - [Other Concepts](#other-concepts)
  - [2.3 Know How To Scale Applications](#23-know-how-to-scale-applications)
  - [2.4 Understand The Primitives Used To Create Robust, Self-Healing, Application Deployments](#24-understand-the-primitives-used-to-create-robust-self-healing-application-deployments)
  - [2.5 Understand How Resource Limits Can Affect Pod Scheduling](#25-understand-how-resource-limits-can-affect-pod-scheduling)
  - [2.6 Awareness Of Manifest Management And Common Templating Tools](#26-awareness-of-manifest-management-and-common-templating-tools)
- [Objective 3: Services \& Networking](#objective-3-services--networking)
  - [3.1 Understand Host Networking Configuration On The Cluster Nodes](#31-understand-host-networking-configuration-on-the-cluster-nodes)
  - [3.2 Understand Connectivity Between Pods](#32-understand-connectivity-between-pods)
  - [3.3 Understand ClusterIP, NodePort, LoadBalancer Service Types And Endpoints](#33-understand-clusterip-nodeport-loadbalancer-service-types-and-endpoints)
    - [ClusterIP](#clusterip)
    - [NodePort](#nodeport)
    - [LoadBalancer](#loadbalancer)
    - [ExternalIP](#externalip)
    - [ExternalName](#externalname)
    - [Networking Cleanup for Objective 3.3](#networking-cleanup-for-objective-33)
  - [3.4 Know How To Use Ingress Controllers And Ingress Resources](#34-know-how-to-use-ingress-controllers-and-ingress-resources)
  - [3.5 Know How To Configure And Use CoreDNS](#35-know-how-to-configure-and-use-coredns)
  - [3.6 Choose An Appropriate Container Network Interface Plugin](#36-choose-an-appropriate-container-network-interface-plugin)
- [Objective 4: Storage](#objective-4-storage)
  - [4.1 Understand Storage Classes, Persistent Volumes](#41-understand-storage-classes-persistent-volumes)
    - [Storage Classes](#storage-classes)
    - [Persistent Volumes](#persistent-volumes)
  - [4.2 Understand Volume Mode, Access Modes And Reclaim Policies For Volumes](#42-understand-volume-mode-access-modes-and-reclaim-policies-for-volumes)
    - [Volume Mode](#volume-mode)
    - [Access Modes](#access-modes)
    - [Reclaim Policies](#reclaim-policies)
  - [4.3 Understand Persistent Volume Claims Primitive](#43-understand-persistent-volume-claims-primitive)
  - [4.4 Know How To Configure Applications With Persistent Storage](#44-know-how-to-configure-applications-with-persistent-storage)
- [Objective 5: Troubleshooting](#objective-5-troubleshooting)
  - [5.1 Evaluate Cluster And Node Logging](#51-evaluate-cluster-and-node-logging)
    - [Cluster Logging](#cluster-logging)
    - [Node Logging](#node-logging)
  - [5.2 Understand How To Monitor Applications](#52-understand-how-to-monitor-applications)
  - [5.3 Manage Container Stdout \& Stderr Logs](#53-manage-container-stdout--stderr-logs)
  - [5.4 Troubleshoot Application Failure](#54-troubleshoot-application-failure)
  - [5.5 Troubleshoot Cluster Component Failure](#55-troubleshoot-cluster-component-failure)
  - [5.6 Troubleshoot Networking](#56-troubleshoot-networking)

## 1.1 Manage Role Based Access Control (RBAC)

Documentation and Resources:

- [Kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
- [Using RBAC Authorization](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)
- [A Practical Approach to Understanding Kubernetes Authorization](https://thenewstack.io/a-practical-approach-to-understanding-kubernetes-authorization/)

RBAC is handled by roles (permissions) and bindings (assignment of permissions to subjects):

| Object               | Description                                                                                  |
| -------------------- | -------------------------------------------------------------------------------------------- |
| `Role`               | Permissions within a particular namespace                                                    |
| `ClusterRole`        | Permissions to non-namespaced resources; can be used to grant the same permissions as a Role |
| `RoleBinding`        | Grants the permissions defined in a role to a user or set of users                           |
| `ClusterRoleBinding` | Grant permissions across a whole cluster                                                     |

### Lab Environment

If desired, use a managed Kubernetes cluster, such as Amazon EKS, to immediately begin working with RBAC. The command `aws --region REGION eks update-kubeconfig --name CLUSTERNAME` will generate a .kube configuration file on your workstation to permit kubectl commands.

### Lab Practice

Create the `wahlnetwork1` namespace.

`kubectl create namespace wahlnetwork1`

---

Create a deployment in the `wahlnetwork1` namespace using the image of your choice:

# `kubectl create deployment hello-node --image=k8s.gcr.io/echoserver:1.4 -n wahlnetwork1`
# `kubectl create deployment busybox --image=busybox -n wahlnetwork1 -- sleep 2000`

You can view the yaml file by adding `--dry-run=client -o yaml` to the end of either deployment.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: hello-node
  name: hello-node
  namespace: wahlnetwork1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: hello-node
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: hello-node
    spec:
      containers:
        - image: k8s.gcr.io/echoserver:1.4
          name: echoserver
          resources: {}
```

---

Create the `pod-reader` role in the `wahlnetwork1` namespace.

`kubectl create role pod-reader --verb=get --verb=list --verb=watch --resource=pods -n wahlnetwork1`

> Alternatively, use `kubectl create role pod-reader --verb=get --verb=list --verb=watch --resource=pods -n wahlnetwork1 --dry-run=client -o yaml` to output a proper yaml configuration.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  creationTimestamp: null
  name: pod-reader
  namespace: wahlnetwork1
rules:
  - apiGroups:
      - ""
    resources:
      - pods
    verbs:
      - get
      - list
      - watch
```

---

Create the `read-pods` rolebinding between the role named `pod-reader` and the user `spongebob` in the `wahlnetwork1` namespace.

`kubectl create rolebinding --role=pod-reader --user=spongebob read-pods -n wahlnetwork1`

> Alternatively, use `kubectl create rolebinding --role=pod-reader --user=spongebob read-pods -n wahlnetwork1 --dry-run=client -o yaml` to output a proper yaml configuration.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  creationTimestamp: null
  name: read-pods
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: pod-reader
subjects:
  - apiGroup: rbac.authorization.k8s.io
    kind: User
    name: spongebob
```

---

Create the `cluster-secrets-reader` clusterrole.

`kubectl create clusterrole cluster-secrets-reader --verb=get --verb=list --verb=watch --resource=secrets`

> Alternatively, use `kubectl create clusterrole cluster-secrets-reader --verb=get --verb=list --verb=watch --resource=secrets --dry-run=client -o yaml` to output a proper yaml configuration.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  creationTimestamp: null
  name: cluster-secrets-reader
rules:
- apiGroups:
  - ""
  resources:
  - secrets
  verbs:
  - get
  - list
  - watch
```

---

Create the `cluster-read-secrets` clusterrolebinding between the clusterrole named `cluster-secrets-reader` and the user `gizmo`.

`kubectl create clusterrolebinding --clusterrole=cluster-secrets-reader --user=gizmo cluster-read-secrets`

> Alternatively, use `kubectl create clusterrolebinding --clusterrole=cluster-secrets-reader --user=gizmo cluster-read-secrets --dry-run=client -o yaml` to output a proper yaml configuration.

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  creationTimestamp: null
  name: cluster-read-secrets
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-secrets-reader
subjects:
  - apiGroup: rbac.authorization.k8s.io
    kind: User
    name: gizmo
```

Test to see if this works by running the `auth` command.

`kubectl auth can-i get secrets --as=gizmo`

Attempt to get secrets as the `gizmo` user.

`kubectl get secrets --as=gizmo`

```bash
NAME                  TYPE                                  DATA   AGE
default-token-lz87v   kubernetes.io/service-account-token   3      7d1h
```

## 1.2 Use Kubeadm to Install a Basic Cluster

Official documentation: [Creating a cluster with kubeadm](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/)

> Terraform code is available [here](../code/tf-cluster-asg/) to create the resources necessary to experiment with `kubeadm`

### Kubeadm Tasks for All Nodes

- Create Amazon EC2 Instances
  - Create an AWS Launch Template using an Ubuntu 18.04 LTS image (or newer) of size `t3a.small` (2 CPU, 2 GiB Memory).
  - Disable the [swap](https://askubuntu.com/questions/214805/how-do-i-disable-swap) file.
    - Note: This can be validated by using the console command `free` when SSH'd to the instance. The swap space total should be 0.
  - Consume this template as part of an Auto Scaling Group of 1 or more instances. This makes deployment of new instances and removal of old instances trivial.
- [Configure iptables](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#letting-iptables-see-bridged-traffic)
  - This allows iptables to see bridged traffic.
- [Install the Docker container runtime](https://kubernetes.io/docs/setup/production-environment/container-runtimes/#docker)
  - The [docker-install](https://github.com/docker/docker-install) script is handy for this.
- [Install kubeadm, kubelet, and kubectl](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl)

Alternatively, use a `user-data` bash script attached to the Launch Template:

```bash
#!/bin/bash

# Disable Swap
sudo swapoff -a

# Bridge Network
sudo modprobe br_netfilter
sudo cat <<'EOF' | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sudo sysctl --system

# Install Docker
sudo curl -fsSL https://get.docker.com -o /home/ubuntu/get-docker.sh
sudo sh /home/ubuntu/get-docker.sh

# Install Kube tools
sudo apt-get update && sudo apt-get install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<'EOF' | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl
```

Optionally, add `sudo kubeadm config images pull` to the end of the script to pre-pull images required for setting up a Kubernetes cluster.

```bash
$ sudo kubeadm config images pull

[config/images] Pulled k8s.gcr.io/kube-apiserver:v1.19.2
[config/images] Pulled k8s.gcr.io/kube-controller-manager:v1.19.2
[config/images] Pulled k8s.gcr.io/kube-scheduler:v1.19.2
[config/images] Pulled k8s.gcr.io/kube-proxy:v1.19.2
[config/images] Pulled k8s.gcr.io/pause:3.2
[config/images] Pulled k8s.gcr.io/etcd:3.4.13-0
[config/images] Pulled k8s.gcr.io/coredns:1.7.0
```

### Kubeadm Tasks for Single Control Node

- Initialize the cluster
  - Choose your Container Network Interface (CNI) plugin. This guide uses [Calico's CNI](https://docs.projectcalico.org/about/about-calico).
  - Run `sudo kubeadm init --pod-network-cidr=192.168.0.0/16` to initialize the cluster and provide a pod network aligned to [Calico's default configuration](https://docs.projectcalico.org/getting-started/kubernetes/quickstart#create-a-single-host-kubernetes-cluster).
  - Write down the `kubeadm join` output to [join worker nodes](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#join-nodes) later in this guide.
    - Example `kubeadm join 10.0.0.100:6443 --token 12345678901234567890 --discovery-token-ca-cert-hash sha256:123456789012345678901234567890123456789012345678901234567890`
- [Install Calico](https://docs.projectcalico.org/getting-started/kubernetes/quickstart)
- [Configure local kubectl access](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#optional-controlling-your-cluster-from-machines-other-than-the-control-plane-node)
  - This step simply copies the `admin.conf` file into a location accessible for a regular user.

Alternatively, use the [Flannel CNI](https://coreos.com/flannel/docs/latest/kubernetes.html).

- Run `sudo kubeadm init --pod-network-cidr=10.244.0.0/16` to initialize the cluster and provide a pod network aligned to [Flannel's default configuration](https://github.com/coreos/flannel/blob/master/Documentation/kubernetes.md).
  - Note: The [`kube-flannel.yml`](https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml) file is hosted in the same location.

### Kubeadm Tasks for Worker Node(s)

- [Join the cluster](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#join-nodes)
  - Note: You can view the cluster config with `kubectl config view`. This includes the cluster server address (e.g. `server: https://10.0.0.100:6443`)

### Kubeadm Troubleshooting

- If using `kubeadm init` without a pod network CIDR the CoreDNS pods will remain [stuck in pending state](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/troubleshooting-kubeadm/#coredns-or-kube-dns-is-stuck-in-the-pending-state)
- Broke cluster and want to start over? Use `kubeadm reset` and `rm -rf .kube` in the user home directory to remove the old config and avoid [TLS certificate errors](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/troubleshooting-kubeadm/#tls-certificate-errors)
- If seeing `error: error loading config file "/etc/kubernetes/admin.conf": open /etc/kubernetes/admin.conf: permission denied` it likely means the `KUBECONFIG` variable is set to that path, try `unset KUBECONFIG` to use the `$HOME/.kube/config` file.

### Kubeadm Optional Tasks

- [Install kubectl client locally on Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-windows) for those using this OS.
- Single node cluster? [Taint the control node](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/) to accept pods without dedicated worker nodes.
- Deploy the "hello-node" app from the [minikube tutorial](https://kubernetes.io/docs/tutorials/hello-minikube/) to test basic functionality.

## 1.3 Manage A Highly-Available Kubernetes Cluster

[High Availability Production Environment](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/)

Kubernetes Components for HA:

- Load Balancer / VIP
- DNS records
- etcd Endpoint
- Certificates
- Any HA specific queries / configuration / settings

### HA Deployment Types

- With stacked control plane nodes. This approach requires less infrastructure. The etcd members and control plane nodes are co-located.
- With an external etcd cluster. This approach requires more infrastructure. The control plane nodes and etcd members are separated. ([source](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/high-availability/))

### Upgrading from Single Control-Plane to High Availability

If you have plans to upgrade this single control-plane kubeadm cluster to high availability you should specify the --control-plane-endpoint to set the shared endpoint for all control-plane nodes. Such an endpoint can be either a DNS name or an IP address of a load-balancer. ([source](https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/#initializing-your-control-plane-node))

## 1.4 Provision Underlying Infrastructure to Deploy a Kubernetes Cluster

See Objective [1.2 Use Kubeadm to Install a Basic Cluster](#12-use-kubeadm-to-install-a-basic-cluster).

> Note: Make sure that swap is disabled on all nodes.

## 1.5 Perform a Version Upgrade on a Kubernetes Cluster using Kubeadm

- [Upgrading kubeadm clusters](https://kubernetes.io/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/)
- [Safely Drain a Node while Respecting the PodDisruptionBudget](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)
- [Cluster Management: Maintenance on a Node](https://kubernetes.io/docs/tasks/administer-cluster/cluster-management/#maintenance-on-a-node)

> Note: All containers are restarted after upgrade, because the container spec hash value is changed. Upgrades are constrained from one minor version to the next minor version.

### First Control Plane Node

Update the kubeadm tool and verify the new version

> Note: The `--allow-change-held-packages` flag is used because kubeadm updates should be held to prevent automated updates.

```bash
apt-get update && \
apt-get install -y --allow-change-held-packages kubeadm=1.19.x-00
kubeadm version
```

---

[Drain](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#drain) the node to mark as unschedulable

`kubectl drain $NODENAME --ignore-daemonsets`

<details><summary>Drain Diagram</summary>

![drain](https://kubernetes.io/images/docs/kubectl_drain.svg)

</details>

---

Perform an upgrade plan to validate that your cluster can be upgraded

> Note: This also fetches the versions you can upgrade to and shows a table with the component config version states.

`sudo kubeadm upgrade plan`

---

Upgrade the cluster

`sudo kubeadm upgrade apply v1.19.x`

---

[Uncordon](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#uncordon) the node to mark as schedulable

`kubectl uncordon $NODENAME`

### Additional Control Plane Nodes

Repeat the first control plane node steps while replacing the "upgrade the cluster" step using the command below:

`sudo kubeadm upgrade node`

### Upgrade Control Plane Node Kubectl And Kubelet Tools

Upgrade the kubelet and kubectl on all control plane nodes

```bash
apt-get update && \
apt-get install -y --allow-change-held-packages kubelet=1.19.x-00 kubectl=1.19.x-00
```

---

Restart the kubelet

```bash
sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

### Upgrade Worker Nodes

Upgrade kubeadm

```bash
apt-get update && \
apt-get install -y --allow-change-held-packages kubeadm=1.19.x-00
```

---

Drain the node

`kubectl drain $NODENAME --ignore-daemonsets`

---

Upgrade the kubelet configuration

`sudo kubeadm upgrade node`

---

Upgrade kubelet and kubectl

```bash
apt-get update && \
apt-get install -y --allow-change-held-packages kubelet=1.19.x-00 kubectl=1.19.x-00

sudo systemctl daemon-reload
sudo systemctl restart kubelet
```

---

Uncordon the node

`kubectl uncordon $NODENAME`

## 1.6 Implement Etcd Backup And Restore

- [Operating etcd clusters for Kubernetes: Backing up an etcd cluster](https://kubernetes.io/docs/tasks/administer-cluster/configure-upgrade-etcd/#backing-up-an-etcd-cluster)
- [Etcd Documentation: Disaster Recovery](https://etcd.io/docs/v3.4.0/op-guide/recovery/)
- [Kubernetes Tips: Backup and Restore Etcd](https://medium.com/better-programming/kubernetes-tips-backup-and-restore-etcd-97fe12e56c57)

### Snapshot The Keyspace

Use `etcdctl snapshot save`.

Snapshot the keyspace served by \$ENDPOINT to the file snapshot.db:

`ETCDCTL_API=3 etcdctl --endpoints $ENDPOINT snapshot save snapshot.db`

### Restore From Snapshot

Use `etcdctl snapshot restore`.

> Note: Restoring overwrites some snapshot metadata (specifically, the member ID and cluster ID); the member loses its former identity.
>
> Note: Snapshot integrity is verified when restoring from a snapshot using an integrity hash created by `etcdctl snapshot save`, but not when restoring from a file copy.

Create new etcd data directories (m1.etcd, m2.etcd, m3.etcd) for a three member cluster:

```bash
$ ETCDCTL_API=3 etcdctl snapshot restore snapshot.db \
  --name m1 \
  --initial-cluster m1=http://host1:2380,m2=http://host2:2380,m3=http://host3:2380 \
  --initial-cluster-token etcd-cluster-1 \
  --initial-advertise-peer-urls http://host1:2380
$ ETCDCTL_API=3 etcdctl snapshot restore snapshot.db \
  --name m2 \
  --initial-cluster m1=http://host1:2380,m2=http://host2:2380,m3=http://host3:2380 \
  --initial-cluster-token etcd-cluster-1 \
  --initial-advertise-peer-urls http://host2:2380
$ ETCDCTL_API=3 etcdctl snapshot restore snapshot.db \
  --name m3 \
  --initial-cluster m1=http://host1:2380,m2=http://host2:2380,m3=http://host3:2380 \
  --initial-cluster-token etcd-cluster-1 \
  --initial-advertise-peer-urls http://host3:2380
```

# Objective 2: Workloads & Scheduling

- [Objective 1: Cluster Architecture, Installation \& Configuration](#objective-1-cluster-architecture-installation--configuration)
  - [1.1 Manage Role Based Access Control (RBAC)](#11-manage-role-based-access-control-rbac)
    - [Lab Environment](#lab-environment)
    - [Lab Practice](#lab-practice)
  - [1.2 Use Kubeadm to Install a Basic Cluster](#12-use-kubeadm-to-install-a-basic-cluster)
    - [Kubeadm Tasks for All Nodes](#kubeadm-tasks-for-all-nodes)
    - [Kubeadm Tasks for Single Control Node](#kubeadm-tasks-for-single-control-node)
    - [Kubeadm Tasks for Worker Node(s)](#kubeadm-tasks-for-worker-nodes)
    - [Kubeadm Troubleshooting](#kubeadm-troubleshooting)
    - [Kubeadm Optional Tasks](#kubeadm-optional-tasks)
  - [1.3 Manage A Highly-Available Kubernetes Cluster](#13-manage-a-highly-available-kubernetes-cluster)
    - [HA Deployment Types](#ha-deployment-types)
    - [Upgrading from Single Control-Plane to High Availability](#upgrading-from-single-control-plane-to-high-availability)
  - [1.4 Provision Underlying Infrastructure to Deploy a Kubernetes Cluster](#14-provision-underlying-infrastructure-to-deploy-a-kubernetes-cluster)
  - [1.5 Perform a Version Upgrade on a Kubernetes Cluster using Kubeadm](#15-perform-a-version-upgrade-on-a-kubernetes-cluster-using-kubeadm)
    - [First Control Plane Node](#first-control-plane-node)
    - [Additional Control Plane Nodes](#additional-control-plane-nodes)
    - [Upgrade Control Plane Node Kubectl And Kubelet Tools](#upgrade-control-plane-node-kubectl-and-kubelet-tools)
    - [Upgrade Worker Nodes](#upgrade-worker-nodes)
  - [1.6 Implement Etcd Backup And Restore](#16-implement-etcd-backup-and-restore)
    - [Snapshot The Keyspace](#snapshot-the-keyspace)
    - [Restore From Snapshot](#restore-from-snapshot)
- [Objective 2: Workloads \& Scheduling](#objective-2-workloads--scheduling)
  - [2.1 Understand Deployments And How To Perform Rolling Update And Rollbacks](#21-understand-deployments-and-how-to-perform-rolling-update-and-rollbacks)
    - [Create Deployment](#create-deployment)
    - [Perform Rolling Update](#perform-rolling-update)
    - [Perform Rollbacks](#perform-rollbacks)
  - [2.2 Use Configmaps And Secrets To Configure Applications](#22-use-configmaps-and-secrets-to-configure-applications)
    - [Configmaps](#configmaps)
    - [Secrets](#secrets)
    - [Other Concepts](#other-concepts)
  - [2.3 Know How To Scale Applications](#23-know-how-to-scale-applications)
  - [2.4 Understand The Primitives Used To Create Robust, Self-Healing, Application Deployments](#24-understand-the-primitives-used-to-create-robust-self-healing-application-deployments)
  - [2.5 Understand How Resource Limits Can Affect Pod Scheduling](#25-understand-how-resource-limits-can-affect-pod-scheduling)
  - [2.6 Awareness Of Manifest Management And Common Templating Tools](#26-awareness-of-manifest-management-and-common-templating-tools)
- [Objective 3: Services \& Networking](#objective-3-services--networking)
  - [3.1 Understand Host Networking Configuration On The Cluster Nodes](#31-understand-host-networking-configuration-on-the-cluster-nodes)
  - [3.2 Understand Connectivity Between Pods](#32-understand-connectivity-between-pods)
  - [3.3 Understand ClusterIP, NodePort, LoadBalancer Service Types And Endpoints](#33-understand-clusterip-nodeport-loadbalancer-service-types-and-endpoints)
    - [ClusterIP](#clusterip)
    - [NodePort](#nodeport)
    - [LoadBalancer](#loadbalancer)
    - [ExternalIP](#externalip)
    - [ExternalName](#externalname)
    - [Networking Cleanup for Objective 3.3](#networking-cleanup-for-objective-33)
  - [3.4 Know How To Use Ingress Controllers And Ingress Resources](#34-know-how-to-use-ingress-controllers-and-ingress-resources)
  - [3.5 Know How To Configure And Use CoreDNS](#35-know-how-to-configure-and-use-coredns)
  - [3.6 Choose An Appropriate Container Network Interface Plugin](#36-choose-an-appropriate-container-network-interface-plugin)
- [Objective 4: Storage](#objective-4-storage)
  - [4.1 Understand Storage Classes, Persistent Volumes](#41-understand-storage-classes-persistent-volumes)
    - [Storage Classes](#storage-classes)
    - [Persistent Volumes](#persistent-volumes)
  - [4.2 Understand Volume Mode, Access Modes And Reclaim Policies For Volumes](#42-understand-volume-mode-access-modes-and-reclaim-policies-for-volumes)
    - [Volume Mode](#volume-mode)
    - [Access Modes](#access-modes)
    - [Reclaim Policies](#reclaim-policies)
  - [4.3 Understand Persistent Volume Claims Primitive](#43-understand-persistent-volume-claims-primitive)
  - [4.4 Know How To Configure Applications With Persistent Storage](#44-know-how-to-configure-applications-with-persistent-storage)
- [Objective 5: Troubleshooting](#objective-5-troubleshooting)
  - [5.1 Evaluate Cluster And Node Logging](#51-evaluate-cluster-and-node-logging)
    - [Cluster Logging](#cluster-logging)
    - [Node Logging](#node-logging)
  - [5.2 Understand How To Monitor Applications](#52-understand-how-to-monitor-applications)
  - [5.3 Manage Container Stdout \& Stderr Logs](#53-manage-container-stdout--stderr-logs)
  - [5.4 Troubleshoot Application Failure](#54-troubleshoot-application-failure)
  - [5.5 Troubleshoot Cluster Component Failure](#55-troubleshoot-cluster-component-failure)
  - [5.6 Troubleshoot Networking](#56-troubleshoot-networking)

## 2.1 Understand Deployments And How To Perform Rolling Update And Rollbacks

[Official Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#use-case)

Deployments are used to manage Pods and ReplicaSets in a declarative manner.

### Create Deployment

Using the [nginx](https://hub.docker.com/_/nginx) image on Docker Hub, we can use a Deployment to push any number of replicas of that image to the cluster.

Create the `nginx` deployment in the `wahlnetwork1` namespace.

`kubectl create deployment nginx --image=nginx --replicas=3 -n wahlnetwork1`

> Alternatively, use `kubectl create deployment nginx --image=nginx --replicas=3 -n wahlnetwork1 --dry-run=client -o yaml` to output a proper yaml configuration.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: nginx
  name: nginx
  namespace: wahlnetwork1
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: nginx
    spec:
      containers:
        - image: nginx
          name: nginx
          resources: {}
```

### Perform Rolling Update

[Official Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#updating-a-deployment)

Used to make changes to the pod's template and roll them out to the cluster. Triggered when data within `.spec.template` is changed.

Update the `nginx` deployment in the `wahlnetwork1` namespace to use version `1.16.1`

`kubectl set image deployment/nginx nginx=nginx:1.16.1 -n wahlnetwork1 --record`

Track the rollout status.

`kubectl rollout status deployment.v1.apps/nginx -n wahlnetwork1`

```bash
Waiting for deployment "nginx" rollout to finish: 1 out of 2 new replicas have been updated...
Waiting for deployment "nginx" rollout to finish: 1 out of 2 new replicas have been updated...
Waiting for deployment "nginx" rollout to finish: 1 out of 2 new replicas have been updated...
Waiting for deployment "nginx" rollout to finish: 1 old replicas are pending termination...
Waiting for deployment "nginx" rollout to finish: 1 old replicas are pending termination...
deployment "nginx" successfully rolled out
```

### Perform Rollbacks

[Official Documentation](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#rolling-back-a-deployment)

Rollbacks offer a method for reverting the changes to a pod's `.spec.template` data to a previous version. By default, executing the `rollout undo` command will revert to the previous version. The desired version can also be declared.

Review the version history for the `nginx` deployment in the `wahlnetwork1` namespace. In this scenario, other revisions 1-4 have been made to simulate a deployment lifecycle. The 4th revision specifies a fake image version of `1.222222222222` to force a rolling update failure.

`kubectl rollout history deployment.v1.apps/nginx -n wahlnetwork1`

```bash
deployment.apps/nginx
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl.exe set image deployment/nginx nginx=nginx:1.16.1 --record=true --namespace=wahlnetwork1
3         kubectl.exe set image deployment/nginx nginx=nginx:1.14.1 --record=true --namespace=wahlnetwork1
4         kubectl.exe set image deployment/nginx nginx=nginx:1.222222222222 --record=true --namespace=wahlnetwork1
```

Revert to the previous version of the `nginx` deployment to use image version `1.14.1`. This forces revision 3 to become revision # Note that revision 3 no longer exists.

`kubectl rollout undo deployment.v1.apps/nginx -n wahlnetwork1`

```bash
deployment.apps/nginx rolled back

~ kubectl rollout history deployment.v1.apps/nginx -n wahlnetwork1

deployment.apps/nginx
REVISION  CHANGE-CAUSE
1         <none>
2         kubectl.exe set image deployment/nginx nginx=nginx:1.16.1 --record=true --namespace=wahlnetwork1
4         kubectl.exe set image deployment/nginx nginx=nginx:1.222222222222 --record=true --namespace=wahlnetwork1
5         kubectl.exe set image deployment/nginx nginx=nginx:1.14.1 --record=true --namespace=wahlnetwork1
```

Revert to revision 2 of the `nginx` deployment, which becomes revision 6 (the next available revision number). Note that revision 2 no longer exists.

`kubectl rollout undo deployment.v1.apps/nginx -n wahlnetwork1 --to-revision=2`

```bash
~ kubectl rollout history deployment.v1.apps/nginx -n wahlnetwork1

deployment.apps/nginx
REVISION  CHANGE-CAUSE
1         <none>
4         kubectl.exe set image deployment/nginx nginx=nginx:1.222222222222 --record=true --namespace=wahlnetwork1
5         kubectl.exe set image deployment/nginx nginx=nginx:1.14.1 --record=true --namespace=wahlnetwork1
6         kubectl.exe set image deployment/nginx nginx=nginx:1.16.1 --record=true --namespace=wahlnetwork1
```

## 2.2 Use Configmaps And Secrets To Configure Applications

### Configmaps

API object used to store non-confidential data in key-value pairs

- [Official Documentation](https://kubernetes.io/docs/concepts/configuration/configmap/)
  [Configure a Pod to Use a ConfigMap](https://kubernetes.io/docs/tasks/configure-pod-container/configure-pod-configmap/)

Create a configmap named `game-config` using a directory.

`kubectl create configmap game-config --from-file=/code/configmap/`

```bash
~ k describe configmap game-config

Name:         game-config
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
game.properties:
----
enemies=aliens
lives=3
enemies.cheat=true
enemies.cheat.level=noGoodRotten
secret.code.passphrase=UUDDLRLRBABAS
secret.code.allowed=true
secret.code.lives=30

ui.properties:
----
color.good=purple
color.bad=yellow
allow.textmode=true
how.nice.to.look=fairlyNice

Events:  <none>
```

Create a configmap named `game-config` using a file.

`kubectl create configmap game-config-2 --from-file=/code/configmap/game.properties`

Create a configmap named `game-config` using an env-file.

`kubectl create configmap game-config-env-file --from-env-file=/code/configmap/game-env-file.properties`

Create a configmap named `special-config` using a literal key/value pair.

`kubectl create configmap special-config --from-literal=special.how=very`

Edit a configmap named `game-config`.

`kubectl edit configmap game-config`

Get a configmap named `game-config` and output the response into yaml.

`kubectl get configmaps game-config -o yaml`

Use a configmap with a pod by declaring a value for `.spec.containers.env.name.valueFrom.configMapKeyRef`.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: ["/bin/sh", "-c", "env"]
      env:
        # Define the environment variable
        - name: SPECIAL_LEVEL_KEY
          valueFrom:
            configMapKeyRef:
              # The ConfigMap containing the value you want to assign to SPECIAL_LEVEL_KEY
              name: special-config
              # Specify the key associated with the value
              key: special.how
  restartPolicy: Never
```

Investigate the configmap value `very` from the key `SPECIAL_LEVEL_KEY` by reviewing the logs for the pod or by connecting to the pod directly.

`kubectl exec -n wahlnetwork1 --stdin nginx-6889dfccd5-msmn8 --tty -- /bin/bash`

```bash
~ kubectl logs dapi-test-pod

KUBERNETES_SERVICE_PORT=443
KUBERNETES_PORT=tcp://10.96.0.1:443
HOSTNAME=dapi-test-pod
SHLVL=1
HOME=/root
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_PROTO=tcp
SPECIAL_LEVEL_KEY=very
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_SERVICE_PORT_HTTPS=443
PWD=/
KUBERNETES_SERVICE_HOST=10.96.0.1
```

### Secrets

- [Managing Secret using kubectl](https://kubernetes.io/docs/tasks/configmap-secret/managing-secret-using-kubectl/)
- [Using Secrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-secrets)

Create a secret named `db-user-pass` using files.

```bash
kubectl create secret generic db-user-pass `
  --from-file=./username.txt `
  --from-file=./password.txt
```

The key name can be modified by inserting a key name into the file path. For example, setting the key names to `funusername` and `funpassword` can be done as shown below:

```bash
kubectl create secret generic fundb-user-pass `
  --from-file=funusername=./username.txt `
  --from-file=funpassword=./password.txt
```

Check to make sure the key names matches the defined names.

`kubectl describe secret fundb-user-pass`

```bash
Name:         fundb-user-pass
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
funpassword:  14 bytes
funusername:  7 bytes
```

Get secret values from `db-user-pass`.

`kubectl get secret db-user-pass -o jsonpath='{.data}'`

Edit secret values using the `edit` command.

`kubectl edit secrets db-user-pass`

```yaml
apiVersion: v1
data:
  password.txt: PASSWORD
  username.txt: USERNAME
kind: Secret
metadata:
  creationTimestamp: "2020-10-13T22:48:27Z"
  name: db-user-pass
  namespace: default
  resourceVersion: "1022459"
  selfLink: /api/v1/namespaces/default/secrets/db-user-pass
  uid: 6bb24810-dd33-4b92-9a37-424f3c7553b6
type: Opaque
```

Use a secret with a pod by declaring a value for `.spec.containers.env.name.valueFrom.secretKeyRef`.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: secret-env-pod
spec:
  containers:
    - name: mycontainer
      image: redis
      env:
        - name: SECRET_USERNAME
          valueFrom:
            secretKeyRef:
              name: mysecret
              key: username
        - name: SECRET_PASSWORD
          valueFrom:
            secretKeyRef:
              name: mysecret
              key: password
  restartPolicy: Never
```

### Other Concepts

- [Using imagePullSecrets](https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets)

## 2.3 Know How To Scale Applications

Scaling is accomplished by changing the number of replicas in a Deployment.

- [Running Multiple Instances of Your App](https://kubernetes.io/docs/tutorials/kubernetes-basics/scale/scale-intro/)

Scale a deployment named `nginx` from 3 to 4 replicas.

`kubectl scale deployments/nginx --replicas=4`

## 2.4 Understand The Primitives Used To Create Robust, Self-Healing, Application Deployments

- Don't use naked Pods (that is, Pods not bound to a ReplicaSet or Deployment) if you can avoid it. Naked Pods will not be rescheduled in the event of a node failure. ([source](https://kubernetes.io/docs/concepts/configuration/overview/#naked-pods-vs-replicasets-deployments-and-jobs))
- A Deployment, which both creates a ReplicaSet to ensure that the desired number of Pods is always available, and specifies a strategy to replace Pods (such as RollingUpdate), is almost always preferable to creating Pods directly, except for some explicit `restartPolicy: Never` scenarios. A Job may also be appropriate. ([source](https://kubernetes.io/docs/concepts/configuration/overview/#naked-pods-vs-replicasets-deployments-and-jobs))
- Define and use labels that identify semantic attributes of your application or Deployment, such as `{ app: myapp, tier: frontend, phase: test, deployment: v3 }`. ([source](https://kubernetes.io/docs/concepts/configuration/overview/#using-labels))

## 2.5 Understand How Resource Limits Can Affect Pod Scheduling

Resource limits are a mechanism to control the amount of resources needed by a container. This commonly translates into CPU and memory limits.

- Limits set an upper boundary on the amount of resources a container is allowed to consume from the host.
- Requests set an upper boundary on the amount of resources a container is allowed to consume from the host.
- If a limit is set without a request, the request value is set to equal the limit value.
- [Managing Resources for Containers](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/)
- [Resource Quotas](https://kubernetes.io/docs/concepts/policy/resource-quotas/)

Here is an example of pod configured with resource requests and limits.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: frontend
spec:
  containers:
    - name: app
      image: images.my-company.example/app:v4
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m"
    - name: log-aggregator
      image: images.my-company.example/log-aggregator:v6
      resources:
        requests:
          memory: "64Mi"
          cpu: "250m"
        limits:
          memory: "128Mi"
          cpu: "500m"
```

## 2.6 Awareness Of Manifest Management And Common Templating Tools

- [Templating YAML in Kubernetes with real code](https://learnk8s.io/templating-yaml-with-code)
- [yq](https://github.com/kislyuk/yq): Command-line YAML/XML processor
- [kustomize](https://github.com/kubernetes-sigs/kustomize): lets you customize raw, template-free YAML files for multiple purposes, leaving the original YAML untouched and usable as is.
- [Helm](https://github.com/helm/helm): A tool for managing Charts. Charts are packages of pre-configured Kubernetes resources.

# Objective 3: Services & Networking

- [Objective 3: Services & Networking](#objective-3-services--networking)
  - [3.1 Understand Host Networking Configuration On The Cluster Nodes](#31-understand-host-networking-configuration-on-the-cluster-nodes)
  - [3.2 Understand Connectivity Between Pods](#32-understand-connectivity-between-pods)
  - [3.3 Understand ClusterIP, NodePort, LoadBalancer Service Types And Endpoints](#33-understand-clusterip-nodeport-loadbalancer-service-types-and-endpoints)
    - [ClusterIP](#clusterip)
    - [NodePort](#nodeport)
    - [LoadBalancer](#loadbalancer)
    - [ExternalIP](#externalip)
    - [ExternalName](#externalname)
    - [Networking Cleanup for Objective 3.3](#networking-cleanup-for-objective-33)
  - [3.4 Know How To Use Ingress Controllers And Ingress Resources](#34-know-how-to-use-ingress-controllers-and-ingress-resources)
  - [3.5 Know How To Configure And Use CoreDNS](#35-know-how-to-configure-and-use-coredns)
  - [3.6 Choose An Appropriate Container Network Interface Plugin](#36-choose-an-appropriate-container-network-interface-plugin)

> Note: If you need access to the pod network while working through the networking examples, use the [Get a Shell to a Running Container](https://kubernetes.io/docs/tasks/debug-application-cluster/get-shell-running-container/) guide to deploy a shell container. I often like to have a tab open to the shell container to run arbitrary network commands without the need to `exec` in and out of it repeatedly.

## 3.1 Understand Host Networking Configuration On The Cluster Nodes

- Design

  - All nodes can talk
  - All pods can talk (without NAT)
  - Every pod gets a unique IP address

- Network Types

  - Pod Network
  - Node Network
  - Services Network
    - Rewrites egress traffic destined to a service network endpoint with a pod network IP address

- Proxy Modes
  - IPTables Mode
    - The standard mode
    - `kube-proxy` watches the Kubernetes control plane for the addition and removal of Service and Endpoint objects
    - For each Service, it installs iptables rules, which capture traffic to the Service's clusterIP and port, and redirect that traffic to one of the Service's backend sets.
    - For each Endpoint object, it installs iptables rules which select a backend Pod.
    - [Official Documentation](https://kubernetes.io/docs/concepts/services-networking/service/#proxy-mode-iptables)
    - [Kubernetes Networking Demystified: A Brief Guide](https://www.stackrox.com/post/2020/01/kubernetes-networking-demystified/)
  - IPVS Mode
    - Since 1.11
    - Linux IP Virtual Server (IPVS)
    - L4 load balancer

## 3.2 Understand Connectivity Between Pods

[Official Documentation](https://kubernetes.io/docs/concepts/cluster-administration/networking/)

Read [The Kubernetes network model](https://kubernetes.io/docs/concepts/cluster-administration/networking/#the-kubernetes-network-model):

- Every pod gets its own address
- Fundamental requirements on any networking implementation
  - Pods on a node can communicate with all pods on all nodes without NAT
  - Agents on a node (e.g. system daemons, kubelet) can communicate with all pods on that node
  - Pods in the host network of a node can communicate with all pods on all nodes without NAT
- Kubernetes IP addresses exist at the Pod scope
  - Containers within a pod can communicate with one another over `localhost`
  - "IP-per-pod" model

## 3.3 Understand ClusterIP, NodePort, LoadBalancer Service Types And Endpoints

Services are all about abstracting away the details of which pods are running behind a particular network endpoint. For many applications, work must be processed by some other service. Using a service allows the application to "toss over" the work to Kubernetes, which then uses a selector to determine which pods are healthy and available to receive the work. The service abstracts numerous replica pods that are available to do work.

- [Official Documentation](https://kubernetes.io/docs/concepts/services-networking/service/)
- [Katakoda Networking Introduction](https://www.katacoda.com/courses/kubernetes/networking-introduction)

> Note: This section was completed using a GKE cluster and may differ from what your cluster looks like.

### ClusterIP

- Exposes the Service on a cluster-internal IP.
- Choosing this value makes the Service only reachable from within the cluster.
- This is the default ServiceType.
- [Using Source IP](https://kubernetes.io/docs/tutorials/services/source-ip/)
- [Kubectl Expose Command Reference](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#expose)

The imperative option is to create a deployment and then expose the deployment. In this example, the deployment is exposed using a ClusterIP service that accepts traffic on port 80 and translates it to the pod using port 8080.

`kubectl create deployment funkyapp1 --image=k8s.gcr.io/echoserver:1.4`

`kubectl expose deployment funkyapp1 --name=funkyip --port=80 --target-port=8080 --type=ClusterIP`

> Note: The `--type=ClusterIP` parameter is optional when deploying a `ClusterIP` service since this is the default type.

```yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: funkyapp1 #Selector
  name: funkyip
spec:
  ports:
    - port: 80
      protocol: TCP
      targetPort: 8080
  selector:
    app: funkyapp1
  type: ClusterIP #Note this!
```

Using `kubectl describe svc funkyip` shows more details:

```bash
Name:              funkyip
Namespace:         default
Labels:            app=funkyapp1
Annotations:       cloud.google.com/neg: {"ingress":true}
Selector:          app=funkyapp1
Type:              ClusterIP
IP:                10.108.3.156
Port:              <unset>  80/TCP
TargetPort:        8080/TCP
Endpoints:         10.104.2.7:8080
Session Affinity:  None
Events:            <none>
```

---

Check to make sure the `funkyip` service exists. This also shows the assigned service (cluster IP) address.

`kubectl get svc funkyip`

```bash
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
funkyip      ClusterIP   10.108.3.156   <none>        80/TCP    21m
```

---

From there, you can see the endpoint created to match any pod discovered using the `app: funkyapp1` label.

`kubectl get endpoints funkyip`

```bash
NAME         ENDPOINTS           AGE
funkyip      10.104.2.7:8080     21m
```

---

The endpoint matches the IP address of the matching pod.

`kubectl get pods -o wide`

```bash
NAME                         READY   STATUS    RESTARTS   AGE     IP            NODE                                                NOMINATED NODE   READINESS GATES
funkyapp1-7b478ccf9b-2vlc2   1/1     Running   0          21m     10.104.2.7    gke-my-first-cluster-1-default-pool-504c1e77-zg6v   <none>           <none>
shell-demo                   1/1     Running   0          3m12s   10.128.0.14   gke-my-first-cluster-1-default-pool-504c1e77-m9lk   <none>           <none>
```

---

The `.spec.ports.port` value defines the port used to access the service. The `.spec.ports.targetPort` value defines the port used to access the container's application.

`User -> Port -> Kubernetes Service -> Target Port -> Application`

This can be tested using `curl`:

```bash
export CLUSTER_IP=$(kubectl get services/funkyip -o go-template='(index .spec.clusterIP)')
echo CLUSTER_IP=$CLUSTER_IP
```

From there, use `curl $CLUSTER_IP:80` to hit the service `port`, which redirects to the `targetPort` of 8080.

`curl 10.108.3.156:80`

```bash
CLIENT VALUES:
client_address=10.128.0.14
command=GET
real path=/
query=nil
request_version=1.1
request_uri=http://10.108.3.156:8080/

SERVER VALUES:
server_version=nginx: 1.10.0 - lua: 10001

HEADERS RECEIVED:
accept=*/*
host=10.108.3.156
user-agent=curl/7.64.0
BODY:
-no body in request-root
```

### NodePort

- Exposes the Service on each Node's IP at a static port (the NodePort).
- [Official Documentation](https://kubernetes.io/docs/concepts/services-networking/service/#nodeport)

`kubectl expose deployment funkyapp1 --name=funkynode --port=80 --target-port=8080 --type=NodePort`

```yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: funkyapp1 #Selector
  name: funkynode
spec:
  ports:
    - port: 80
      protocol: TCP
      targetPort: 8080
  selector:
    app: funkyapp1
  type: NodePort #Note this!
```

---

This service is available on each node at a specific port.

`kubectl describe svc funkynode`

```bash
Name:                     funkynode
Namespace:                default
Labels:                   app=funkyapp1
Annotations:              cloud.google.com/neg: {"ingress":true}
Selector:                 app=funkyapp1
Type:                     NodePort
IP:                       10.108.5.37
Port:                     <unset>  80/TCP
TargetPort:               8080/TCP
NodePort:                 <unset>  30182/TCP
Endpoints:                10.104.2.7:8080
Session Affinity:         None
External Traffic Policy:  Cluster
Events:                   <none>
```

---

By using the node IP address with the `nodePort` value, we can see the desired payload. Make sure to scale the deployment so that each node is running one replica of the pod. For a cluster with 2 worker nodes, this can be done with `kubectl scale deploy funkyapp1 --replicas=3`.

From there, it is possible to `curl` directly to a node IP address using the `nodePort` when using the shell pod demo. If working from outside the pod network, use the service IP address.

`curl 10.128.0.14:30182`

```bash
CLIENT VALUES:
client_address=10.128.0.14
command=GET
real path=/
query=nil
request_version=1.1
request_uri=http://10.128.0.14:8080/

SERVER VALUES:
server_version=nginx: 1.10.0 - lua: 10001

HEADERS RECEIVED:
accept=*/*
host=10.128.0.14:30182
user-agent=curl/7.64.0
BODY:
-no body in request-root
```

### LoadBalancer

- Exposes the Service externally using a cloud provider's load balancer.
- NodePort and ClusterIP Services, to which the external load balancer routes, are automatically created.
- [Source IP for Services with Type LoadBalancer](https://kubernetes.io/docs/tutorials/services/source-ip/#source-ip-for-services-with-type-loadbalancer)

`kubectl expose deployment funkyapp1 --name=funkylb --port=80 --target-port=8080 --type=LoadBalancer`

```yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: null
  labels:
    app: funkyapp1
  name: funkylb
spec:
  ports:
    - port: 80
      protocol: TCP
      targetPort: 8080
  selector:
    app: funkyapp1
  type: LoadBalancer #Note this!
```

---

Get information on the `funkylb` service to determine the External IP address.

`kubectl get svc funkylb`

```bash
NAME      TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
funkylb   LoadBalancer   10.108.11.148   35.232.149.96   80:31679/TCP   64s
```

It is then possible to retrieve the payload using the External IP address and port value from anywhere on the Internet; no need to use the pod shell demo!

`curl 35.232.149.96:80`

```bash
CLIENT VALUES:
client_address=10.104.2.1
command=GET
real path=/
query=nil
request_version=1.1
request_uri=http://35.232.149.96:8080/

SERVER VALUES:
server_version=nginx: 1.10.0 - lua: 10001

HEADERS RECEIVED:
accept=*/*
host=35.232.149.96
user-agent=curl/7.55.1
BODY:
-no body in request-
```

### ExternalIP

[Official Documentation](https://kubernetes.io/docs/concepts/services-networking/service/#external-ips)

- Exposes a Kubernetes service on an external IP address.
- Kubernetes has no control over this external IP address.

Here is an example spec:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-service
spec:
  selector:
    app: MyApp
  ports:
    - name: http
      protocol: TCP
      port: 80
      targetPort: 9376
  externalIPs:
    - 80.11.12.10 #Take note!
```

### ExternalName

- Maps the Service to the contents of the externalName field (e.g. foo.bar.example.com), by returning a CNAME record with its value.
- No proxy of any kind is set up.

### Networking Cleanup for Objective 3.3

Run these commands to cleanup the resources, if desired.

```bash
kubectl delete svc funkyip
kubectl delete svc funkynode
kubectl delete svc funkylb
kubectl delete deploy funkyapp1
```

## 3.4 Know How To Use Ingress Controllers And Ingress Resources

Ingress exposes HTTP and HTTPS routes from outside the cluster to services within the cluster.

- Traffic routing is controlled by rules defined on the **Ingress resource**.
- An **Ingress controller** is responsible for fulfilling the Ingress, usually with a load balancer, though it may also configure your edge router or additional frontends to help handle the traffic.
  - For example, the [NGINX Ingress Controller for Kubernetes](https://www.nginx.com/products/nginx/kubernetes-ingress-controller)
- The name of an Ingress object must be a valid DNS subdomain name.
- [Ingress Documentation](https://kubernetes.io/docs/concepts/services-networking/ingress/)
- A list of [Ingress Controllers](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/)
- [Katacoda - Create Ingress Routing](https://www.katacoda.com/courses/kubernetes/create-kubernetes-ingress-routes) lab
- [Katacoda - Nginx on Kubernetes](https://www.katacoda.com/javajon/courses/kubernetes-applications/nginx) lab

Example of an ingress resource:

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: minimal-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
    - http:
        paths:
          - path: /testpath
            pathType: Prefix
            backend:
              service:
                name: test
                port:
                  number: 80
```

Information on some of the objects within this resource:

- [Ingress Rules](https://kubernetes.io/docs/concepts/services-networking/ingress/#ingress-rules)
- [Path Types](https://kubernetes.io/docs/concepts/services-networking/ingress/#path-types)

And, in the case of Nginx, [a custom resource definition (CRD) is often used](https://octopus.com/blog/nginx-ingress-crds) to extend the usefulness of an ingress. An example is shown below:

```yaml
apiVersion: k8s.nginx.org/v1
kind: VirtualServer
metadata:
  name: cafe
spec:
  host: cafe.example.com
  tls:
    secret: cafe-secret
  upstreams:
    - name: tea
      service: tea-svc
      port: 80
    - name: coffee
      service: coffee-svc
      port: 80
  routes:
    - path: /tea
      action:
        pass: tea
    - path: /coffee
      action:
        pass: coffee
```

## 3.5 Know How To Configure And Use CoreDNS

CoreDNS is a general-purpose authoritative DNS server that can serve as cluster DNS.

- A bit of history:
  - As of Kubernetes v1.12, CoreDNS is the recommended DNS Server, replacing `kube-dns`.
  - In Kubernetes version 1.13 and later the CoreDNS feature gate is removed and CoreDNS is used by default.
  - In Kubernetes 1.18, `kube-dns` usage with kubeadm has been deprecated and will be removed in a future version.
- [Using CoreDNS for Service Discovery](https://kubernetes.io/docs/tasks/administer-cluster/coredns/)
- [Customizing DNS Service](https://kubernetes.io/docs/tasks/administer-cluster/dns-custom-nameservers/)

CoreDNS is installed with the following default [Corefile](https://coredns.io/2017/07/23/corefile-explained/) configuration:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns
  namespace: kube-system
data:
  Corefile: |
    .:53 {
        errors
        health {
            lameduck 5s
        }
        ready
        kubernetes cluster.local in-addr.arpa ip6.arpa {
            pods insecure
            fallthrough in-addr.arpa ip6.arpa
            ttl 30
        }
        prometheus :9153
        forward . /etc/resolv.conf
        cache 30
        loop
        reload
        loadbalance
    }
```

If you need to customize CoreDNS behavior, you create and apply your own ConfigMap to override settings in the Corefile. The [Configuring DNS Servers for Kubernetes Clusters](https://docs.cloud.oracle.com/en-us/iaas/Content/ContEng/Tasks/contengconfiguringdnsserver.htm) document describes this in detail.

---

Review your configmaps for the `kube-system` namespace to determine if there is a `coredns-custom` configmap.

`kubectl get configmaps --namespace=kube-system`

```bash
NAME                                 DATA   AGE
cluster-kubestore                    0      23h
clustermetrics                       0      23h
extension-apiserver-authentication   6      24h
gke-common-webhook-lock              0      23h
ingress-gce-lock                     0      23h
ingress-uid                          2      23h
kube-dns                             0      23h
kube-dns-autoscaler                  1      23h
metrics-server-config                1      23h
```

---

Create a file named `coredns.yml` containing a configmap with the desired DNS entries in the `data` field such as the example below:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: coredns-custom
  namespace: kube-system
data:
  example.server:
    | # All custom server files must have a “.server” file extension.
    # Change example.com to the domain you wish to forward.
    example.com {
      # Change 1.1.1.1 to your customer DNS resolver.
      forward . 1.1.1.1
    }
```

---

Apply the configmap.

`kubectl apply -f coredns.yml`

---

Validate the existence of the `coredns-custom` configmap.

`kubectl get configmaps --namespace=kube-system`

```bash
NAME                                 DATA   AGE
cluster-kubestore                    0      24h
clustermetrics                       0      24h
coredns-custom                       1      6s
extension-apiserver-authentication   6      24h
gke-common-webhook-lock              0      24h
ingress-gce-lock                     0      24h
ingress-uid                          2      24h
kube-dns                             0      24h
kube-dns-autoscaler                  1      24h
metrics-server-config                1      24h
```

---

Get the configmap and output the value in yaml format.

`kubectl get configmaps --namespace=kube-system coredns-custom -o yaml`

```yaml
apiVersion: v1
data:
  example.server: |
    # Change example.com to the domain you wish to forward.
    example.com {
      # Change 1.1.1.1 to your customer DNS resolver.
      forward . 1.1.1.1
    }
kind: ConfigMap
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"v1","data":{"example.server":"# Change example.com to the domain you wish to forward.\nexample.com {\n  # Change 1.1.1.1 to your customer DNS resolver.\n  forward . 1.1.1.1\n}\n"},"kind":"ConfigMap","metadata":{"annotations":{},"name":"coredns-custom","namespace":"kube-system"}}
  creationTimestamp: "2020-10-27T19:49:24Z"
  managedFields:
    - apiVersion: v1
      fieldsType: FieldsV1
      fieldsV1:
        f:data:
          .: {}
          f:example.server: {}
        f:metadata:
          f:annotations:
            .: {}
            f:kubectl.kubernetes.io/last-applied-configuration: {}
      manager: kubectl-client-side-apply
      operation: Update
      time: "2020-10-27T19:49:24Z"
  name: coredns-custom
  namespace: kube-system
  resourceVersion: "519480"
  selfLink: /api/v1/namespaces/kube-system/configmaps/coredns-custom
  uid: 8d3250a5-cbb4-4f01-aae3-4e83bd158ebe
```

## 3.6 Choose An Appropriate Container Network Interface Plugin

Generally, it seems that Flannel is good for starting out in a very simplified environment, while Calico (and others) extend upon the basic functionality to meet design-specific requirements.

- [Network Plugins](https://kubernetes.io/docs/concepts/extend-kubernetes/compute-storage-net/network-plugins/)
- [Choosing a CNI Network Provider for Kubernetes](https://chrislovecnm.com/kubernetes/cni/choosing-a-cni-provider/)
- [Comparing Kubernetes CNI Providers: Flannel, Calico, Canal, and Weave](https://rancher.com/blog/2019/2019-03-21-comparing-kubernetes-cni-providers-flannel-calico-canal-and-weave/)

Common decision points include:

- Network Model: Layer 2, Layer 3, VXLAN, etc.
- Routing: Routing and route distribution for pod traffic between nodes
- Network Policy: Essentially the firewall between network / pod segments
- IP Address Management (IPAM)
- Datastore:
  - `etcd` - for direct connection to an etcd cluster
  - Kubernetes - for connection to a Kubernetes API server

# Objective 4: Storage

- [Objective 4: Storage](#objective-4-storage)
  - [4.1 Understand Storage Classes, Persistent Volumes](#41-understand-storage-classes-persistent-volumes)
    - [Storage Classes](#storage-classes)
    - [Persistent Volumes](#persistent-volumes)
  - [4.2 Understand Volume Mode, Access Modes And Reclaim Policies For Volumes](#42-understand-volume-mode-access-modes-and-reclaim-policies-for-volumes)
    - [Volume Mode](#volume-mode)
    - [Access Modes](#access-modes)
    - [Reclaim Policies](#reclaim-policies)
  - [4.3 Understand Persistent Volume Claims Primitive](#43-understand-persistent-volume-claims-primitive)
  - [4.4 Know How To Configure Applications With Persistent Storage](#44-know-how-to-configure-applications-with-persistent-storage)

## 4.1 Understand Storage Classes, Persistent Volumes

- [Storage Classes](https://kubernetes.io/docs/concepts/storage/storage-classes/)
- [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)

### Storage Classes

- [Reclaim Policy](https://kubernetes.io/docs/concepts/storage/storage-classes/#reclaim-policy): PersistentVolumes that are dynamically created by a StorageClass will have the reclaim policy specified in the reclaimPolicy field of the class
  - Delete: When PersistentVolumeClaim is deleted, also deletes PersistentVolume and underlying storage object
  - Retain: When PersistentVolumeClaim is deleted, PersistentVolume remains and volume is "released"
- [Volume Binding Mode](https://kubernetes.io/docs/concepts/storage/storage-classes/#volume-binding-mode):
  - `Immediate`: By default, the `Immediate` mode indicates that volume binding and dynamic provisioning occurs once the PersistentVolumeClaim is created
  - `WaitForFirstConsumer`: Delay the binding and provisioning of a PersistentVolume until a Pod using the PersistentVolumeClaim is created
    - Supported by `AWSElasticBlockStore`, `GCEPersistentDisk`, and `AzureDisk`
- [Allow Volume Expansion](https://kubernetes.io/docs/concepts/storage/storage-classes/#allow-volume-expansion): Allow volumes to be expanded
  - Note: It is not possible to reduce the size of a PersistentVolume
- Default Storage Class: A default storage class is used when a PersistentVolumeClaim does not specify the storage class
  - Can be handy when a single default services all pod volumes
- [Provisioner](https://kubernetes.io/docs/concepts/storage/storage-classes/#provisioner)
  - Determines the volume plugin to use for provisioning PVs.
  - Example: `gke-pd`, `azure-disk`

---

View all storage classes

`kubectl get storageclass` or `kubectl get sc`

```bash
NAME                 PROVISIONER            RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
standard (default)   kubernetes.io/gce-pd   Delete          Immediate           true                   25h
```

---

View the storage class in yaml format

`kubectl get sc standard -o yaml`

```yaml
allowVolumeExpansion: true
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: standard
parameters:
  type: pd-standard
provisioner: kubernetes.io/gce-pd
reclaimPolicy: Delete
volumeBindingMode: Immediate
```

---

Make a custom storage class using the yaml configuration below and save it as `speedyssdclass.yaml`

```yaml
allowVolumeExpansion: true
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: speedyssdclass
parameters:
  type: pd-ssd # Note: This will use SSD backed disks
  fstype: ext4
  replication-type: none
provisioner: kubernetes.io/gce-pd
reclaimPolicy: Delete
volumeBindingMode: Immediate
```

---

Apply the storage class configuration to the cluster

`kubectl apply -f speedyssdclass.yaml`

```bash
storageclass.storage.k8s.io/speedyssdclass created
```

---

Get the storage classes

`kubectl get sc`

```bash
NAME                 PROVISIONER            RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
speedyssdclass       kubernetes.io/gce-pd   Retain          WaitForFirstConsumer   true                   5m19s
standard (default)   kubernetes.io/gce-pd   Delete          Immediate              true                   8d
```

### Persistent Volumes

View a persistent volume in yaml format

`kubectl get pv pvc-d2f6e37e-277f-4b7b-8725-542609f1dea4 -o yaml`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pvc-d2f6e37e-277f-4b7b-8725-542609f1dea4
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 1Gi
  persistentVolumeReclaimPolicy: Delete
  storageClassName: standard
  volumeMode: Filesystem
```

---

Create a new disk named `pv100` in Google Cloud to be used as a persistent volume

> Note: Use the zone of your GKE cluster

`gcloud compute disks create pv100 --size 10GiB --zone=us-central1-c`

---

Make a custom persistent volume using the yaml configuration below and save it as `pv100.yaml`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv100
spec:
  accessModes:
    - ReadWriteOnce
  capacity:
    storage: 1Gi
  persistentVolumeReclaimPolicy: Delete
  storageClassName: standard
  volumeMode: Filesystem
  gcePersistentDisk: # This section is required since we are not using a Storage Class
    fsType: ext4
    pdName: pv100
```

---

Apply the persistent volume to the cluster

`kubectl apply -f pv100.yaml`

```bash
persistentvolume/pv100 created
```

---

Get the persistent volume and notice that it has a status of `Available` since there is no `PersistentVolumeClaim` to bind against

`kubectl get pv pv100`

```bash
NAME    CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
pv100   1Gi        RWO            Delete           Available           standard                2m51s
```

## 4.2 Understand Volume Mode, Access Modes And Reclaim Policies For Volumes

- [Volume Mode](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#volume-mode)
- [Access Modes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#access-modes)
- [Reclaim Policy](https://kubernetes.io/docs/concepts/storage/storage-classes/#reclaim-policy)

### Volume Mode

- Filesystem: Kubernetes formats the volume and presents it to a specified mount point.
  - If the volume is backed by a block device and the device is empty, Kuberneretes creates a filesystem on the device before mounting it for the first time.
- Block: Kubernetes exposes a raw block device to the container.
  - Improved time to usage and perhaps performance.
  - The container must know what to do with the device; there is no filesystem.
- Defined in `.spec.volumeMode` for a `PersistentVolumeClaim`.

---

View the volume mode for persistent volume claims using the `-o wide` to see the `VOLUMEMODE` column

`kubectl get pvc -o wide`

```bash
NAME        STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE   VOLUMEMODE
www-web-0   Bound    pvc-f3e92637-7e0d-46a3-ad87-ef1275bb5a72   1Gi        RWO            standard       19m   Filesystem
www-web-1   Bound    pvc-d2f6e37e-277f-4b7b-8725-542609f1dea4   1Gi        RWO            standard       19m   Filesystem
```

### Access Modes

- ReadWriteOnce (RWO): can be mounted as read-write by a single node
- ReadOnlyMany (ROX): can be mounted as read-only by many nodes
- ReadWriteMany (RWX): can be mounted as read-write by many nodes
- Defined in `.spec.accessModes` for a `PersistentVolumeClaim` and `PersistentVolume`

View the access mode for persistent volume claims

`kubectl get pvc`

```bash
NAME        STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
www-web-0   Bound    pvc-f3e92637-7e0d-46a3-ad87-ef1275bb5a72   1Gi        RWO            standard       28m
www-web-1   Bound    pvc-d2f6e37e-277f-4b7b-8725-542609f1dea4   1Gi        RWO            standard       27m
```

### Reclaim Policies

- [Reclaim Policy](https://kubernetes.io/docs/concepts/storage/storage-classes/#reclaim-policy): PersistentVolumes that are dynamically created by a StorageClass will have the reclaim policy specified in the reclaimPolicy field of the class
  - Delete: When PersistentVolumeClaim is deleted, also deletes PersistentVolume and underlying storage object
  - Retain: When PersistentVolumeClaim is deleted, PersistentVolume remains and volume is "released"
- [Change the Reclaim Policy of a PersistentVolume](https://kubernetes.io/docs/tasks/administer-cluster/change-pv-reclaim-policy/)
- Defined in `.spec.persistentVolumeReclaimPolicy` for `PersistentVolume`.

---

View the reclaim policy set on persistent volumes

`kubectl get pv`

```bash
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM               STORAGECLASS   REASON   AGE
pvc-d2f6e37e-277f-4b7b-8725-542609f1dea4   1Gi        RWO            Delete           Bound    default/www-web-1   standard                45m
pvc-f3e92637-7e0d-46a3-ad87-ef1275bb5a72   1Gi        RWO            Delete           Bound    default/www-web-0   standard                45m
```

## 4.3 Understand Persistent Volume Claims Primitive

Make a custom persistent volume claim using the yaml configuration below and save it as `pvc01.yaml`

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc01
spec:
  storageClassName: standard
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```

---

Apply the persistent volume claim

`kubectl apply -f pvc01.yaml`

```bash
persistentvolumeclaim/pvc01 created
```

---

Get the persistent volume claim

`kubectl get pvc pvc01`

```bash
NAME    STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
pvc01   Bound    pvc-9f2e7c5d-b64c-467e-bba6-86ccb333d981   3Gi        RWO            standard       5m19s
```

## 4.4 Know How To Configure Applications With Persistent Storage

- [Configure a Pod to Use a PersistentVolume for Storage](https://kubernetes.io/docs/tasks/configure-pod-container/configure-persistent-volume-storage/)

---

Create a new yaml file using the configuration below and save it as `pv-pod.yaml`

> Note: Make sure to create `pvc01` in [this earlier step](#43-understand-persistent-volume-claims-primitive)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pv-pod
spec:
  volumes:
    - name: pv-pod-storage # The name of the volume, used by .spec.containers.volumeMounts.name
      persistentVolumeClaim:
        claimName: pvc01 # This pvc was created in an earlier step
  containers:
    - name: pv-pod-container
      image: nginx
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/usr/share/nginx/html"
          name: pv-pod-storage # This refers back to .spec.volumes.name
```

---

Apply the pod

`kubectl apply -f pv-pod.yaml`

```bash
pod/pv-pod created
```

---

Watch the pod provisioning process

`kubectl get pod -w pv-pod`

```bash
NAME     READY   STATUS    RESTARTS   AGE
pv-pod   1/1     Running   0          30s
```

---

View the binding on `pvc01`

`kubectl describe pvc pvc01`

```bash
Name:          pvc01
Namespace:     default
StorageClass:  standard
Status:        Bound
Volume:        pvc-9f2e7c5d-b64c-467e-bba6-86ccb333d981
Labels:        <none>
Annotations:   pv.kubernetes.io/bind-completed: yes
               pv.kubernetes.io/bound-by-controller: yes
               volume.beta.kubernetes.io/storage-provisioner: kubernetes.io/gce-pd
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      3Gi
Access Modes:  RWO
VolumeMode:    Filesystem
Mounted By:    pv-pod # Here it is!
Events:
  Type    Reason                 Age   From                         Message
  ----    ------                 ----  ----                         -------
  Normal  ProvisioningSucceeded  36m   persistentvolume-controller  Successfully provisioned volume pvc-9f2e7c5d-b64c-467e-bba6-86ccb333d981 using kubernetes.io/gce-pd
```

# Objective 5: Troubleshooting

- [Troubleshooting Kubernetes deployments](https://learnk8s.io/troubleshooting-deployments)

- [Objective 5: Troubleshooting](#objective-5-troubleshooting)
  - [5.1 Evaluate Cluster And Node Logging](#51-evaluate-cluster-and-node-logging)
    - [Cluster Logging](#cluster-logging)
    - [Node Logging](#node-logging)
  - [5.2 Understand How To Monitor Applications](#52-understand-how-to-monitor-applications)
  - [5.3 Manage Container Stdout & Stderr Logs](#53-manage-container-stdout--stderr-logs)
  - [5.4 Troubleshoot Application Failure](#54-troubleshoot-application-failure)
  - [5.5 Troubleshoot Cluster Component Failure](#55-troubleshoot-cluster-component-failure)
  - [5.6 Troubleshoot Networking](#56-troubleshoot-networking)

## 5.1 Evaluate Cluster And Node Logging

### Cluster Logging

Having a separate storage location for cluster component logging, such as nodes, pods, and applications.

- [Cluster-level logging architectures](https://kubernetes.io/docs/concepts/cluster-administration/logging/#cluster-level-logging-architectures)
- [Kubernetes Logging Best Practices](https://platform9.com/blog/kubernetes-logging-best-practices/)

Commonly deployed in one of three ways:

# [Logging agent on each node](https://kubernetes.io/docs/concepts/cluster-administration/logging/#using-a-node-logging-agent) that sends log data to a backend storage repository
   # These agents can be deployed using a DaemonSet replica to ensure nodes have the agent running
   # Note: This approach only works for applications' standard output (_stdout_) and standard error (_stderr_)
# [Logging agent as a sidecar](https://kubernetes.io/docs/concepts/cluster-administration/logging/#using-a-sidecar-container-with-the-logging-agent) to specific deployments that sends log data to a backend storage repository
   # Note: Writing logs to a file and then streaming them to stdout can double disk usage
# [Configure the containerized application](https://kubernetes.io/docs/concepts/cluster-administration/logging/#exposing-logs-directly-from-the-application) to send log data to a backend storage repository

### Node Logging

Having a log file on the node that is populated with standard output (_stdout_) and standard error (_stderr_) log entries from containers running on the node.

- [Logging at the node level](https://kubernetes.io/docs/concepts/cluster-administration/logging/#logging-at-the-node-level)

## 5.2 Understand How To Monitor Applications

- [Using kubectl describe pod to fetch details about pods](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application-introspection/#using-kubectl-describe-pod-to-fetch-details-about-pods)
- [Interacting with running Pods](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#interacting-with-running-pods)

## 5.3 Manage Container Stdout & Stderr Logs

- [Kubectl Commands - Logs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)
- [How to find—and use—your GKE logs with Cloud Logging](https://cloud.google.com/blog/products/management-tools/finding-your-gke-logs)
- [Enable Log Rotation in Kubernetes Cluster](https://vividcode.io/enable-log-rotation-in-kubernetes-cluster/)

`kubectl logs [-f] [-p] (POD | TYPE/NAME) [-c CONTAINER]`

- `-f` will follow the logs
- `-p` will pull up the previous instance of the container
- `-c` will select a specific container for pods that have more than one

## 5.4 Troubleshoot Application Failure

- [Troubleshoot Applications](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/)
- Status: Pending
  - The Pod has been accepted by the Kubernetes cluster, but one or more of the containers has not been set up and made ready to run.
  - If no resources available on cluster, Cluster Autoscaling will increased node count if enabled
  - Once node count satisfied, pods in Pending status will be deployed
- Status: Waiting
  - A container in the Waiting state is still running the operations it requires in order to complete start up

---

Describe the pod to get details on the configuration, containers, events, conditions, volumes, etc.

- Is the status equal to RUNNING?
- Are there enough resources to schedule the pod?
- Are there enough `hostPorts` remaining to schedule the pod?

`kubectl describe pod counter`

```yaml
Name:         counter
Namespace:    default
Priority:     0
Node:         gke-my-first-cluster-1-default-pool-504c1e77-xcvj/10.128.0.15
Start Time:   Tue, 10 Nov 2020 16:33:10 -0600
Labels:       <none>
Annotations:  <none>
Status:       Running
IP:           10.104.1.7
IPs:
  IP:  10.104.1.7
Containers:
  count:
    Container ID:  docker://430313804a529153c1dc5badd1394164906a7dead8708a4b850a0466997e1c34
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:a9286defaba7b3a519d585ba0e37d0b2cbee74ebfe590960b0b1d6a5e97d1e1d
    Port:          <none>
    Host Port:     <none>
    Args:
      /bin/sh
      -c
      i=0; while true; do
        echo "$i: $(date)" >> /var/log/1.log;
        echo "$(date) INFO $i" >> /var/log/2.log;
        i=$((i+1));
        sleep 1;
      done

    State:          Running
      Started:      Tue, 10 Nov 2020 16:33:12 -0600
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/log from varlog (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-2qnnp (ro)
  count-log-1:
    Container ID:  docker://d5e95aa4aec3a55435d610298f94e7b8b2cfdf2fb88968f00ca4719a567a6e37
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:a9286defaba7b3a519d585ba0e37d0b2cbee74ebfe590960b0b1d6a5e97d1e1d
    Port:          <none>
    Host Port:     <none>
    Args:
      /bin/sh
      -c
      tail -n+1 -f /var/log/1.log
    State:          Running
      Started:      Tue, 10 Nov 2020 16:33:13 -0600
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/log from varlog (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-2qnnp (ro)
  count-log-2:
    Container ID:  docker://eaa9983cbd55288a139b63c30cfe3811031dedfae0842b9233ac48db65387d4d
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:a9286defaba7b3a519d585ba0e37d0b2cbee74ebfe590960b0b1d6a5e97d1e1d
    Port:          <none>
    Host Port:     <none>
    Args:
      /bin/sh
      -c
      tail -n+1 -f /var/log/2.log
    State:          Running
      Started:      Tue, 10 Nov 2020 16:33:13 -0600
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/log from varlog (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-2qnnp (ro)
Conditions:
  Type              Status
  Initialized       True
  Ready             True
  ContainersReady   True
  PodScheduled      True
Volumes:
  varlog:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:
    SizeLimit:  <unset>
  default-token-2qnnp:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-2qnnp
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                 node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age   From                                                        Message
  ----    ------     ----  ----                                                        -------
  Normal  Scheduled  30m   default-scheduler                                           Successfully assigned default/counter to gke-my-first-cluster-1-default-pool-504c1e77-xcvj
  Normal  Pulling    30m   kubelet, gke-my-first-cluster-1-default-pool-504c1e77-xcvj  Pulling image "busybox"
  Normal  Pulled     30m   kubelet, gke-my-first-cluster-1-default-pool-504c1e77-xcvj  Successfully pulled image "busybox"
  Normal  Created    30m   kubelet, gke-my-first-cluster-1-default-pool-504c1e77-xcvj  Created container count
  Normal  Started    30m   kubelet, gke-my-first-cluster-1-default-pool-504c1e77-xcvj  Started container count
  Normal  Pulling    30m   kubelet, gke-my-first-cluster-1-default-pool-504c1e77-xcvj  Pulling image "busybox"
  Normal  Created    30m   kubelet, gke-my-first-cluster-1-default-pool-504c1e77-xcvj  Created container count-log-1
  Normal  Pulled     30m   kubelet, gke-my-first-cluster-1-default-pool-504c1e77-xcvj  Successfully pulled image "busybox"
  Normal  Started    30m   kubelet, gke-my-first-cluster-1-default-pool-504c1e77-xcvj  Started container count-log-1
  Normal  Pulling    30m   kubelet, gke-my-first-cluster-1-default-pool-504c1e77-xcvj  Pulling image "busybox"
  Normal  Pulled     30m   kubelet, gke-my-first-cluster-1-default-pool-504c1e77-xcvj  Successfully pulled image "busybox"
  Normal  Created    30m   kubelet, gke-my-first-cluster-1-default-pool-504c1e77-xcvj  Created container count-log-2
  Normal  Started    30m   kubelet, gke-my-first-cluster-1-default-pool-504c1e77-xcvj  Started container count-log-2
```

---

Validate the commands being presented to the pod to ensure nothing was configured incorrectly.

`kubectl apply --validate -f mypod.yaml`

## 5.5 Troubleshoot Cluster Component Failure

- [Troubleshoot Clusters](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/)
- [A general overview of cluster failure modes](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/#a-general-overview-of-cluster-failure-modes)
- [Control Plane Components](https://kubernetes.io/docs/concepts/overview/components/#control-plane-components)
- [Node Components](https://kubernetes.io/docs/concepts/overview/components/#node-components)

---

Components to investigate:

- Control Plane Components
  - `kube-apiserver`
  - `etcd`
  - `kube-scheduler`
  - `kube-controller-manager`
  - `cloud-controller-manager`
- Node Components
  - `kubelet`
  - `kube-proxy`
  - Container runtime (e.g. Docker)

---

View the components with:

`kubectl get all -n kube-system`

```bash
NAME                                                               READY   STATUS    RESTARTS   AGE
pod/konnectivity-agent-56nck                                       1/1     Running   0          15d
pod/konnectivity-agent-gmklx                                       1/1     Running   0          15d
pod/konnectivity-agent-wg92c                                       1/1     Running   0          15d
pod/kube-dns-576766df6b-cz4ln                                      3/3     Running   0          15d
pod/kube-dns-576766df6b-rcsk7                                      3/3     Running   0          15d
pod/kube-dns-autoscaler-7f89fb6b79-pq66d                           1/1     Running   0          15d
pod/kube-proxy-gke-my-first-cluster-1-default-pool-504c1e77-m9lk   1/1     Running   0          15d
pod/kube-proxy-gke-my-first-cluster-1-default-pool-504c1e77-xcvj   1/1     Running   0          15d
pod/kube-proxy-gke-my-first-cluster-1-default-pool-504c1e77-zg6v   1/1     Running   0          15d
pod/l7-default-backend-7fd66b8b88-ng57f                            1/1     Running   0          15d
pod/metrics-server-v0.3.6-7c5cb99b6f-2d8bx                         2/2     Running   0          15d

NAME                           TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)         AGE
service/default-http-backend   NodePort    10.108.1.184   <none>        80:32084/TCP    15d
service/kube-dns               ClusterIP   10.108.0.10    <none>        53/UDP,53/TCP   15d
service/metrics-server         ClusterIP   10.108.1.154   <none>        443/TCP         15d

NAME                                      DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR                                                        AGE
daemonset.apps/konnectivity-agent         3         3         3       3            3           <none>                                                               15d
daemonset.apps/kube-proxy                 0         0         0       0            0           kubernetes.io/os=linux,node.kubernetes.io/kube-proxy-ds-ready=true   15d
daemonset.apps/metadata-proxy-v0.1        0         0         0       0            0           cloud.google.com/metadata-proxy-ready=true,kubernetes.io/os=linux    15d
daemonset.apps/nvidia-gpu-device-plugin   0         0         0       0            0           <none>                                                               15d

NAME                                    READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/kube-dns                2/2     2            2           15d
deployment.apps/kube-dns-autoscaler     1/1     1            1           15d
deployment.apps/l7-default-backend      1/1     1            1           15d
deployment.apps/metrics-server-v0.3.6   1/1     1            1           15d

NAME                                               DESIRED   CURRENT   READY   AGE
replicaset.apps/kube-dns-576766df6b                2         2         2       15d
replicaset.apps/kube-dns-autoscaler-7f89fb6b79     1         1         1       15d
replicaset.apps/l7-default-backend-7fd66b8b88      1         1         1       15d
replicaset.apps/metrics-server-v0.3.6-7c5cb99b6f   1         1         1       15d
replicaset.apps/metrics-server-v0.3.6-7ff8cdbc49   0         0         0       15d
```

---

Retrieve detailed information about the cluster

`kubectl cluster-info` or `kubectl cluster-info dump`

---

Retrieve a list of known API resources to aid with describing or troubleshooting

`kubectl api-resources`

```bash
NAME                              SHORTNAMES   APIGROUP                       NAMESPACED   KIND
bindings                                                                      true         Binding
componentstatuses                 cs                                          false        ComponentStatus
configmaps                        cm                                          true         ConfigMap
endpoints                         ep                                          true         Endpoints
events                            ev                                          true         Event
limitranges                       limits                                      true         LimitRange
namespaces                        ns                                          false        Namespace
nodes                             no                                          false        Node
persistentvolumeclaims            pvc                                         true         PersistentVolumeClaim
persistentvolumes                 pv                                          false        PersistentVolume

<snip>
```

---

[Check the logs](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-cluster/#looking-at-logs) in `/var/log` on the master and worker nodes:

- Master
  - `/var/log/kube-apiserver.log` - API Server, responsible for serving the API
  - `/var/log/kube-scheduler.log` - Scheduler, responsible for making scheduling decisions
  - `/var/log/kube-controller-manager.log` - Controller that manages replication controllers
- Worker Nodes
  - `/var/log/kubelet.log` - Kubelet, responsible for running containers on the node
  - `/var/log/kube-proxy.log` - Kube Proxy, responsible for service load balancing

## 5.6 Troubleshoot Networking

- [Flannel Troubleshooting](https://github.com/coreos/flannel/blob/master/Documentation/troubleshooting.md#kubernetes-specific)
  - The flannel kube subnet manager relies on the fact that each node already has a podCIDR defined.
- [Calico Troubleshooting](https://docs.projectcalico.org/maintenance/troubleshoot/)
  - [Containers do not have network connectivity](https://docs.projectcalico.org/maintenance/troubleshoot/troubleshooting#containers-do-not-have-network-connectivity)