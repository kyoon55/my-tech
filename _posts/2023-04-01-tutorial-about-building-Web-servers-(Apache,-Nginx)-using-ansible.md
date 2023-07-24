---
title: tutorial about building Web servers (Apache, Nginx) using ansible
sidebar: news
tags: [news]
permalink: tutorial-about-building-Web-servers-(Apache,-Nginx)-using-ansible.html
---
# Building Web Servers (Apache, Nginx) using Ansible

## Introduction

In this tutorial, we will explore how to use Ansible to build web servers using Apache and Nginx. Ansible is an open-source automation tool that allows you to automate IT tasks such as server provisioning, configuration management, and application deployment.

## Prerequisites

Before we begin, make sure you have the following prerequisites installed on your system:

- Ansible (version 2.9 or higher)
- SSH client

## Step 1: Setting up Ansible Inventory

The Ansible inventory file is where we define the list of servers we want to manage using Ansible. Create a new file called `inventory.yml` and add the following content:

```
[webservers]
web1 ansible_host=10.0.0.1
web2 ansible_host=10.0.0.2
```

Replace the IP addresses with the actual IP addresses of your web servers.

## Step 2: Setting up Ansible Playbook

An Ansible playbook is a YAML file that defines a set of tasks to be executed on remote servers. Create a new file called `webserver.yml` and add the following content:

```yaml
---
- name: Configure Apache
  hosts: webservers
  become: true
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present

    - name: Start Apache service
      systemd:
        name: apache2
        state: started
        enabled: true

- name: Configure Nginx
  hosts: webservers
  become: true
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present

    - name: Start Nginx service
      systemd:
        name: nginx
        state: started
        enabled: true
```

This playbook defines two plays: one to configure Apache and another to configure Nginx. The tasks under each play install the respective web servers and ensure their services are started and enabled.

## Step 3: Running the Ansible Playbook

To run the Ansible playbook and configure the web servers, use the following command:

```shell
ansible-playbook -i inventory.yml webserver.yml
```

Ansible will connect to the specified servers using SSH and execute the tasks defined in the playbook.

## Step 4: Verifying the Configuration

After the playbook execution completes, you can verify if Apache and Nginx are correctly installed and running on the web servers. Open a web browser and enter the IP address of one of your web servers. If you see the default Apache or Nginx landing page, it means the setup was successful.

## Conclusion

In this tutorial, you learned how to use Ansible to automate the configuration of web servers using Apache and Nginx. Ansible greatly simplifies the process of provisioning and managing servers, making it an ideal tool for infrastructure automation.

Feel free to explore more Ansible modules and playbooks to further customize and enhance your web server setup.

Happy automating!

{% include links.html %}
