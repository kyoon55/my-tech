---
layout: default
title: Docker First Setup From A Cloud Guru Part 1
excerpt: This is a page for deploying several resources on Docker, from A Cloud Guru
parent: Docker
nav_order: 1
date: 2023-02-19
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>


## Initializing the Docker Environment

### Ths deployment creates a new docker environment

#### Key Points
- Packages needed: yum-utils, device-mapper-presistent-data, lvm2
- Once docker-ce is installed, enable it and create a docker user
<details>

<summary>Script Example</summary>
{% highlight bash %}
## Installing Docker

# Install the Docker prerequisites:
sudo yum install -y yum-utils device-mapper-persistent-data lvm2

# Using yum-config-manager, add the CentOS-specific Docker repo:
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# Install Docker:
sudo yum -y install docker-ce

## Enable the Docker Daemon

# Enable the Docker daemon:
sudo systemctl enable --now docker

## Configure User Permissions

# Add the lab user to the docker group:
sudo usermod -aG docker cloud_user

## Caution: You will need to exit the server for the change to take effect.

## Run a Test Image

# Using docker, run the hello-world image to verify that the environment is set up properly:
docker run hello-world

{% endhighlight %}
</details>
<hr style="3px solid #bbb">

## Working with Prebuild Docker Images

### This Docker deployment is to migrate a website, from a traditional server to containers by using Dockers

#### Key Points
- Once Docker is installed, Docker commands are used, such as 
- docker ps
- docker run --name container-name -p Port:Port -d detech-background
- docker stop
- docker rm
- docker pull


<details>
<summary>Script Example</summary>
{% highlight bash %}
## Explore Docker Hub

### Sign in to Docker Hub.
## At the top of the page, search for "httpd".In the left-hand menu, filter for Application Infrastructure, and Official Images. Select the httpd project. At the top of the page, click the Tags tab. Under latest, select linux/amd64. Back in the list of available images, select nginx. Finally, Review the How to use this image section.

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

{% endhighlight %}
</details>
<hr style="3px solid #bbb">

## Handcrafting a Container Image

### Manually mounting volumes for each new container isn't a scalable solution. This deployment guides you to creating a new image from an existing container.

#### Key Points
- Some commands:
  - docker exec -t container-name bash


<details>
<summary>Script Example</summary>
{% highlight bash %}

# Get and Run the Base Image
## Retrieve the httpd image:
docker pull httpd:2.4

## Run the image:
docker run --name webtemplate -d httpd:2.4

## Check the status of the webtemplate container:
docker ps

# Install Tools and Code in the Container
## Log in to the container:
docker exec -it webtemplate bash

## Run apt update and install git
apt update && apt install git -y

## Clone the website code from GitHub:
git clone  https://github.com/linuxacademy/content-widget-factory-inc.git /tmp/widget-factory-inc

## Verify that the code was cloned successfully:
ls -l /tmp/widget-factory-inc/

## List the files in the htdocs/ directory:
ls -l htdocs/

## Remove the index.html file:
rm htdocs/index.html

## Copy the webcode from /tmp/ to the htdocs/ folder:
cp -r /tmp/widget-factory-inc/web/* htdocs/

## Verify that they were copied over successfully:
ls -l htdocs/

## Exit the container:
exit

# Create an Image from the Container
## Copy the Container ID:
docker ps

## Create an image from the container:
docker commit <CONTAINER_ID> example/widgetfactory:v1

## Verify that the image was created successfully:
docker images

## Take note of the image size. Then clean up the Template for a Second Version

## Log in to the container:
docker exec -it webtemplate bash

## Remove the cloned code from the /tmp/ directory:
rm -rf /tmp/widget-factory-inc/

## Use apt to uninstall git and clean the cache:
apt remove git -y && apt autoremove -y && apt clean 

## Exit the container:
exit

## Check the status of the container:
docker ps

## Create an image from the updated container:
docker commit <CONTAINER_ID> example/widgetfactory:v2

## Verify that both images are now running:
docker images  

## Delete the v1 image:
docker rmi example/widgetfactory:v1

# Run Multiple Containers from the Image

## Run multiple containers using the new image:
docker run -d --name web1 -p 8081:80 example/
widgetfactory:v2
docker run -d --name web2 -p 8082:80 example/widgetfactory:v2
docker run -d --name web3 -p 8083:80 example/widgetfactory:v2

## Check the status of the containers:
docker ps

## Stop the base webtemplate image:
docker stop webtemplate

## Verify that only the created containers are running:
docker ps

## Using a web browser, verify that the containers are running successfully:
<SERVER_PUBLIC_IP_ADDRESS>:8081
<SERVER_PUBLIC_IP_ADDRESS>:8082
<SERVER_PUBLIC_IP_ADDRESS>:8083

{% endhighlight %}
</details>
<hr style="3px solid #bbb">

## Storing Container Data In Docker Volumes

### Another way to back up the data that's stored in he docker containers

<details>
<summary>Script Example</summary>
{% highlight bash %}

## Discover Aonoymous Docker Volumes
docker images

## Run a container "postgress:12.1"
docker run -d --name db1 postgres:12.1
docker run -d --name db2 postgres:12.1

## Check the status
## Check the Docker volumes, creates an anonymous volume
docker ps
docker volume ls

## inspect the db1 and db2 containers
docker inspect db1 -f '{{ json .Mounts }}' | python -m json.tool
docker inspect db2 -f '{{ json .Mounts}}' | python -m json.tool

## Create a third container using the --rm flag
docker run -d --rm --name dbTemp postgres:12.1

## check the status
docker ps -a
docker volume ls

## Stop the db2 and dbTmp containers
docker stop db2 dbTmp

## List the anonymous volumes
docker volume ls

## Check the status of the containers:
docker ps -a

# Create a Docker Volume

## Create a Docker volume
docker volume create website

## Verify the volume
docker volume ls

## Copy the "widget-factory-inc" data to the website containers
sudo cp -r /home/cloud_user/widget-factory-inc/web/* /var/lib/docker/volumes/website/_data/

## List the copied data:
sudo ls -l /var/lib/docker/volumes/website/_data/

# Use the Website Volume with Containers

## Run a docker container with the website volume:
docker run -d --name web1 -p 80:80 -v website:/usr/local/apache2/htdocs:ro httpd:2.4

## Check the status of the container:
docker ps

## In a web browser, verify connectivity to the container:
<PUBLIC_IP_ADDRESS>

## Run a second container with the --rm flag:
docker run -d --name webTmp --rm -v website:/usr/local/apache2/htdocs:ro httpd:2.4

## Check the status of the containers:
docker ps

## Stop the webTmp container:
docker stop webTmp

## Check the status of the containers:
docker ps -a

## Verify that the website can still be accessed through a web browser:
<PUBLIC_IP_ADDRESS>

# Clean Up Unused Volumes

## Clean up the unused volumes:
docker volume prune
## Check the currently running containers:
docker ps -a

## Remove the db2 container:
docker rm db2

## Clean up the unused volumes again:
docker volume prune

## List the current volumes:
docker volume ls

# Back Up and Restore the Docker Volume

## Switch to the root user:
sudo -i

## Find where the website volume data is stored:
docker volume inspect website

## Copy the Mountpoint.

## Back up the volume:
tar czf /tmp/website_$(date +%Y-%m-%d-%H%M).tgz -C /var/lib/docker/volumes/website/_data .

## Verify that the data was backed up properly:
ls -l /tmp/website_*.tgz

## List the contents of the tgz file:
tar tf <BACKUP_FILE_NAME>.tgz

## Exit out of root:
exit

## Run a new container using the website volume, and create a backup:
docker run -it --rm -v website:/website -v /tmp:/backup bash tar czf /backup/website_$(date +%Y-%m-%d-%H-%M).tgz -C /website .

## Verify that the data was backed up properly:
ls -l /tmp/website_*.tgz

## Switch to the root user:
sudo -i

## Change to the /docker/volumes/ directory:
cd /var/lib/docker/volumes/

## List the volumes:
ls -l

## Change to the website/_data directory:
cd website/_data/

## Remove the contents of the directory:
rm * -rf

## Verify that the backups are still available:
ls -l /tmp/website_*.tgz

## Restore the data to the current directory:
tar xf /tmp/<BACKUP_FILE_NAME>.tgz .

## Verify that the data was restored successfully:
ls -l

{% endhighlight %}
</details>
<hr style="3px solid #bbb">

## Storing Container Data in AWS S3

### Store the website data to AWS S3

#### Key Words
- First, Set up the AWS account and S3 bucket
- s3fs is a FUSE-based file system to mount the S3 bucket to the server

<details>
<summary>Script Example</summary>

{% highlight bash %}

# Configuration and Installation

## Install the awscli, while checking if there are any versions currently installed, and not stopping any user processes:
pip install --upgrade --user awscli

## Configure the CLI:
aws configure

## Enter the following:

### AWS Access Key ID: <ACCESS_KEY_ID>

### AWS Secret Access Key: <SECRET_ACCESS_KEY>

### Default region name: us-east-1

### Default output format: json

## Copy the CLI configuration to the root user:
sudo cp -r ~/.aws /root

## Install the s3fs package:
sudo yum install s3fs-fuse -y

# Prepare the Bucket

## Create a mount point for the s3 bucket:
sudo mkdir /mnt/widget-factory

## Export the bucket name:
export BUCKET=<S3_BUCKET_NAME>

## Mount the S3 bucket:
sudo s3fs $BUCKET /mnt/widget-factory -o allow_other -o use_cache=/tmp/s3fs

## Verify that the bucket was mounted successfully:
ll /mnt/widget-factory

## Copy the website files to the s3 bucket:
cp -r ~/widget-factory-inc/web/* /mnt/widget-factory

## Verify the files are in the folder:
ll /mnt/widget-factory

## Verify the files are in the s3 bucket:
aws s3 ls s3://$BUCKET

# Use the S3 Bucket Files in a Docker Container
## Run an httpd container using the S3 bucket:
docker run -d --name web1 -p 80:80 --mount type=bind,source=/mnt/widget-factory,target=/usr/local/apache2/htdocs,readonly httpd:2.4

## In a web browser, verify connectivity to the container:
<SERVER_PUBLIC_IP_ADDRESS>

## Check the bucket cache, then change to the /mnt/widget-factory/ directory:
cd /mnt/widget-factory

## Create a new page within the bucket:
cp index.html newPage.html

## In a web browser, verify that the new page is accessible:
<SERVER_PUBLIC_IP_ADDRESS>/newPage.html

## Verify that the page was added to the bucket:
aws s3 ls $BUCKET

{% endhighlight %}
</details>
<hr style="3px solid #bbb">

## Storing Container Data in AZ Storage

#### Key Points
- First create an AZ Storage 
- blobfuse is a FUSE-based file system to mount the Blob storage container to the server

<details>
<summary>Script Example</summary>

{% highlight bash %}

# Configuration and Installation
## Obtain the Azure login credentials:
az login
## Copy the code provided by the command.
## Open a browser and navigate to https://microsoft.com/devicelogin.
## Enter the code copied in a previous step and click Next.
## Use the login credentials from the lab page to finish logging in.
## Switch back to the terminal and wait for the confirmation.

# Prepare the Storage
## Find the name of the Storage account:
az storage account list | grep name | head -1

## Copy the name of the Storage account to the clipboard.
## Export the Storage account name:
export AZURE_STORAGE_ACCOUNT=<COPIED_STORAGE_ACCOUNT_NAME>
# Retrieve the Storage access key:
az storage account keys list --account-name=$AZURE_STORAGE_ACCOUNT

# Copy the key1 "value" for later use.
# Export the key value:
export AZURE_STORAGE_ACCESS_KEY=<KEY1_VALUE>

##  Install blobfuse:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm
sudo yum install blobfuse fuse -y

## Modify the fuse.conf configuration file:
sudo sed -ri 's/# user_allow_other/user_allow_other/' /etc/fuse.conf

# Use the Azure Blob Storage Container
## Create necessary directories:
sudo mkdir -p /mnt/widget-factory /mnt/blobfusetmp

## Change ownership of the directories:
sudo chown cloud_user /mnt/widget-factory/ /mnt/blobfusetmp/

## Mount the Blob Storage from Azure:
blobfuse /mnt/widget-factory --container-name=website --tmp-path=/mnt/blobfusetmp -o allow_other

## Copy website files into the Blob Storage container:
cp -r ~/widget-factory-inc/web/* /mnt/widget-factory/

## Verify the copy worked:
ll /mnt/widget-factory/

## Verify the files made it to Azure Blob Storage:
az storage blob list -c website --output table

## Run a Docker container:
docker run -d --name web1 -p 80:80 --mount type=bind,source=/mnt/widget-factory,target=/usr/local/apache2/htdocs,readonly httpd:2.4

## Once the command is complete, open a web browser and navigate to the public IP address of the server.
## Verify the website is up and running.

{% endhighlight %}
</details>
<hr style="3px solid #bbb">

## Building Container Images Using Dockerfiles

### Creating a container image by hand is possible, but it requires manual processes. There has to be a more automatic way to build images. Manual processes do not scale and are not easily version controlled. Docker provides a solution to this problem - the Dockerfile.

#### Key Points
- Every time the docker build with the Dockerfile is done, add a tag (:0.1)

<details>
<summary>Script Example</summary>

{% highlight bash %}
{% raw %}
# Build a First Version

## Change to the widget-factory-inc directory:
cd widget-factory-inc

## Create a Dockerfile that uses httpd:2.4 as the base image:
vim Dockerfile

## In the new file, insert the following:
FROM httpd:2.4
RUN apt update -y && apt upgrade -y && apt autoremove -y && apt clean && rm -rf /var/lib/apt/lists*

## Save the file:
ESC
:wq

## Verify that the file was saved successfully:
cat Dockerfile

## Build the 0.1 version of the widgetfactory image using the Dockerfile:
docker build -t widgetfactory:0.1 .

## Set variables to examine the image's size and layers:
export showLayers='{{ range .RootFS.Layers }}{{ println . }}{{end}}'
export showSize='{{ .Size }}'

## Compare the httpd and widgetfactory images:
docker images

## Show the widgetfactory image's size:
docker inspect -f "$showSize" widgetfactory:0.1

## Show the layers:
docker inspect -f "$showLayers" widgetfactory:0.1

## Show the layers of the httpd:2.4 image:
docker inspect -f "$showLayers" httpd:2.4

# Load the Website into the Container

## Open the Dockerfile:
vim Dockerfile

## Remove the Apache welcome page from the image by adding the following:
RUN rm -f /usr/local/apache2/htdocs/index.html

## Save the file:
ESC
:wq

## Build version 0.2 of the widgetfactory image:
docker build -t widgetfactory:0.2 .

## Inspect both versions of the widgetfactory image to see the differences in size and layers:
docker images

## Show the widgetfactory:0.1 image's size:
docker inspect -f "$showSize" widgetfactory:0.1

## Compare it to the image size for widgetfactory:0.2:
docker inspect -f "$showSize" widgetfactory:0.2

## Using an interactive terminal, check the htdocs folder for widgetfactory:0.2. Are the website files in the folder?:
docker run --rm -it widgetfactory:0.2 bash
ls htdocs

## Exit the container:
exit

## Show the layers for the widgetfactory:0.1 image:
docker inspect -f "$showLayers" widgetfactory:0.1

## Show the layers for the widgetfactory:0.2 image and compare the two:
docker inspect -f "$showLayers" widgetfactory:0.2

## Open the Dockerfile:
vim Dockerfile

## Add the website data to the container by adding the following to the end of the file:
WORKDIR /usr/local/apache2/htdocs
COPY ./web .

## Save the file:
ESC
:wq

## Build version 0.3 of the widgetfactory image:
docker build -t widgetfactory:0.3 .

## Inspect versions 0.2 and 0.3 to see the differences in size and layers:
docker images

## Show the widgetfactory:0.2 image's size:
docker inspect -f "$showSize" widgetfactory:0.2

## Compare it to the image size for widgetfactory:0.3:
docker inspect -f "$showSize" widgetfactory:0.3

## Show the layers for the widgetfactory:0.2 image:
docker inspect -f "$showLayers" widgetfactory:0.2

## Show the layers for the widgetfactory:0.3 image and compare the two:
docker inspect -f "$showLayers" widgetfactory:0.3

## Using an interactive terminal, check the htdocs folder for widgetfactory:0.3:
docker run --rm -it widgetfactory:0.3 bash

{% endraw %}
{% endhighlight %}
</details>
<hr style="3px solid #bbb">

## Docker Container Networking

### Each container should serve a single purpose, such as running one application like a web server. Containers can be powerful by themselves, but when connected together, they are far more useful. For example, a web server container can be connected to a database container to provide application storage. Docker provides multiple options for networking containers

#### Key Points
- Detached mode, shown by the option --detach or -d, means that a Docker container runs in the background of your terminal. It does not receive input or display output.
- add --rm to use it temporarily. when it is used with Busybox, it just ssh's into busybox
- Busybox can ssh into the httpd bridge network

<details>
<summary>Script Example</summary>

{% highlight bash %}
{% raw %}

# Explore the Default Network

## List the default networks:
docker network ls

## Run an httpd container named web1, without specifying a network, and see which network it uses:
docker run -d --name web1 httpd:2.4
docker inspect web1

## Take note of the IPAddress.
# Run a container using the busybox image, and see if you can connect to the web1 server:
docker run --rm -it busybox

# Check the container's networking, and verify it is in the same IP range as web1:
ip addr

## Ping the web1 container using the IP address:
ping <WEB1_IP_ADDRESS>

## Attempt to ping the web1 container by name:
ping web1
## Attempt to access web1 using wget:
wget <WEB1_IP_ADDRESS>

## Exit the container:
exit

# Explore Bridge Networks

## Create a new bridge network named test_application:
docker network create test_application

## Run an httpd container named web2, in the test_application network:
docker run -d --name web2 --network test_application httpd:2.4

## Check the status of the container:
docker ps -a

## Verify that web2 was added to the test_application network:
docker inspect web2

## Run a container using the busybox image, and see if you can connect to the web2 server, within the test_application network:
docker run --rm -it --network test_application busybox

## Check the container's networking, and verify it is in the same IP range as web2:
ip addr

## Ping the web2 container using the IP address:
ping <WEB2_IP_ADDRESS>

## Attempt to ping the web2 container by name:
ping web2

## Using wget, attempt to access web2 with the hostname:
wget web2

## Attempt to ping web1:
ping <WEB1_IP_ADDRESS>

## Attempt to access web1 using wget:
wget <WEB1_IP_ADDRESS>

## Exit the container:
exit

# Explore the Host Network

## Run an httpd container named web3 on the host network:
docker run -d --name web3 --network host httpd:2.4

## Check the status of the container:
docker ps -a

## Attempt to connect to web3 directly from the server:
wget localhost

## Stop web3:
docker stop web3

## Attempt to connect to web3 directly from the server again:
wget localhost

## Start web3:
docker start web3

## Run a container using the busybox image, and see if you can connect to the web3 server:
docker run --rm -it --network host busybox
ping web3

## Using wget, attempt to access localhost within the busybox image:
wget localhost

## Attempt to ping web2:
ping <WEB2_IP_ADDRESS>

## Attempt to ping web1:
ping <WEB1_IP_ADDRESS>

{% endraw %}
{% endhighlight %}
</details>
<hr style="3px solid #bbb">


<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>
# Tutorial: Running a Sample Docker Image

## Dockerize a Flask Applicaiton

### Deployment of a Flask Applicaiton

#### Key Points
- Dockerfile also builds a network in the directory 
- docker ps: Running Containers 
- docker images ls: current images
- once docker run --rm -it --network notes -p 80:80 notesapp:0.1 is run then do Ctrl+C to quit the container
- Once Dockerfile is edited, edit a tag using: docker build -t notesapp:0.2 .

<details>
<summary>Script Example</summary>

{% highlight bash %}
{% raw %}

# Create the Build Files
## Change to the notes directory:
cd notes

## List the files in the directory:
ls -la

## Inspect the config.py file:
cat config.py

import os

db_host = os.environ.get('DB_HOST', default='localhost')
db_name = os.environ.get('DB_NAME', default='notes')
db_user = os.environ.get('DB_USERNAME', default='notes')
db_password = os.environ.get('DB_PASSWORD', default='')
db_port = os.environ.get('DB_PORT', default='5432')

SQLALCHEMY_TRACK_MODIFICATIONS = False
SQLALCHEMY_DATABASE_URI = f"postgresql://{db_user}:{db_password}@{db_host}:{db_port}/{db_name}"

## Crate the .dockerignore file:
vim .dockerignore

# In the file, paste the following:
.dockerignore
Dockerfile
.gitignore
Pipfile.lock
migrations/

## Save the file:
ESC
:wq

## Create the Dockerfile:
vim Dockerfile

## In the file, paste the following:
FROM python:3
ENV PYBASE /pybase
ENV PYTHONUSERBASE $PYBASE
ENV PATH $PYBASE/bin:$PATH
RUN pip install pipenv

WORKDIR /tmp
COPY Pipfile .
RUN pipenv lock
RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile

COPY . /app/notes
WORKDIR /app/notes
EXPOSE 80
CMD ["flask", "run", "--port=80", "--host=0.0.0.0"]

# Build and Setup Environment
## Build the notesapp image:
docker build -t notesapp:0.1 .

## Check the status of the image:
docker images

## List the containers:
docker ps -a

## List the docker networks:
docker network ls

## Run a container using the notesapp image and mount the migrations directory:
docker run --rm -it --network notes -v /home/cloud_user/notes/migrations:/app/notes/migrations notesapp:0.1 bash

## Once connected to the container, enable SQLAlchemy:
flask db init

## Check the migrations folder:
ls -l migrations

## Create the files needed to configure the database:
flask db migrate

## Apply the files:
flask db upgrade

# Run, Evaluate, and Upgrade
## Run a container using the notesapp:0.1 image:
docker run --rm -it --network notes -p 80:80 notesapp:0.1

## Using a web browser, navigate to the public IP address for the server.
## Sign up for a new account using an email address and password.
## Once you are signed up, log in to your account.
## Create your first note.
## Verify that you can edit the note.

## Back in the terminal, disable Debug mode by editing the .env file:
vim .env

## Remove the export FLASK_ENV='development' line.
## Save the file:
ESC
:wq

## Build the image again:
docker build -t notesapp:0.2 .

## Run a container using the updated image:
docker run --rm -it --network notes -p 80:80 notesapp:0.2

## In a web browser, navigate to the public IP address for the server, and log in to your account.
## Verify that you can add a second note.

## In the terminal, stop the container:
CTRL+C

# Upgrade to Gunicorn
## Check the Pipfile:
cat Pipfile

## Run a container and modify the pip file:
docker run --rm -it -v /home/cloud_user/notes/Pipfile:/tmp/Pipfile notesapp:0.2 bash

## Once connected, change to the /tmp directory:
cd /tmp

## Add gunicorn to the list of dependencies:
pipenv install gunicorn

## Exit the container:
exit

## Verify that gunicorn was added under [packages]:
cat Pipfile

## Modify the init.py script:
vim __init__.py

from dotenv import load_dotenv, find_dotenv
load_dotenv(find_dotenv())

## Beneath the import section, add the following:
from dotenv import load_dotenv, find_dotenv
load_dotenv(find_dotenv())

## Save the file:
ESC
:wq

## Open the Dockerfile:
vim Dockerfile

## To the bottom of the file, make the following changes:
COPY . /app/notes
WORKDIR /app
EXPOSE 80
CMD ["gunicorn", "-b 0.0.0.0:80", "notes:create_app()"]

## Save the file:
ESC
:wq

# Build a Production Image
## Build the updated notesapp image:
docker build -t notesapp:0.3 .

## Run a detached container using the updated image:
docker run -d --name notesapp --network notes -p 80:80 notesapp:0.3

## Check the status of the container:
docker ps -a

## In a web browser, navigate to the public IP address for the server, and log in to your account.
## pVerify that you can create a new note.

{% endraw %}
{% endhighlight %}
</details>
<hr style="3px solid #bbb">