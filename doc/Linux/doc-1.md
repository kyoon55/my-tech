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

## Manipulating Text Files in Linux

In the daily life of a Linux system administrator, text files are everywhere, forming the core of the system's configuration and software. Editing and manipulating these files, especially text files, is crucial.

### **Resources:**
- Mastering Regular Expressions
- Getting started with regular expressions: An example

### **Objective:**
Create a new system log to monitor httpd events on RHEL 8 for web developers. They need a log aggregating all httpd web server-related logs in a text file for report generation.

### **Steps:**

1. **Setting Up**
    - Log in as `root`, then switch to `web_user` (this user will own the logs).
    - Create directories:
        ```bash
        mkdir -p log_project/raw_logs
        mkdir log_project/processed_logs
        cd log_project/
        ```

2. **Creating the Master Log**
    - Create an empty "master" log:
        ```bash
        touch raw_logs/master.log
        ```
    - View structure of `/var/log`:
        ```bash
        sudo tree /var/log | more
        ```
    - Generate the "master" log with references to `httpd`:
        ```bash
        sudo grep httpd /var/log/* > raw_logs/master.log 2> errors.log
        ```
    - Examine the error logs and move them:
        ```bash
        cat errors.log
        mv errors.log raw_logs
        ```
    - Update the "master" log without error messages:
        ```bash
        sudo grep httpd /var/log/* > raw_logs/master.log 2>/dev/null
        ```

3. **Incorporating the Systemd Journal**
    - Append the systemd journal logs to the "master" log:
        ```bash
        journalctl --unit=httpd --no-pager >> raw_logs/master.log
        ```
    - Backup the "master" log:
        ```bash
        cp raw_logs/master.log /tmp
        ```

4. **Using the `grep` Command & Regular Expressions**
    - Reorganize directory structure:
        ```bash
        mv processed_logs httpd_logs
        mkdir error_logs
        mv -v raw_logs/errors.log error_logs
        ```
    - Examine logs and extract specific lines:
        ```bash
        grep systemd raw_logs/master.log > httpd_logs/systemd.log
        egrep -v "dnf|secure" raw_logs/master.log > httpd_logs/no_dnf_secure.log
        ```

5. **Cleaning Up**
    - Remove the unnecessary error logs directory:
        ```bash
        rm -rf error_logs
        ```

### **Conclusion:**
After setting up the desired logs, a proof of concept was completed. The next steps will involve a meeting with the web team to discuss further actions.