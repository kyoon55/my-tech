---
layout: default
title: List of CloudFormation Part 2
excerpt: List of Cloudformation templates that generate basic infrastructure and platforms
parent: Misc
nav_order: 1
date: 2023-08-19
---


<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>
# Tutorial: Creating a Load Balancer and Auto Scaling Group to elastically scale nginx instnaces, in Public Subnets

<details>
<summary>Summary</summary>
{% highlight yaml %}
---
    AWSTemplateFormatVersion: '2010-09-09'
    Description: 'CloudFormation template for provisioning NGINX in an Auto Scaling Group with a Load Balancer within a VPC, with Internet Gateway'
    
    Resources:
    
      # VPC
      NginxVPC:
        Type: AWS::EC2::VPC
        Properties: 
          CidrBlock: 10.0.0.0/16
          EnableDnsSupport: true
          EnableDnsHostnames: true
          Tags:
            - Key: Name
              Value: NginxVPC
    
      # Internet Gateway
      NginxIGW:
        Type: 'AWS::EC2::InternetGateway'
    
      # Attach Internet Gateway to VPC
      AttachGateway:
        Type: 'AWS::EC2::VPCGatewayAttachment'
        Properties:
          VpcId: 
            Ref: NginxVPC
          InternetGatewayId: 
            Ref: NginxIGW
    
      # Public Subnet
      PublicSubnet:
        Type: AWS::EC2::Subnet
        Properties:
          VpcId:
            Ref: NginxVPC
          CidrBlock: 10.0.1.0/24
          MapPublicIpOnLaunch: true
          Tags:
            - Key: Name
              Value: PublicSubnet
    
      # Route Table for Public Subnet
      PublicRouteTable:
        Type: AWS::EC2::RouteTable
        Properties:
          VpcId:
            Ref: NginxVPC
    
      # Route to IGW
      PublicRoute:
        Type: AWS::EC2::Route
        DependsOn: AttachGateway
        Properties:
          RouteTableId:
            Ref: PublicRouteTable
          DestinationCidrBlock: 0.0.0.0/0
          GatewayId:
            Ref: NginxIGW
    
      # Associate Route Table with Subnet
      SubnetRouteTableAssociation:
        Type: AWS::EC2::SubnetRouteTableAssociation
        Properties:
          SubnetId:
            Ref: PublicSubnet
          RouteTableId:
            Ref: PublicRouteTable
    
      # Security Group
      NginxSecurityGroup:
        Type: 'AWS::EC2::SecurityGroup'
        Properties:
          GroupDescription: Enable HTTP access via port 80
          VpcId: 
            Ref: NginxVPC
          SecurityGroupIngress:
            - IpProtocol: tcp
              FromPort: '80'
              ToPort: '80'
              CidrIp: 0.0.0.0/0
    
      # Launch Configuration
      NginxLaunchConfiguration:
        Type: AWS::AutoScaling::LaunchConfiguration
        Properties:
          InstanceType: t2.micro
          SecurityGroups:
            - Ref: NginxSecurityGroup
          ImageId: ami-050021685e435466c
          UserData:
            Fn::Base64: |
              #!/bin/bash
              apt update
              apt install -y nginx
              systemctl start nginx
    
      # Auto Scaling Group
      NginxAutoScalingGroup:
        Type: AWS::AutoScaling::AutoScalingGroup
        Properties:
          VPCZoneIdentifier:
            - Ref: PublicSubnet
          LaunchConfigurationName:
            Ref: NginxLaunchConfiguration
          MinSize: '1'
          MaxSize: '3'
          DesiredCapacity: '2'
          LoadBalancerNames:
            - Ref: NginxLoadBalancer
    
      # Load Balancer
      NginxLoadBalancer:
        Type: "AWS::ElasticLoadBalancing::LoadBalancer"
        DependsOn: PublicRoute
        Properties:
          Subnets:
            - Ref: PublicSubnet
          Listeners:
            - LoadBalancerPort: '80'
              InstancePort: '80'
              Protocol: HTTP
          HealthCheck:
            Target: HTTP:80/
            HealthyThreshold: '2'
            UnhealthyThreshold: '5'
            Interval: '30'
            Timeout: '5'
          SecurityGroups:
            - Ref: NginxSecurityGroup
    
    Outputs:
      LoadBalancerDNSName:
        Description: The DNSName of the Load Balancer
        Value:
          Fn::GetAtt:
            - NginxLoadBalancer
            - DNSName
    
{% endhighlight %}