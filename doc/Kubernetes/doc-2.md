---
layout: default
title: CKA Preparation, 1.22
parent: Kubernetes
nav_order: 2
date: 2022-10-25
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>

# Chapter 2: Getting Started

## K8s

- Kubernetes is a portable, extensible, open-source platform for managing containerized workloads and services, that facilitates both declarative configuration and automation 

- K8s means simply short for Kubernetes. The 8 represents the 8 letters between K and S 

## K8s Features

- Container Orchestration  
- The primary purpose of Kubernetes is to dynamically manage containers across multiple host systems  
- Application Reliability 
- Kubernetes makes it easier to build reliable, self-healing, and scalable applications 

## Automation 
- Kubernetes offers a variety of features to help automate the management of your container apps 


## The Control Plane 
- A collection of multiple components responsible for managing the cluster itself globally 

- Controls the cluster 
- Individual control plane components can run on any machine in the cluster, but usually are run on dedicated controller machines. 

- Kube-api-server 

- Serves the Kubernetes API 
- The primary interface to the control plane and the cluster itself 
- When interacting with your Kubernetes cluster, you will usually do so using the Kubernetes API. 

## Etcd 

- Is the backend data store for the Kubernetes cluster 
• Provides high-availability storage for all data relating to the state of the cluster 

## Kube-scheduler 

- Handles scheduling, the process of selecting an available node in the cluster on which to run containers 

## Kube-controller-manager 

- Runs a collection of multiple controller utilities in a single process 

- These controllers carry out a variety of automation-related tasks within the Kubernetes cluster 

## Cloud-controller-manager 

- Provides an interface between Kubernetes and various cloud platforms. 
- It is only used when using cloud-based resources alongside Kubernetes 

## K8s nodes 

- The machines where the containers managed by the cluster run 

- A cluster can have any number of nodes 

## The container runtime 

- Is not built into Kubernetes 
- Is a separate piece of software that's responsible for actually running containers on the machine. 
- Kubernetes supports multiple container runtime implementations 
- Some popular container runtimes are Docker and containerd. 

## Kube-proxy 

- Is a network proxy 
- Runs on each node and handles some tasks related to providing networking between containers and services in the cluster 

## Building a Kubernetes Cluster 

## Kubeadm 

- Kubeadm is a tool that simplifies the process of building Kubernetes clusters 
• $ kubeadm init initializes a control plane 
• $ kubeadm join command can be used to join a new node to the cluster 

Kubeadm Configuration 

• You can use the --config flag to pass a yaml file containing custom configuration for the cluster that will be created with kubeadm init 
• $ kubeadm init --config my-config.yaml 

Ignoring Preflight Errors 

• Kubeadm performs a series of preflight checks to verify that the current environment is suitable for creating or joining to a cluster 
• By default kubeadm will halt execution if any of these preflight checks fails 
• You can use the --ignore-preflight-errors flag to force kubeadm to ignore these errors and attempt the operation anyway 
• $ kubeadm init --ignore-preflight-errors all 

Networking Add-On 

• In order to become fully functional, the cluster needs a networking add-on 
• You can install one after the cluster has been initialized 
 
