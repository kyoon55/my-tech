---
layout: default
title: Create a Basic Infrastructure
excerpt: List of CloudFormation Stack Deployments for AWS resources
parent: AWS
nav_order: 2
date: 2023-05-03
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>



# Basic Infrastructure CloudFormation Template with EC2 Instance

<details>
<summary>Provisioing the Basic Infrastructure by Creating a CloudFormation Stack</summary>


{% highlight yaml %}
AWSTemplateFormatVersion: 2010-09-09
Description: Basic Infrastructure CloudFormation Template with EC2 Instance

Parameters:
  KeyName:
    Description: Name of an existing EC2 KeyPair to enable SSH access to the instance
    Type: AWS::EC2::KeyPair::KeyName
    Default: Dev
    
Resources:
  MyVPC:
    Type: AWS::EC2::VPC
    Properties:
      CidrBlock: "10.0.0.0/16"
      EnableDnsSupport: true
      EnableDnsHostnames: true

  MySubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: "10.0.0.0/24"
      AvailabilityZone: "us-east-1a"

  MyInternetGateway:
    Type: AWS::EC2::InternetGateway

  MyVPCGatewayAttachment:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MyVPC
      InternetGatewayId: !Ref MyInternetGateway

  MyRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC

  MyRoute:
    Type: AWS::EC2::Route
    DependsOn: MyVPCGatewayAttachment
    Properties:
      RouteTableId: !Ref MyRouteTable
      DestinationCidrBlock: "0.0.0.0/0"
      GatewayId: !Ref MyInternetGateway

  MySubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref MySubnet
      RouteTableId: !Ref MyRouteTable

  MySecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: "My Security Group"
      VpcId: !Ref MyVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: "0.0.0.0/0"


  MyInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      NetworkInterfaces:
        - GroupSet:
            - !Ref MySecurityGroup
          AssociatePublicIpAddress: true
          DeviceIndex: 0
          DeleteOnTermination: true
          SubnetId: !Ref MySubnet
      KeyName: !Ref KeyName
      ImageId: "ami-97785bed" 
      Tags:
        - Key: Name
          Value: "instance-1"
 
  MyEIP:
    Type: AWS::EC2::EIP

  MyInstanceEIPAssociation:
    Type: AWS::EC2::EIPAssociation
    Properties:
      InstanceId: !Ref MyInstance
      EIP: !Ref MyEIP

Outputs:
  Instance1PublicIP:
    Description: Public IP Address of instance-1
    Value: !GetAtt MyInstance.PublicIp
{% endhighlight %}

</details>

## Parameters
- `KeyName`: This parameter allows you to specify the name of an existing EC2 KeyPair to enable SSH access to the instance. The default value is set to `Dev`.

## Resources
### VPC (`MyVPC`)
- Creates a Virtual Private Cloud (VPC) with the CIDR block `10.0.0.0/16`.

### Subnet (`MySubnet`)
- Creates a subnet within the VPC with the CIDR block `10.0.0.0/24`. The subnet is associated with the availability zone `us-east-1a`.

### Internet Gateway (`MyInternetGateway`)
- Creates an Internet Gateway that enables internet connectivity for instances within the VPC.

### VPC Gateway Attachment (`MyVPCGatewayAttachment`)
- Attaches the Internet Gateway created above to the VPC.

### Route Table (`MyRouteTable`)
- Creates a route table associated with the VPC.

### Route (`MyRoute`)
- Adds a default route to the route table, directing all traffic (`0.0.0.0/0`) to the Internet Gateway.

### Subnet Route Table Association (`MySubnetRouteTableAssociation`)
- Associates the subnet with the route table, enabling internet access for instances in the subnet.

### Security Group (`MySecurityGroup`)
- Creates a security group allowing SSH (TCP port 22) traffic from any IP (`0.0.0.0/0`). It is associated with the VPC.

### EC2 Instance (`MyInstance`)
- Creates an EC2 instance of type `t2.micro`, associated with the specified KeyPair and placed in the subnet created earlier. The instance uses the specified Amazon Machine Image (AMI) ID. It is also tagged with the name "instance-1".

### Elastic IP (`MyEIP`)
- Allocates an Elastic IP address to associate with the EC2 instance.

### EIP Association (`MyInstanceEIPAssociation`)
- Associates the Elastic IP with the EC2 instance, making it accessible with a static public IP address.

## Outputs
- `Instance1PublicIP`: This output displays the public IP address of the "instance-1" EC2 instance, allowing you to easily identify and connect to the instance via SSH.
