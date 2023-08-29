---
layout: default
title: RHCSA Overview
parent: Linux
nav_order: 1
date: 2023-01-15
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>

## Accessing and Administering Linux Servers

### Access Local and Remote Systems
- Access a server via the console.
  - **Physical hardware:** Use a keyboard, monitor, and mouse.
  - **Virtual machine:** Use a software console (maybe in a web browser).

### Switching Users
#### **su** command:
- `su - root` - Switch to root user (requires root password).
- `su -` - Same as above.

#### **sudo** command:
- `sudo -i` - Open an interactive shell as root.
- Execute commands directly: `sudo <command>`. Example: `sudo dnf update`.

### SSH Commands Suite
Secure methods for remote operations.

#### Setting up passwordless login:
1. Become the web_user: `su - web_user`.
2. Generate SSH keys: `ssh-keygen` (use defaults).
3. Copy key to the other server: `ssh-copy-id web_user@<<other_server>>`.
4. Test login: `ssh web_user@<<other_server>>`.

#### Remote command execution:
- Execute a remote command: `ssh web_user@<<other_server>> <command>`. Example: `ssh web_user@<<other_server>> cat /etc/redhat-release`.

#### File transfer with **scp**:
- `scp web_user@<<other_server>>:/path/to/file /local/destination`.
  
#### Switching to db_user:
- `sudo su - db_user`.

#### File transfer with **sftp**:
- `sftp db_user@<<other_server>>`: Gives an encrypted FTP-like session.

### Resources
- **Chapter 4**: Managing user and group accounts in Red Hat Enterprise Linux 8 | Red Hat Customer Portal.

### Tasks Overview:
1. Check for outstanding patches.
2. Grab web content for the web_user account.
3. Help db_user retrieve database dumps from another server. 

Note: All operations are based on a RHEL 8 server set up by an intern with DHCP networking. Only cloud_user login credentials are available.