---
layout: default
title: Docker First Setup From A Cloud Guru Part 3
excerpt: This is a page for deploying several resources on Docker, from A Cloud Guru
parent: Docker
nav_order: 1
date: 2023-02-19
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>


<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>
# Tutorial: Running a Sample Docker Image

## Dockerize a Flask Applicaiton

### Deployment of a Flask Applicaiton

#### Key Points
- Dockerfile starts with "FROM python3 AS base
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