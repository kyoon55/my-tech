---
layout: default
title: Docker First Setup From A Cloud Guru Part 2
excerpt: This is a page for deploying several resources on Docker, from A Cloud Guru
parent: Docker
nav_order: 1
date: 2023-02-19
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>
# Tutorial: Running a Sample Docker Image

## Building Smaller Images with Multi-Stage Builds

### This decresaes size and # of layers to simplify Dockerfile deployments

#### Key Points
- Dockerfile starts with "FROM python3 AS base"
- Then it creates another layer to ahorten # of layers: "FROM base AS builder"

<details>
<summary>Script Example</summary>

{% highlight bash %}
{% raw %}

# Do Prep Work in the Image

## Change to the notes directory:
cd notes

## Check the Dockerfile:
cat Dockerfile

## Build an image using the file:
docker build -t notesapp:default .

## Set a variable to view the layers of the image:
export showLayers='{{ range .RootFS.Layers }}{{ println . }}{{end}}'

## Set a variable to show the size of the image:
export showSize='{{ .Size }}'

## Show the image layers:
docker inspect -f "$showLayers" notesapp:default

## Count the number of layers:
docker inspect -f "$showLayers" notesapp:default | wc -l

## Show the size of the image:
docker inspect -f "$showSize" notesapp:default | numfmt --to=iec

{% endraw %}
{% endhighlight %}
</details>
<hr style="3px solid #bbb">


# Tutorial: Running a Sample Docker Image

## Updating Containers with Watchtower

### Watchtower is a container that notifies changes in containers

<details>
<summary>Script Example</summary>

{% highlight bash %}
{% raw %}

# Create the Docker file

## Create a Dockerfile
vi Dockerfile

## We need to set these parameters and conditions:

### The base image should be node.
###  Using the RUN instruction, make a directory called /var/node.
### Use the ADD instruction to add the contents of the code directory into /var/node.
### Make /var/node the working directory.
### Execute an npm install.
### Set ./bin/www as the command.
### From the command line, log in to Docker Hub.
### Build your image using <USERNAME>/express.
### Push the image to Docker Hub.
### When we're finished, the file should look like this:

## Dockerfile:
FROM node

RUN mkdir -p /var/node
ADD content-express-demo-app/ /var/node/
WORKDIR /var/node
RUN npm install
CMD ./bin/www

# Log in to Docker Hub
## We've got to build our Docker image, but before we do that, log into our Docker Hub account. Do it from the command line, like this:

docker login

# Build the Docker Image
## Now we can build the image, where <USERNAME> in the command is your Docker Hub username:

docker build -t <USERNAME>/express -f Dockerfile .

# Push the Image to Docker Hub
## Push the image up to Docker Hub with this:

docker push <USERNAME>/express

## If we log into the Docker Hub with a web browser, we should see the image in our repository.

# Create the Demo Container
## In order for Watchtower to watch anything, we've got to give it something to keep an eye on. We're going to create a Docker container and set some parameters, all in one command:

docker run -d --name demo-app -p 80:3000 --restart always <USERNAME>/express

## To make sure it's running, execute a quick docker ps. We should see it in the list.

# Create the Watchtower Container

## Now we'll spin up Watchtower to watch our demo-app container. We can create the Watchtower container with this:

docker run -d --name watchtower --restart always -v /var/run/docker.sock:/var/run/docker.sock v2tec/watchtower -i 30

## Again, run a docker ps real quick to make sure this container spun up properly.

# Testing
## We want to see if Watchtower is actually keeping watch, so we've got to take a couple more steps.

# Update the Docker Image
Dockerfile of "Dockerimage"

FROM node

RUN mkdir -p /var/node
RUN mkdir -p /var/test
ADD content-express-demo-app/ /var/node/
WORKDIR /var/node
RUN npm install
CMD ./bin/www

## Rebuild the image with this:

docker build -t <USERNAME>/express -f Dockerfile .

## Push it up to Docker Hub:

docker push <USERNAME>/express

{% endraw %}
{% endhighlight %}
</details>
<hr style="3px solid #bbb">


## Add Metadata and Labels

### This will create several labels, the maintainer, build date, application name, and application versions

#### Key Points
- 1
- 2

<details>
<summary>Script Example</summary>

{% highlight bash %}
{% raw %}

## Create a Dockerfile

FROM node

LABEL maintainer="EMAIL_ADDRESS"

ARG BUILD_VERSION
ARG BUILD_DATE
ARG APPLICATION_NAME

LABEL org.label-schema.build-date=$BUILD_DATE
LABEL org.label-schema.application=$APPLICATION_NAME
LABEL org.label-schema.version=$BUILD_VERSION

RUN mkdir -p /var/node
ADD weather-app/ /var/node/
WORKDIR /var/node
RUN npm install
EXPOSE 3000
CMD ./bin/www

# Build the Docker Image
## Log in to Docker Hub:

[root@docker_workstation]# docker login
Build the Docker image using the following parameters:

## Note: Be sure to replace <DOCKER_USERNAME> with your Docker Hub username.

[root@docker_workstation]# docker build -t <DOCKER_USERNAME>/weather-app --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') \
--build-arg APPLICATION_NAME=weather-app --build-arg BUILD_VERSION=v1.0 -f Dockerfile .

## Show image ID:

[root@docker_workstation]# docker images
## Use image ID to inspect:

[root@docker_workstation]# docker inspect <IMAGE_ID>

# Push the Image to Docker Hub
## Push the weather-app image to Docker Hub.
## Note: Be sure to replace <DOCKER_USERNAME> with your Docker Hub username.

[root@docker_workstation]# docker push <DOCKER_USERNAME>/weather-app

# Create the weather-app Container

## On the Docker Server, start the weather-app container.

## Note: Be sure to replace <DOCKER_USERNAME> with your Docker Hub username.

[root@docker_server]# docker run -d --name weather-app -p 80:3000 --restart always <DOCKER_USERNAME>/weather-app

## Verify the image is running:

[root@docker_server]# docker ps

# Check Out Version 1.1 of the Weather App

## On the Docker Workstation, in the weather-app directory, check out version 1.1 of the weather app:

[root@docker_workstation]# cd weather-app
[root@docker_workstation]# git checkout v1.1
[root@docker_workstation]# git branch
[root@docker_workstation]# cd ../

# Rebuild the weather-app Image

## Rebuild and push the weather-app image.
## Note: Be sure to replace <DOCKER_USERNAME> with your Docker Hub username.

[root@docker_workstation]# docker build -t <DOCKER_USERNAME>/weather-app --build-arg BUILD_DATE=$(date -u +'%Y-%m-%dT%H:%M:%SZ') \
--build-arg APPLICATION_NAME=weather-app --build-arg BUILD_VERSION=v1.1  -f Dockerfile .
Just to double check, inspect the image (running docker images first to get the image ID):

[root@docker_workstation]# docker inspect <IMAGE_ID>

## We should be running v1.1 now, so let's push it up:

[root@docker_workstation]# docker push <DOCKER_USERNAME>/weather-app
## On the Docker Server:
## Show image status:

[root@docker_server]# docker ps

## Using the container ID from the previous command, inspect the weather-app image:

[root@docker_server]# docker inspect <IMAGE_ID>
This one should also be at v1.1.

{% endraw %}
{% endhighlight %}
</details>
<hr style="3px solid #bbb">