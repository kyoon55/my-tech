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

- Detail about umask: <https://www.cyberciti.biz/tips/understanding-linux-unix-umask-value-usage.html>

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

# Using Input/Output Redirection and Analyzing Text on RHEL 8

## Log in to server1

Use the credentials provided in the lab:

```bash
ssh cloud_user@<PUBLIC_IP_ADDRESS>
```

## Use Input/Output Redirection

### Create a Server Health Log File

1. Create a server health log file that contains a sequential number of outputs with the hostname, date and time, and a simple header:

```bash
{ echo " " ; echo "==== `date` on `hostname` ====" ; df -hT ; }
```

2. Rerun the previous command, and output this to a text file:

```bash
{ echo " " ; echo "==== `date` on `hostname` ====" ; df -hT ; } > `hostname`-health.txt
```

3. Review the output:

```bash
cat server1-health.txt
```

- Observe what was added to the file.

4. Rerun the first echo command to create the server health log file twice more:

```bash
{ echo " " ; echo "==== `date` on `hostname` ====" ; df -hT ; } > `hostname`-health.txt
```

- Review the output using `cat server1-health.txt` again to see how many times the command was run.

5. Change the server health log file to contain a double redirect to ensure that the initial and subsequent outputs are not overwritten:

```bash
{ echo " " ; echo "==== `date` on `hostname` ====" ; df -hT ; } >> `hostname`-health.txt
```

- Run the above command 2 more times.
- Inspect the output again using `cat server1-health.txt`.

## Search for Files in the /home Directory

1. Find all the files in the `/home` directory owned by the `cloud_user`:

```bash
find /home -user cloud_user
```

- Observe a long list of filenames, along with 2 Permission denied errors at the end.

2. Generate clean lines of output without the 2 errors:

```bash
find /home -user cloud_user 2> /dev/null
```

3. Find out how many clean lines of output there are:

```bash
find /home -user cloud_user 2> /dev/null | wc -l
```

4. Save the output to a text file:

```bash
find /home -user cloud_user 2> /dev/null > cloud_user-files.txt
```

5. Ensure the filenames are in the text file:

```bash
cat cloud_user-files.txt
```

6. Number the list of filenames, and save them in another text file:

```bash
nl cloud_user-files.txt > numberedfiles.txt
```

7. Ensure the numbered filenames are in the text file:

```bash
cat numberedfiles.txt
```

8. Sort the numbered lines:

```bash
sort numberedfiles.txt
```

- Observe that because there were no leading zeroes in front of the lower numbers, it doesn't sort properly.

9. To fix this, run a numeric sort:

```bash
sort -n numberedfiles.txt
```

10. Generate a list showing the first-level directories inside `/etc`:

```bash
find /etc -maxdepth 1
```

- Observe the output includes files that go 1 level further than then `/etc` directory.

11. Add on to the previous command to generate a list showing the space used for the first-level directories inside `/etc`:

```bash
find /etc -maxdepth 1 -iname "*.*" -exec du -sh {} \;
```

- Observe the previous list now includes total space usage for each item.

12. Rerun the following command, and sort it by space usage from least to most used:

```bash
find /etc -maxdepth 1 -iname "*.*" -exec du -sh {} \; | sort -h
```

13. Rerun the previous command, and this time, output it to `etc-space-usage.txt`:

```bash
find /etc -maxdepth 1 -iname "*.*" -exec du -sh {} \; | sort -h > etc-space-usage.txt
```

- You shouldn't see any output aside from 2 directory errors.

14. Review the file:

```bash
less etc-space-usage.txt
```

- Observe the files listed and sorted by space usage.

## Use grep and Regular Expressions to Analyze Text

1. Find all the files owned by the `cloud_user` in the `/home` directory:

```bash
find /home -user cloud_user
```

2. Find all the files owned by the `cloud_user` in the `/home` directory that contain the word "file":

```bash
find /home -user cloud_user | grep -i file
```

- Notice in the output that it only searches for the word "file" in the actual filenames rather than within the contents of the file.

3. Use the `-exec` feature of `find` to find the word "file" in the files themselves:

```bash
find /home -user cloud_user -exec grep -i file {} \;
```

4. Find out how many lines of output were found:

```bash
find /home -user cloud_user -exec grep -i file {} \; | wc -l
```

- You should see 140 lines at the bottom of the output.

5. Count how many lines were generated without errors:

```bash
find /home -user cloud_user -exec grep -i file {} \; 2> /dev/null | wc -l
```

- You should again see 140 lines. Note that this counts only the standard out rather than the standard error.

6. Utilize the `grep` command directly to find the word "file" inside all files owned by `cloud_user` in the `/home` directory:

```bash
grep -ir file /usr/share/doc/zip
```

7. Add a total count of the output to the bottom of the list:

```bash
grep -ir file /usr/share/doc/zip ; !! | wc -l
```

- You should see 965 at the bottom.

8. Run a case-sensitive search for only the word "file" as lowercase:

```bash
grep -ir file /usr/share/doc/zip ; grep -r file /usr/share/doc/zip | wc -l
```

- You should see 925 at the bottom.

9. Create a text file containing all the search results for the word "file":

```bash
grep -ir file /usr/share/doc/zip ; grep -r file /usr/share/doc/zip > grepoutput.txt
```

10. Check the text file output:

```bash
cat grepoutput.txt
```

## Managing Files and Directories on RHEL 8

- Check the size of the directory:
  
```bash
du -sh /user/share/doc
```

- Create a tar file from a directrory

```bash
tar -cf documentation.tar /usr/share/doc
```

- List the entries in the tar file (-v with more details)

```bash
tar -tfv documentation.tar
```

- Uncompress the tar.gz file

```bash
tar -xzvf documentation.tar.gz
```

- Using gzip, compress the file

```bash
gzip file wget-1.19.5-8.el8_1.1.x86_64.rpm.gz
```

- Using gzip, compress the whole file in the directory

```bash
gzip *
```

- Create a multiple file in the file:

```bash
touch ~/{file1,file2,file3}.cf
```

- Link, Hard Link, and Soft Link
  - Link: between the file name and the actual data stored
  - Hard Link: the permissions, link count, ownership, timestamps, and file content are the same
    - If the original file is deleted, the data exists in the hard link
    - Only deleted when all links are deleted
  - Soft Link (Symbolic Link): links together non-regular and regular files
    - Can span multiple filesystems
    - not a standard file
    - if origianl file is lost, the soft link is deleted
    - ```bash
    $ ls -l soft_link_test /tmp/soft_link_new
-rw-rw-r--. 1 tcarrigan tcarrigan 17 Aug 30 11:59 soft_link_test
lrwxrwxrwx. 1 tcarrigan tcarrigan 35 Aug 30 12:09 /tmp/soft_link_new -> /home/tcarrigan/demo/soft_link_test```

- Create a soft link

```bash
ln -s original softlink (file)
```

- Create a soft link (directory)

```bash
ln -s directory otherdirectory
```

- Create a hard link

```bash
ln original hardlink
```

- Add a user named "snuffy' and create a password for the user

```bash
useradd -m -g users -G devops snuffy
passwd snuffy
```

- Chmod user: only groupos

```bash
chown .users ~/directory/
```

- chmod write capabilities for the group

```bash
chmod g+w ~/directory/
```

- Update the permissions in the directory so that the users can only delete files that are owned by themselves

```bash
chmod +t /directory/
```