---
layout: default
title: Initializing the Docker Environment
parent: Docker
nav_order: 1
excerpt: This is the way to initialize the docker environment. This will need to 1. install the docker prerequisites, 2. CentOs-specific Docker Repo, 3. install Docker, 4. Enable Docker daemon, 5. add a user and 6. run a test.
time: 2023-06-12
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.time | date: "%Y %m %d" }}</h3>

## Installing Docker

1. Install the Docker prerequisites:

- `sudo yum install -y yum-utils device-mapper-persistent-data lvm2`

2. Using yum-config-manager, add the CentOS-specific Docker repo:

- `sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`

3. Install Docker:

- `sudo yum -y install docker-ce`

## Enable the Docker Daemon

1. Enable the Docker daemon:

- `sudo systemctl enable --now docker`

## Configure User Permissions

1. Add the lab user to the docker group:

- `sudo usermod -aG docker cloud_user`
  - Caution: You will need to exit the server for the change to take effect.

## Run a Test Image

1. Using docker, run the hello-world image to verify that the environment is set up properly:

- `docker run hello-world`