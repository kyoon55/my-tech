---
layout: default
title: Working With Prebuilt Docker Images
parent: Docker
nav_order: 1
excerpt: This article is to migrate a website, from a traditional server to containers by using Dockers
date: 2023-02-01
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>

## Explore Docker Hub

# Sign in to Docker Hub.
# At the top of the page, search for "httpd".
# In the left-hand menu, filter for Application Infrastructure, and Official Images.
# Select the httpd project.
# At the top of the page, click the Tags tab.
# Under latest, select linux/amd64.
# Back in the list of available images, select nginx
# Review the How to use this image section.

## Get and View httpd

```bash
## In the Docker Instance, verify that docker is installed:
docker ps
## Using docker, pull the httpd:2.4 image:
docker pull httpd:2.4
## Run the image:
docker run --name httpd -p 8080:80 -d httpd:2.4
## Check the status of the container:
docker ps
## In a web browser, test connectivity to the container:
<PUBLIC_IP_ADDRESS>:8080
```

## Run a Copy of the Website in httpd

```bash
## Clone the Widget Factory Inc repository
git clone https://github.com/linuxacademy/content-widget-factory-inc
## Change to the content-widget-factory-inc directory:
cd content-widget-factory-inc
## Check the files:
ll
## Move to the web directory:
cd web
## Check the files:
ll
## Stop the httpd container:
docker stop httpd
## Remove the httpd container:
docker rm httpd
## Verify that the container has been removed:
docker ps -a
## Run the container with the website data:
docker run --name httpd -p 8080:80 -v $(pwd):/usr/local/apache2/htdocs:ro -d httpd:2.4
## Check the status of the container:
docker ps
## In a web browser, check connectivity to the container:
<PUBLIC_IP_ADDRESS>:8080
```

## Get and View Nginx

```bash
## Using docker, pull the latest version of nginx:
docker pull nginx
## Verify that the image was pulled successfully:
docker images
## Run the container using the nginx image:
docker run --name nginx -p 8081:80 -d nginx 
## Check the status of the container:
docker ps
## Verify connectivity to the nginx container:
<PUBLIC_IP_ADDRESS>:8081
```

## Run a Copy of the Website in Nginx

```bash
## Stop the nginx container:
docker stop nginx
## Remove the nginx container:
docker rm nginx
## Verify that the container has been removed:
docker ps -a
## Run the nginx container, and mount the website data:
docker run --name nginx -v $(pwd):/usr/share/nginx/html:ro -p 8081:80 -d nginx
## Check the status of the container:
docker ps
## In a web browser, verify connectivity to the container:
<PUBLIC_IP_ADDRESS>:8081
## Stop the nginx container:
docker stop nginx
## Remove the nginx container:
docker rm nginx
## Verify that the container has been removed:
docker ps -a
```
