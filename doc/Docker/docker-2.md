---
layout: default
title: Initializing the Docker Environment
parent: Docker
nav_order: 1
excerpt: This is the way to initialize the docker environment. This will need to # install the docker prerequisites, # CentOs-specific Docker Repo, # install Docker, # Enable Docker daemon, # add a user and # run a test.
date: 2023-02-17
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>

## Installing Docker

# Install the Docker prerequisites:

- `sudo yum install -y yum-utils device-mapper-persistent-data lvm2`

# Using yum-config-manager, add the CentOS-specific Docker repo:

- `sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo`

# Install Docker:

- `sudo yum -y install docker-ce`

## Enable the Docker Daemon

# Enable the Docker daemon:

- `sudo systemctl enable --now docker`

## Configure User Permissions

# Add the lab user to the docker group:

- `sudo usermod -aG docker cloud_user`
  - Caution: You will need to exit the server for the change to take effect.

## Run a Test Image

# Using docker, run the hello-world image to verify that the environment is set up properly:

- `docker run hello-world`
