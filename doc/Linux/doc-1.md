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

# Working with Linux Files - Permissions

## Introduction
A key aspect of Linux security is managing file and directory access. When files or directories are created, their permissions are determined by the 'umask' of the session. Linux also offers the convenience of using hard and soft links as shortcuts to files. This lesson covers:
- Standard Linux `ugo/rwx` permissions model.
- Setting and troubleshooting permissions.
- Understanding 'umask', its configuration, and functioning.
- How to utilize hard and soft links.

## Resources
- [Managing file permissions Red Hat Enterprise Linux 8 | Red Hat Customer Portal](#)
- [File-system permissions - Wikipedia](#)

## Instructions

### 1. Setting up New Storage Location

- **Become Root**:
  ```bash
  sudo -i
  ```

- **Create a new storage location and move data**:
  ```bash
  mkdir /web_data
  mv -v /var/www/* /web_data
  ```

- **Adjusting ownership and permissions**:
  ```bash
  chown -R web_user:web_group /web_data
  chmod -R g+w /web_data
  mkdir /web_data/db_stuff
  chown -R db_user:web_group /web_data/db_stuff
  chmod -R 775 /web_data
  chmod -R 770 /web_data/db_stuff
  ```

### 2. Test Permissions

- **For web_user**:
  ```bash
  su - web_user
  echo Hello! > /web_data/html/index.html
  cat /web_data/html/index.html
  exit
  ```

- **For db_user**:
  ```bash
  su - db_user
  echo For DBAs only! > /web_data/db_stuff/README
  cat /web_data/db_stuff/README
  exit
  ```

- **For cloud_user**:
  ```bash
  su - cloud_user
  echo I wanna play too! > /web_data/html/play.html
  cat /web_data/html/index.html
  groups cloud_user
  cat /web_data/db_stuff/README
  exit
  ```

### 3. Create Links to New Location

- **Soft links for directories**:
  ```bash
  ln -s /web_data/* /var/www
  ```

- **Hard link for DBA README**:
  ```bash
  su - db_user
  ln /web_data/db_stuff/README .
  rm -f /web_data/db_stuff/README
  exit
  ```

### 4. Cleanup and Addressing Broken Symlinks

- **Deleting the db_stuff Directory and addressing broken symlinks**:
  ```bash
  rm -rf /web_data/db_stuff
  rm -f /var/www/db_stuff
  ```

### 5. Adjust the web_user Umask

- **Setting Umask for web_user**:
  ```bash
  su - web_user
  echo 'umask 0027' >> ~/.bashrc
  exit
  su - web_user
  touch testfile
  ls -la
  ```
