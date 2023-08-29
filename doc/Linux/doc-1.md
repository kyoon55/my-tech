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

### Tasks Overview:
1. Check for outstanding patches.
2. Grab web content for the web_user account.
3. Help db_user retrieve database dumps from another server. 


## Linux System Documentation

Linux offers comprehensive system documentation. This lesson covers 'man' pages, the 'info' command, and the /usr/share/doc directory.

### **Objective:**
Install Apache for web developers on RHEL 8, confirm documentation presence, and share the Apache license file. Also, pre-install MariaDB anticipating future needs.

### **Checking for Apache/MariaDB Documentation**
1. **Man Pages**
    ```bash
    man httpd
    man mariadb
    man mysql
    ```

2. **Info Pages**
    ```bash
    info httpd
    info mariadb
    info mysql
    ```

3. **Other System Documentation**
    ```bash
    ls -la /usr/share/doc | egrep -i "httpd|mariadb|mysql"
    ```

### **Installing Apache and MariaDB**
```bash
sudo dnf -y install httpd mariadb
```

### **Using Built-in Command Help**
1. **MariaDB Help**
    ```bash
    mariadb --help
    mariadb -?
    ```

2. **Help for Man & Info**
    ```bash
    man --help
    info --help
    ```

### **Using Apache/MariaDB Documentation**
1. **Man Pages**
    - Check documentation:
        ```bash
        man httpd
        man mariadb
        ```

    - Discover man pages:
        ```bash
        whatis httpd
        whatis httpd mariadb mysql
        ```

    - Additional information:
        ```bash
        apropos httpd
        apropos httpd mariadb
        ```

    - Access a specific man page by page number:
        ```bash
        man 8 httpd
        ```

    - Man command info:
        ```bash
        man man
        man info
        ```

2. **Info Pages**
    - Check documentation:
        ```bash
        info httpd
        info mariadb
        ```

    - Info command info:
        ```bash
        info info
        info man
        ```

3. **System Documentation in /usr/share/doc**
    - Locate Apache documentation:
        ```bash
        ls -la /usr/share/doc | egrep -i "httpd|mariadb|mysql"
        ```

    - Change to the Apache documentation directory:
        ```bash
        cd /usr/share/doc/httpd
        ls -la
        ```

    - View the license file:
        ```bash
        more LICENSE
        ```

This documentation assists users in exploring the in-depth details of various commands and utilities in Linux.