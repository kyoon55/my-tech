---
layout: default
title: Create a host, ssh into the public EC2 instnace
parent: Ansible
nav_order: 1
date: 2023-01-12
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>

<details>
<summary>Playbook to ssh into an EC2 instance</summary>
{% highlight yaml %}
- name: SSH into EC2 instances and echo "hello world"
  hosts: public
  remote_user: ec2-user
  become: true

  vars:
    ansible_ssh_private_key_file: "Key file"

  tasks:
    - name: Run "echo hello world" command
      command: echo "hello world"
      register: result

    - name: Display the output
      debug:
        var: result.stdout
{% endhighlight %}
</details>

## Test
```bash

# Store hosts file
cat << EOF > /etc/ansible/hosts
[public]
123.123.123.123
EOF

# Run a playbook

ansible-playbook playbook.yaml
```
