---
layout: default
title: RHCSA - Creating Simple Shell Scripts, from A Cloud Guru
parent: Linux
nav_order: 1
date: 2023-01-15
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>


# Shell Scripts for System Administrators

Shell scripts are an important tool in a system administratorʼs toolbox. Whether you use them for automation, reporting, or on the command line to process large volumes of repetitive work, itʼs important to have an understanding of how to make scripts work for you. In this lesson, we will take a look at some basic shell scripting concepts.

## Instructions:

### We Need to Simplify User Creation!
Our team would like to simplify and automate user creation and deletion to deal with the increasing volume of requests. Weʼve created scripts to do this and are going to test them. Letʼs check them out!

### Running Scripts on the Command Line

Sometimes you have a task you need to knock out that can use some automation. You can do that on the command line in the bash shell. 

#### Monitor File System Sizes
Say you want to monitor file system sizes and refresh every 5 seconds. You could keep hitting the up arrow and hit Enter over and over again, or you can run:
```bash
while true ; do clear ; df -h ; sleep 5 ; done
```
You can break out of that loop with CTRL-C.

#### Add Users
Or maybe you want to add a few users:
```bash
for user in manny moe jack ; do useradd -m $user ; done
```
**Checking:**
```bash
grep -E 'manny|moe|jack' /etc/passwd
```

It might be tedious to type for adding three users, but if you add or remove batches of users on a regular basis, creating a script to do it would be beneficial.

### Simple Scripts to Add and Remove Users

We're going to take the concept of adding users to the next level and look at two scripts to do this.

#### Create Users Script
Let's take a look at the `create_users.sh` script:
```bash
more create_users.sh
```
If we just execute without an input file name:
```bash
./create_users.sh
```
We get an error asking us to specify an input file.

Now, we can create a file with the names of the users we want to create, and then execute the script to add the users.
```bash
echo "marcia jan cindy" > users.txt
./create_users.sh users.txt
```
**Examining the exit code:**
```bash
echo $?
```
We can see the users were added as the exit code is 20.
**Checking our work:**
```bash
grep -E 'manny|moe|jack|marcia|jan|cindy' /etc/passwd
```

#### Delete Users Script
Let's take a look at the `delete_users.sh` script:
```bash
more delete_users.sh
```
We're going to add the users we created from our command line script to the `users.txt` file:
```bash
echo "manny moe jack" >> users.txt
```
Now, let's run the script to remove all the users we added:
```bash
./delete_users.sh users.txt
```
**Checking our work:**
```bash
grep -E 'manny|moe|jack|marcia|jan|cindy' /etc/passwd
```

Let's try to create all the users now:
```bash
./create_users.sh users.txt
```
**Examining the exit code:**
```bash
echo $?
```
**Checking our work:**
```bash
grep -E 'manny|moe|jack|marcia|jan|cindy' /etc/passwd
```

## Creating Simple Shell Scripts

# Lesson Guide: Creating Simple Shell Scripts - An Example

Shell scripts are a vital asset in a system administrator's toolkit. They can be used for tasks like automation, reporting, or just ad-hoc assignments involving repetitive tasks. This lesson introduces basic shell scripting concepts.

## Resources:
- [The System Administrator's Guide to Bash Scripting](#)
- [Using Bash for automation | Enable Sysadmin](#)

## Instructions:

### Our Web Team Wants More Power!

The Web Team needs permission to run specific `yum` commands for regular security checks and admin functions. We've drafted a script, `web_admin.sh`, for this purpose.

#### **Analyzing the `web_admin.sh` Script**
To view the script content:
```bash
more web_admin.sh
```
Testing the script:
```bash
sudo ./web_admin.sh
```
To check for updates:
```bash
sudo ./web_admin.sh check-updates
```
Examining the result:
```bash
echo $?
```
Reviewing installed packages (e.g., bash):
```bash
sudo ./web_admin.sh check-installed bash
```
Inspecting the result:
```bash
echo $?
```
Observing available packages (e.g., nginx):
```bash
sudo ./web_admin.sh check-available nginx
```
Checking the result:
```bash
echo $?
```
Checking with a non-existent package (foo-foo):
```bash
sudo ./web_admin.sh check-available foo-foo
```
Evaluating the outcome:
```bash
echo $?
```

<details>
<summary>Provisioing the Basic Infrastructure by Creating a CloudFormation Stack</summary>
{% highlight bash %}
#!/bin/bash

# Script to allow our web_admin users to perform some 'yum' checks
# Usage: ./web_admin.sh <action> <package>
# check-updates: No package to specify
# check-installed: Please specify a package name
# check-available: Please specify a package name

# Define our variables
# Script inputs
ARG1=$1
ARG2=$2

# Other Variables
YUM=/usr/bin/yum

# Let's go!

if [ "$ARG1" = "check-updates" ] ; then
	$YUM check-update >> web_admin.log
	YUM_RESULT=$?
		case $YUM_RESULT in
			100)
				echo "Updates available!"
				exit 111
				;;
			0)
				echo "No updates available!"
				exit 112
				;;
			1)
				echo "Error!"
				exit 113
				;;
		esac
					

elif [ "$ARG1" = "check-installed" ] ; then
	$YUM list --installed $ARG2 >> web_admin.log 2>&1
	YUM_RESULT=$?
		case $YUM_RESULT in
			0)
				echo "Package is installed!"
				exit 114
				;;
			1)
				echo "Package is NOT installed or not available!"
				exit 115
				;;
		esac

elif [ "$ARG1" = "check-available" ] ; then
	$YUM list --available $ARG2 >> web_admin.log 2>&1
	YUM_RESULT=$?
		case $YUM_RESULT in
			0)
				echo "Package is available!"
				exit 116
				;;
			1)
				echo "Package is NOT available or does not exist!"
				exit 117
				;;
		esac

else
	echo "INVALID OPTIONS.  Please specify one of the following:"
	echo "check-updates: No package to specify"
	echo "check-installed: Please specify a package name"
	echo "check-available: Please specify a package name"
	exit 118
fi

{% endhighlight %}
</details>