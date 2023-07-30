---
layout: default
title: Terraform Deployment of basic infrastructure on AWS
parent: Terraform
nav_order: 1
permalink: /docs/Terraform/doc-1/
date: 2023-03-05
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>

<details>
<summary>Terraform main.tf file to deploy basic infrastructure</summary>
{% highlight tf %}
terraform {
  required_version = ">= 0.14.5"

  required_providers {
    local = {
      source  = "hashicorp/local"
      version = "2.0.0"
    }
    tls = {
      source  = "hashicorp/tls"
      version = "3.0.0"
    }
  }
}

provider "local" {
}

provider "tls" {
}

resource "tls_private_key" "ssh_key" {
  algorithm   = "RSA"
  rsa_bits    = 4096
}

resource "local_file" "cloud_config" {
  filename = "cloud-config.yaml"
  content  = <<-EOT
write_files:
    - path: /etc/app/key
    permissions: '0600'
    content: |
        ${tls_private_key.ssh_key.private_key_pem}
    - path: /etc/app/key.pub
    permissions: '0644'
    content: |
        ${tls_private_key.ssh_key.public_key_openssh}
EOT
}
{% endhighlight %}