---
title: a tutorial about creating an AWS basic infrastructure, with VPC, public, and private subnets, IGW, NAT, security groups, EC2 and RDS that runs postgresql
layout: default
excerpt: this page describes about a tutorial about creating an AWS basic infrastructure, with VPC, public, and private subnets, IGW, NAT, security groups, EC2 and RDS that runs postgresql
parent: Misc
date: 2023-04-16
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>
# Tutorial: Creating a Basic Infrastructure on AWS

In this tutorial, we will walk you through the process of setting up a basic infrastructure on AWS. This infrastructure will consist of a Virtual Private Cloud (VPC) with public and private subnets, an Internet Gateway (IGW), Network Address Translation (NAT) gateway, security groups, and instances of Amazon Elastic Compute Cloud (EC2) and Amazon RDS running PostgreSQL.

By the end of this tutorial, you will have a better understanding of how to configure these essential components on AWS. Let's get started!

## Table of Contents
# Prerequisites
# Set Up a VPC
# Create Subnets
# Configure Routing and Gateways
# Set Up Security Groups
# Launch EC2 Instances
# Provision RDS Instance with PostgreSQL
# Summary

## # Prerequisites
- An AWS account with appropriate permissions
- Basic knowledge of AWS services and concepts

## # Set Up a VPC
# Log in to your AWS Management Console.
# Go to the AWS VPC service.
# Click on "Create VPC."
# Enter a name for your VPC and specify an IPv4 CIDR block.
# Leave the rest of the settings as default and click on "Create."

## # Create Subnets
# Within your new VPC, go to the "Subnets" section.
# Click on "Create subnet."
# Provide a name for your subnet, select your VPC, and specify an IPv4 CIDR block.
# Repeat these steps to create both a public and private subnet.

## # Configure Routing and Gateways
# Go to the "Internet Gateways" section within VPC.
# Click on "Create internet gateway."
# Attach the internet gateway to your VPC.
# Navigate to the "Route Tables" section and select your VPC's main route table.
# Edit the route table by adding a route `0.0.0.0/0` to your internet gateway.

## # Set Up Security Groups
# Move to the EC2 service within your chosen region.
# Go to the "Security Groups" section in the left-hand menu.
# Create a new security group for your EC2 instances, allowing inbound SSH (port 22) access from your IP.
# Create another security group for your RDS instance, allowing inbound PostgreSQL (port 5432) access from your EC2 security group.

## # Launch EC2 Instances
# Within the EC2 service, navigate to "Instances" in the left-hand menu.
# Click on "Launch Instances" and choose an Amazon Machine Image (AMI) of your preference.
# Select your VPC and public subnet.
# Configure the EC2 instance as per your needs (instance type, storage, security group, etc.).
# Launch the instance and repeat these steps to launch a second instance in your private subnet.

## # Provision RDS Instance with PostgreSQL
# Go to the Amazon RDS service.
# Click on "Create database" and select PostgreSQL as the engine.
# Specify the details for your RDS instance (instance size, storage, username, etc.).
# Choose your VPC, private subnet, and security group created for RDS.
# Complete the remaining steps to provision your RDS instance.

## # Summary
Congratulations! You have successfully created a basic infrastructure on AWS. You have set up a VPC with public and private subnets, configured routing and gateways, established security groups, and launched EC2 instances and an RDS instance running PostgreSQL.