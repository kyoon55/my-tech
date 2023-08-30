---
layout: default
title: RHCSA - Understanding and Using Essential Tools on RHEL 8
parent: Linux
nav_order: 1
date: 2023-01-15
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>

# Table Of Content

# Accessing and Administering Linux Servers

### Access Local and Remote Systems

- Access a server via the console.
  - **Physical hardware:** Use a keyboard, monitor, and mouse.
  - **Virtual machine:** Use a software console (maybe in a web browser).

### Switching Users

#### **su** command

- `su - root` - Switch to root user (requires root password).
- `su -` - Same as above.

#### **sudo** command

- `sudo -i` - Open an interactive shell as root.
- Execute commands directly: `sudo <command>`. Example: `sudo dnf update`.

### SSH Commands Suite

Secure methods for remote operations.

#### Setting up passwordless login

1. Become the web_user: `su - web_user`.
2. Generate SSH keys: `ssh-keygen` (use defaults).
3. Copy key to the other server: `ssh-copy-id web_user@<<other_server>>`.
4. Test login: `ssh web_user@<<other_server>>`.

#### Remote command execution

- Execute a remote command: `ssh web_user@<<other_server>> <command>`. Example: `ssh web_user@<<other_server>> cat /etc/redhat-release`.

#### File transfer with **scp**

- `scp web_user@<<other_server>>:/path/to/file /local/destination`.
  
#### Switching to db_user

- `sudo su - db_user`.

#### File transfer with **sftp**

- `sftp db_user@<<other_server>>`: Gives an encrypted FTP-like session.

### Tasks Overview

1. Check for outstanding patches.
2. Grab web content for the web_user account.
3. Help db_user retrieve database dumps from another server.

# Linux System Documentation

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

# Manipulating Text Files in Linux

In the daily life of a Linux system administrator, text files are everywhere, forming the core of the system's configuration and software. Editing and manipulating these files, especially text files, is crucial.

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

# Compressing and Decompressing Files in Linux

## Introduction

For Linux system administrators, compressing and archiving files/directories is a core skill. During the lifecycle of an RHEL 8 server, managing archives becomes essential. Whether it's for installing software, transferring data, or simply saving disk space and improving network transfers, knowing how to work with compressed files is vital.

## Housekeeping in `/home` Directory

The `/home` directory has become cluttered due to inactive user accounts, leftover project files, and more. Cleaning up and organizing the directory requires utilizing both archiving and compression techniques.

### Archiving Orphaned Home Directories

1. Check filesystem utilization:

    ```bash
    df -h /
    ```

2. Create a new archive directory:

    ```bash
    cd /home
    mkdir /home/archives
    ```

3. Archive user directories using `tar` and `star`:

    ```bash
    tar cvf /home/archives/user1.tar user1
    star -cv file=/home/archives/user1.star user1
    ```

4. Compress the archives with `gzip` and `bzip2`:

    ```bash
    gzip archives/user1.tar
    bzip2 archives/user1.star
    ```

5. Inline compression with `tar/gzip` and `star/bzip2`:

    ```bash
    tar cvfz /home/archives/user2.tar.gz user2
    star -cv -bz file=/home/archives/user2.star.bz2 user2
    ```

6. Clean up and archive for multiple users:

    ```bash
    rm -f archives/*.tar.*
    star -cv -bz file=/home/archives/user3.star.bz2 user3
    rm -rf user{1..5}
    ```

### Cleaning up Miscellaneous Files

1. Move and compress ISO files:

    ```bash
    mv -v *.iso archives
    bzip2 archives/*.iso
    ```

### Archiving Project Data

1. Archive project data in `tar/gzip` format:

    ```bash
    tar cvfz archives/project_archive.tar.gz project{1..5}
    rm -rf project{1..5}
    ```

## Restoration and Decompression

### Unarchiving a Compressed Archive

1. Restore `/home/user1` directory:

    ```bash
    tar tvfj archives/user1.star.bz2
    cd /home
    tar xvfj archives/user1.star.bz2
    ```

### Uncompressing a Compressed File

1. Uncompress the specified ISO file:

    ```bash
    bzip2 -d archives/CentOS-8.2.2004-x86_64-boot.iso.bz2
    ```

# SSH and `su` Command Lab Guide

## **Introduction**
This lab guides us through the use of the `su` command, delves into the capabilities of the `ssh` command, demonstrates the setup for key-based authentication in SSH, and explores SSH utilities for secure file transfers.

## **Tasks**

### 1. **Log in and Switch Users**
- Login to the server:
    ```bash
    ssh cloud_user@<PUBLIC_IP_ADDRESS>
    ```
- Check the current user and groups:
    ```bash
    whoami ; groups
    id
    ```
- Become the root user and configure the root user's environment:
    ```bash
    sudo -i
    echo export SOURCED1=.bash_profile >> ~/.bash_profile ; echo 'echo $SOURCED1' >> ~/.bash_profile
    echo export SOURCED2=.bashrc >> ~/.bashrc ; echo 'echo $SOURCED2' >> ~/.bashrc
    exit
    ```
- Check the configured environment:
    ```bash
    grep SOURCED .bash_profile
    grep SOURCED .bashrc
    ```
- Reset sudo timeout:
    ```bash
    sudo -k
    ```
- Observe the difference between `sudo`, `sudo -i`, `su`, and `su -`.

### 2. **Access Remote Systems Using SSH**
- Connect to another cloud server:
    ```bash
    ssh cloud_user@<SECOND_PUBLIC_IP_ADDRESS>
    ```
- Change the password for `cloud_user` and gather some system information:
    ```bash
    sudo -i passwd cloud_user
    top
    hostname
    df -hT
    exit
    ```
- Fetch and store some system information locally:
    ```bash
    ssh -t cloud_user@<SECOND_PUBLIC_IP_ADDRESS> df -hT >> server_health.txt
    ssh -t cloud_user@<SECOND_PUBLIC_IP_ADDRESS> free >> server_health.txt
    cat server_health.txt
    ```

### 3. **Configure Key-Based Authentication for SSH**
- Generate an SSH key pair:
    ```bash
    ssh-keygen
    ```
- Copy the public key to the second server:
    ```bash
    ssh-copy-id <SECOND_PUBLIC_IP_ADDRESS>
    ```
- Test key-based login:
    ```bash
    ssh cloud_user@<SECOND_PUBLIC_IP_ADDRESS>
    exit
    ```
- Start the ssh-agent and add the private key:
    ```bash
    eval $(ssh-agent -s)
    ssh-add
    ```

### 4. **Securely Transfer Files between Systems**
- Execute a backup on a remote system and transfer the files securely:
    ```bash
    ssh cloud_user@<SECOND_PUBLIC_IP_ADDRESS> tar -czvf wget-server2.tar.gz wget-1*.rpm
    scp cloud_user@<SECOND_PUBLIC_IP_ADDRESS>:~/wget-server2*.* .
    ls -l
    ```

# Accessing Linux Systems Using RHEL 8

### **Log in and Switch Users**
- Log in to the server using the credentials provided:
  ```bash
  ssh cloud_user@<PUBLIC_IP_ADDRESS>
  ```
- To get information on who we are, use:
  ```bash
  whoami ; groups
  ```
- For more information on our user ID, primary group ID, and groups that we're part of, run this command:
  ```bash
  id
  ```
- To determine what files get run when we use different commands to elevate our privileges, become the root user:
  ```bash
  sudo -i
  ```
- Enter the following command to export and echo SOURCED1 into the root user's bash_profile:
  ```bash
  echo export SOURCED1=.bash_profile >> ~/.bash_profile ; echo 'echo $SOURCED1' >> ~/.bash_profile
  ```
- Make sure both lines are there:
  ```bash
  grep SOURCED .bash_profile
  ```
- Enter the following command to export and echo SOURCED2 into the root user's .bashrc:
  ```bash
  echo export SOURCED2=.bashrc >> ~/.bashrc ; echo 'echo $SOURCED2' >> ~/.bashrc
  ```
- Make sure both SOURCED lines are there:
  ```bash
  grep SOURCED .bashrc
  ```
- Exit by simply entering:
  ```bash
  exit
  ```
- Kill the timeout:
  ```bash
  sudo -k
  ```
- To see the echoes, enter:
  ```bash
  sudo -i echo
  ```
- Type in the cloud_user's password to see the echoes.
- Set the root user's password:
  ```bash
  sudo -i passwd root
  ```
- Enter a new password for the root user.
- Run the command to see the cloud_user's path:
  ```bash
  su -c 'echo $PATH'
  ```
- Enter the root user's password. This should show the cloud_user's path.
- Run the command to sign in as the root user and show the cloud_user's path:
  ```bash
  su - -c 'echo $PATH'
  ```
- Enter the root user's password. This should show the root user's profile, bashrc, and path.

  Understand that `sudo` equals the `cloud_user`, but `sudo -i` equals the `root user`. `su` also equals the `cloud_user`, while `su -` equals the `root user`.

### **Access Remote Systems Using SSH**
- Connect to a remote system on the second cloud server:
  ```bash
  ssh cloud_user@<SECOND_PUBLIC_IP_ADDRESS>
  ```
- When asked if you want to continue connecting, type "yes".
- Enter the password for the remote system on the second cloud server.
- Change the password:
  ```bash
  sudo -i passwd cloud_user
  ```
- Enter first the old password and then a new password that you'd prefer to use.
- To get some information on the remote system, query the remote system by running the following commands:
  ```bash
  top
  hostname
  df -hT
  ```
- Exit out of the server:
  ```bash
  exit
  ```
- Return the remote system's output to the initial system:
  ```bash
  ssh -t cloud_user@<SECOND_PUBLIC_IP_ADDRESS> df -hT >> server_health.txt
  ```
- Enter the password for the remote system on the second cloud server.
- If you see an error message, pull up the server health file:
  ```bash
  cat server_health.txt
  ```
- See the free memory on the server with the `free` command:
  ```bash
  ssh -t cloud_user@<SECOND_PUBLIC_IP_ADDRESS> free >> server_health.txt
  ```
- Enter the password for the remote system on the second cloud server.
- Get the information and see the text file about the server health file:
  ```bash
  cat server_health.txt
  ```

### **Configure Key-Based Authentication for SSH**
- Generate a public/private key pair using the defaults on the first cloud server:
  ```bash
  ssh-keygen
  ```
- Hit Enter and type in a passphrase, ideally something that's easy to remember. This should give you your randomart image and ID.
- Copy that ID to the second cloud server, also known as the remote server:
  ```bash
  ssh-copy-id <SECOND_PUBLIC_IP_ADDRESS>
  ```
- Type in the password for the second cloud server.
- Connect to the remote server:
  ```bash
  ssh cloud_user@<SECOND_PUBLIC_IP_ADDRESS>
  ```
- Type in the passphrase that you created a few steps ago to test if the key is working.
- Exit out of the remote server:
  ```bash
  exit
  ```
- Add the `cloud_user` identity to the agent and to reload the agent:
  ```bash
  eval $(ssh-agent -s)
  ```
- Add your `cloud_user` identity to the agent, which can now act on your behalf:
  ```bash
  ssh-add
  ```
- Type in your passphrase.
- Connect to the remote server:
  ```bash
  ssh cloud_user@<SECOND_PUBLIC_IP_ADDRESS>
  ```

### **Securely Transfer Files between Systems**
- Execute a backup command on a remote system:
  ```bash
  ssh cloud_user@<SECOND_PUBLIC_IP_ADDRESS> tar -czvf wget-server2.tar.gz wget-1*.rpm
  ```
- Hit the Up arrow and perform an `scp`:
  ```bash
  scp cloud_user@<SECOND_PUBLIC_IP_ADDRESS>:~/wget-server2*.* .
  ```
- Check the home directory to make sure all the files have been transferred:
  ```bash
  ls -l
  ```
