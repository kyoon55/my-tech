---
title: a tutorial about creating an AWS basic infrastructure, with VPC, publicc, and private subnets, IGW, NAT, security groups, EC2 and RDS that runs postgresql
layout: default
excerpt: this page describes about a tutorial about creating an AWS basic infrastructure, with VPC, publicc, and private subnets, IGW, NAT, security groups, EC2 and RDS that runs postgresql
parent: Misc
time: 2023-04-16
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.time | date: "%Y %m %d" }}</h3>
# Tutorial: Creating a Basic Infrastructure on AWS

In this tutorial, we will walk you through the process of setting up a basic infrastructure on AWS. This infrastructure will consist of a Virtual Private Cloud (VPC) with public and private subnets, an Internet Gateway (IGW), Network Address Translation (NAT) gateway, security groups, and instances of Amazon Elastic Compute Cloud (EC2) and Amazon RDS running PostgreSQL.

By the end of this tutorial, you will have a better understanding of how to configure these essential components on AWS. Let's get started!

## Table of Contents
1. Prerequisites
2. Set Up a VPC
3. Create Subnets
4. Configure Routing and Gateways
5. Set Up Security Groups
6. Launch EC2 Instances
7. Provision RDS Instance with PostgreSQL
8. Summary

## 1. Prerequisites
- An AWS account with appropriate permissions
- Basic knowledge of AWS services and concepts

## 2. Set Up a VPC
1. Log in to your AWS Management Console.
2. Go to the AWS VPC service.
3. Click on "Create VPC."
4. Enter a name for your VPC and specify an IPv4 CIDR block.
5. Leave the rest of the settings as default and click on "Create."

## 3. Create Subnets
1. Within your new VPC, go to the "Subnets" section.
2. Click on "Create subnet."
3. Provide a name for your subnet, select your VPC, and specify an IPv4 CIDR block.
4. Repeat these steps to create both a public and private subnet.

## 4. Configure Routing and Gateways
1. Go to the "Internet Gateways" section within VPC.
2. Click on "Create internet gateway."
3. Attach the internet gateway to your VPC.
4. Navigate to the "Route Tables" section and select your VPC's main route table.
5. Edit the route table by adding a route `0.0.0.0/0` to your internet gateway.

## 5. Set Up Security Groups
1. Move to the EC2 service within your chosen region.
2. Go to the "Security Groups" section in the left-hand menu.
3. Create a new security group for your EC2 instances, allowing inbound SSH (port 22) access from your IP.
4. Create another security group for your RDS instance, allowing inbound PostgreSQL (port 5432) access from your EC2 security group.

## 6. Launch EC2 Instances
1. Within the EC2 service, navigate to "Instances" in the left-hand menu.
2. Click on "Launch Instances" and choose an Amazon Machine Image (AMI) of your preference.
3. Select your VPC and public subnet.
4. Configure the EC2 instance as per your needs (instance type, storage, security group, etc.).
5. Launch the instance and repeat these steps to launch a second instance in your private subnet.

## 7. Provision RDS Instance with PostgreSQL
1. Go to the Amazon RDS service.
2. Click on "Create database" and select PostgreSQL as the engine.
3. Specify the details for your RDS instance (instance size, storage, username, etc.).
4. Choose your VPC, private subnet, and security group created for RDS.
5. Complete the remaining steps to provision your RDS instance.

## 8. Summary
Congratulations! You have successfully created a basic infrastructure on AWS. You have set up a VPC with public and private subnets, configured routing and gateways, established security groups, and launched EC2 instances and an RDS instance running PostgreSQL.

Feel free to explore and expand on this basic infrastructure to meet your specific requirements. AWS offers a wide range of services and features to enhance your infrastructure's capabilities. Happy building!

Note: Kindly remove the credits and conclusion section if required.
