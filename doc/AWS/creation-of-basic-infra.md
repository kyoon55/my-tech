---
layout: default
title: CloudFormation Stack Deployment Examples Part 1
excerpt: List of CloudFormation Stack Deployments for AWS resources
parent: AWS
nav_order: 2
date: 2023-05-03
---
<h1>{{ page.title }}</h1>
<h2>{{ page.excerpt }}</h2>
<h3>Date Posted: {{ page.date | date: "%Y %m %d" }}</h3>

## Table of Contents

- [Basic Infrastructure CloudFormation Template with EC2 Instance](#basic-infrastructure-cloudformation-template-with-ec2-instance)

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

## Test

### To test it out, ensure to ssh into the public IP address

```bash
ssh -i "Dev.pem" ec2-user@public-IP-Address
```

# Basic Infrastructure that provisions a Nginx Server

<details>
<summary>Provisioing the Basic Infrastructure by Creating a CloudFormation Stack</summary>

{% highlight yaml %}

# Stack-4.yml
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
        - IpProtocol: tcp
          FromPort: 80
          ToPort: 80
          CidrIp: 0.0.0.0/0

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
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash
          yum update -y
          yum install -y nginx

          echo "<html><body><h1>Welcome to My Website</h1></body></html>" >> /var/www/html/index.html
          echo "<html><body><h1>This is my Second Page</h1></body></html>" >> /var/www/html/second_page.html
          service nginx start

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

- Test:
  - Wait for a few minutes, go to the browser, enter public IP address 

# Basic Infrastructure CloudFormation Template with Public and Private Subnet with Public Bastion and Private EC2 Instance

<details>
<summary>This CloudFormation Stack provisions basic infrastructure, with public and private subnets, along with public and private EC2 instances</summary>

{% highlight yaml %}
AWSTemplateFormatVersion: 2010-09-09
Description: Infrastructure CloudFormation Template with Bastion (Public), Private Subnets, NAT Gateway, and EC2 Instance

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

  MyPublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: "10.0.0.0/24"
      AvailabilityZone: "us-east-1a"

  MyPrivateSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: "10.0.1.0/24"
      AvailabilityZone: "us-east-1b"

  MyPrivateSubnet2:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: "10.0.2.0/24"
      AvailabilityZone: "us-east-1c"

  MyInternetGateway:
    Type: AWS::EC2::InternetGateway

  MyVPCGatewayAttachment:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MyVPC
      InternetGatewayId: !Ref MyInternetGateway

  MyPublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC

  MyPublicRoute:
    Type: AWS::EC2::Route
    DependsOn: MyVPCGatewayAttachment
    Properties:
      RouteTableId: !Ref MyPublicRouteTable
      DestinationCidrBlock: "0.0.0.0/0"
      GatewayId: !Ref MyInternetGateway

  MyPublicSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref MyPublicSubnet
      RouteTableId: !Ref MyPublicRouteTable

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

  MyBastionInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      KeyName: !Ref KeyName
      NetworkInterfaces:
        - GroupSet:
            - !Ref MySecurityGroup
          AssociatePublicIpAddress: true
          DeviceIndex: 0
          DeleteOnTermination: true
          SubnetId: !Ref MyPublicSubnet
      ImageId: "ami-97785bed"
      Tags:
        - Key: Name
          Value: "bastion-instance"

  MyPrivateRouteTable1:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC

  MyPrivateRoute1:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref MyPrivateRouteTable1
      DestinationCidrBlock: "0.0.0.0/0"
      NatGatewayId: !Ref MyNatGateway1

  MyPrivateSubnetRouteTableAssociation1:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref MyPrivateSubnet1
      RouteTableId: !Ref MyPrivateRouteTable1

  MyPrivateRouteTable2:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC

  MyPrivateRoute2:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref MyPrivateRouteTable2
      DestinationCidrBlock: "0.0.0.0/0"
      NatGatewayId: !Ref MyNatGateway2

  MyPrivateSubnetRouteTableAssociation2:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref MyPrivateSubnet2
      RouteTableId: !Ref MyPrivateRouteTable2

  MyNatGateway1:
    Type: AWS::EC2::NatGateway
    Properties:
      AllocationId: !GetAtt MyEIP1.AllocationId
      SubnetId: !Ref MyPublicSubnet

  MyNatGateway2:
    Type: AWS::EC2::NatGateway
    Properties:
      AllocationId: !GetAtt MyEIP2.AllocationId
      SubnetId: !Ref MyPublicSubnet

  MyEIP1:
    Type: AWS::EC2::EIP

  MyEIP2:
    Type: AWS::EC2::EIP

  MyPrivateSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: "Private Security Group"
      VpcId: !Ref MyVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: "0.0.0.0/0"

  MyPrivateEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      KeyName: !Ref KeyName
      NetworkInterfaces:
        - GroupSet:
            - !Ref MyPrivateSecurityGroup
          DeviceIndex: 0
          DeleteOnTermination: true
          SubnetId: !Ref MyPrivateSubnet1
      ImageId: "ami-97785bed"
      Tags:
        - Key: Name
          Value: "private-instance"

Outputs:
  Instance1PublicIP:
    Description: Public IP Address of bastion-instance (instance-1)
    Value: !GetAtt MyBastionInstance.PublicIp

  PrivateInstance1PrivateIP:
    Description: Private IP Address of private-instance-1
    Value: !GetAtt MyPrivateEC2Instance.PrivateIp
{% endhighlight %}

</details>

## Parameters
- `KeyName`: This parameter allows you to specify the name of an existing EC2 KeyPair to enable SSH access to the instances. The default value is set to `Dev`.

## Resources
### VPC (`MyVPC`)
- Creates a Virtual Private Cloud (VPC) with the CIDR block `10.0.0.0/16`.

### Public Subnet (`MyPublicSubnet`)
- Creates a public subnet within the VPC with the CIDR block `10.0.0.0/24`. This subnet is associated with the availability zone `us-east-1a`.

### Private Subnet 1 (`MyPrivateSubnet1`)
- Creates the first private subnet within the VPC with the CIDR block `10.0.1.0/24`. This subnet is associated with the availability zone `us-east-1b`.

### Private Subnet 2 (`MyPrivateSubnet2`)
- Creates the second private subnet within the VPC with the CIDR block `10.0.2.0/24`. This subnet is associated with the availability zone `us-east-1c`.

### Internet Gateway (`MyInternetGateway`)
- Creates an Internet Gateway that enables internet connectivity for instances within the VPC.

### VPC Gateway Attachment (`MyVPCGatewayAttachment`)
- Attaches the Internet Gateway created above to the VPC.

### Public Route Table (`MyPublicRouteTable`)
- Creates a public route table associated with the VPC.

### Public Route (`MyPublicRoute`)
- Adds a default route to the public route table, directing all traffic (`0.0.0.0/0`) to the Internet Gateway.

### Public Subnet Route Table Association (`MyPublicSubnetRouteTableAssociation`)
- Associates the public subnet with the public route table, enabling internet access for instances in the public subnet.

### Security Group (`MySecurityGroup`)
- Creates a security group allowing SSH (TCP port 22) traffic from any IP (`0.0.0.0/0`). It is associated with the VPC and used for the bastion (public) instance.

### Bastion (Public) EC2 Instance (`MyBastionInstance`)
- Creates a bastion (public) EC2 instance of type `t2.micro`, associated with the specified KeyPair, placed in the public subnet, and allocated a public IP address. The instance uses the specified Amazon Machine Image (AMI) ID and is tagged with the name "bastion-instance".

### Private Route Table 1 (`MyPrivateRouteTable1`)
- Creates the first private route table associated with the VPC.

### Private Route 1 (`MyPrivateRoute1`)
- Adds a default route to the first private route table, directing all traffic (`0.0.0.0/0`) to the NAT Gateway for the first private subnet.

### Private Subnet 1 Route Table Association (`MyPrivateSubnetRouteTableAssociation1`)
- Associates the first private subnet with the first private route table.

### Private Route Table 2 (`MyPrivateRouteTable2`)
- Creates the second private route table associated with the VPC.

### Private Route 2 (`MyPrivateRoute2`)
- Adds a default route to the second private route table, directing all traffic (`0.0.0.0/0`) to the NAT Gateway for the second private subnet.

### Private Subnet 2 Route Table Association (`MyPrivateSubnetRouteTableAssociation2`)
- Associates the second private subnet with the second private route table.

### NAT Gateway 1 (`MyNatGateway1`)
- Creates the first NAT Gateway associated with the public subnet. It allows instances in the first private subnet to access the internet.

### NAT Gateway 2 (`MyNatGateway2`)
- Creates the second NAT Gateway associated with the public subnet. It allows instances in the second private subnet to access the internet.

### Elastic IP 1 (`MyEIP1`)
- Allocates an Elastic IP address for the first NAT Gateway.

### Elastic IP 2 (`MyEIP2`)
- Allocates an Elastic IP address for the second NAT Gateway.

### Private Security Group (`MyPrivateSecurityGroup`)
- Creates a security group allowing SSH (TCP port 22) traffic from any IP (`0.0.0.0/0`). It is associated with the VPC and used for the private EC2 instance.

### Private EC2 Instance (`MyPrivateEC2Instance`)
- Creates a private EC2 instance of type `t2.micro`, associated with the specified KeyPair, placed in the first private subnet, and allocated a private IP address. The instance uses the specified Amazon Machine Image (AMI) ID and is tagged with the name "private-instance".

## Outputs
- `Instance1PublicIP`: This output displays the public IP address of the bastion (public) instance (`MyBastionInstance`). You can use this IP address to SSH into the bastion instance.
- `PrivateInstance1PrivateIP`: This output displays the private IP address of the private EC2 instance (`MyPrivateEC2Instance`). You can use this IP address to access the private EC2 instance within the private subnets.

## Test

### To test it out, ensure to ssh into the public IP address

```bash
## SSH into the public EC2 instance
ssh -i "Dev.pem" ec2-user@public-IP-address

## Get the copy of Dev.pem
cat << EOF> Dev.pem
-----BEGIN RSA PRIVATE KEY-----
dscjdnsjkcdsncjks 
-------END RSA PRIVATE KEY-------
EOF

chmod 400 Dev.pem

ssh -i "Dev.pem" ec2-user@private-IP-address
```

# Create 2 More Private EC2 Instances

<details>
<summary>This CloudFormation Stack provisions basic infrastructure, with public and private subnets, along with public and private EC2 instances, Plus two more private EC2 instancesqqus</summary>

{% highlight yaml %}
AWSTemplateFormatVersion: 2010-09-09
Description: Infrastructure CloudFormation Template with Bastion (Public), Private Subnets, NAT Gateway, and EC2 Instance

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

  MyPublicSubnet:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: "10.0.0.0/24"
      AvailabilityZone: "us-east-1a"

  MyPrivateSubnet1:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: "10.0.1.0/24"
      AvailabilityZone: "us-east-1b"

  MyPrivateSubnet2:
    Type: AWS::EC2::Subnet
    Properties:
      VpcId: !Ref MyVPC
      CidrBlock: "10.0.2.0/24"
      AvailabilityZone: "us-east-1c"

  MyInternetGateway:
    Type: AWS::EC2::InternetGateway

  MyVPCGatewayAttachment:
    Type: AWS::EC2::VPCGatewayAttachment
    Properties:
      VpcId: !Ref MyVPC
      InternetGatewayId: !Ref MyInternetGateway

  MyPublicRouteTable:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC

  MyPublicRoute:
    Type: AWS::EC2::Route
    DependsOn: MyVPCGatewayAttachment
    Properties:
      RouteTableId: !Ref MyPublicRouteTable
      DestinationCidrBlock: "0.0.0.0/0"
      GatewayId: !Ref MyInternetGateway

  MyPublicSubnetRouteTableAssociation:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref MyPublicSubnet
      RouteTableId: !Ref MyPublicRouteTable

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

  MyBastionInstance:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      KeyName: !Ref KeyName
      NetworkInterfaces:
        - GroupSet:
            - !Ref MySecurityGroup
          AssociatePublicIpAddress: true
          DeviceIndex: 0
          DeleteOnTermination: true
          SubnetId: !Ref MyPublicSubnet
      ImageId: "ami-97785bed"
      Tags:
        - Key: Name
          Value: "bastion-instance"

  MyPrivateRouteTable1:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC

  MyPrivateRoute1:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref MyPrivateRouteTable1
      DestinationCidrBlock: "0.0.0.0/0"
      NatGatewayId: !Ref MyNatGateway1

  MyPrivateSubnetRouteTableAssociation1:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref MyPrivateSubnet1
      RouteTableId: !Ref MyPrivateRouteTable1

  MyPrivateRouteTable2:
    Type: AWS::EC2::RouteTable
    Properties:
      VpcId: !Ref MyVPC

  MyPrivateRoute2:
    Type: AWS::EC2::Route
    Properties:
      RouteTableId: !Ref MyPrivateRouteTable2
      DestinationCidrBlock: "0.0.0.0/0"
      NatGatewayId: !Ref MyNatGateway2

  MyPrivateSubnetRouteTableAssociation2:
    Type: AWS::EC2::SubnetRouteTableAssociation
    Properties:
      SubnetId: !Ref MyPrivateSubnet2
      RouteTableId: !Ref MyPrivateRouteTable2

  MyNatGateway1:
    Type: AWS::EC2::NatGateway
    Properties:
      AllocationId: !GetAtt MyEIP1.AllocationId
      SubnetId: !Ref MyPublicSubnet

  MyNatGateway2:
    Type: AWS::EC2::NatGateway
    Properties:
      AllocationId: !GetAtt MyEIP2.AllocationId
      SubnetId: !Ref MyPublicSubnet

  MyEIP1:
    Type: AWS::EC2::EIP

  MyEIP2:
    Type: AWS::EC2::EIP

  MyPrivateSecurityGroup:
    Type: AWS::EC2::SecurityGroup
    Properties:
      GroupDescription: "Private Security Group"
      VpcId: !Ref MyVPC
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: 22
          ToPort: 22
          CidrIp: "0.0.0.0/0"

  MyPrivateEC2Instance1:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      KeyName: !Ref KeyName
      NetworkInterfaces:
        - GroupSet:
            - !Ref MyPrivateSecurityGroup
          DeviceIndex: 0
          DeleteOnTermination: true
          SubnetId: !Ref MyPrivateSubnet1
      ImageId: "ami-97785bed"
      Tags:
        - Key: Name
          Value: "private-instance-1"

  MyPrivateEC2Instance2:
    Type: AWS::EC2::Instance
    Properties:
      InstanceType: t2.micro
      KeyName: !Ref KeyName
      NetworkInterfaces:
        - GroupSet:
            - !Ref MyPrivateSecurityGroup
          DeviceIndex: 0
          DeleteOnTermination: true
          SubnetId: !Ref MyPrivateSubnet2
      ImageId: "ami-97785bed"
      Tags:
        - Key: Name
          Value: "private-instance-2"
Outputs:
  Instance1PublicIP:
    Description: Public IP Address of bastion-instance (instance-1)
    Value: !GetAtt MyBastionInstance.PublicIp

  PrivateInstance1PrivateIP:
    Description: Private IP Address of private-instance-1
    Value: !GetAtt MyPrivateEC2Instance1.PrivateIp

  PrivateInstance2PrivateIP:
    Description: Private IP Address of private-instance-2
    Value: !GetAtt MyPrivateEC2Instance2.PrivateIp
{% endhighlight %}
</details>

## Building Smailler Images with Multi-stage builds

### Containers are made of layers. Compile and install operations performed in the image, add to the layers, increasing the size of the container.Instead of keeping all of those layers into the final image, you can split those steps off, and only use the finished product. Docker provides this capability through multi-stage builds

#### Key Points
- docker inspect "-f" parameter gives format (parses) the value from the inspect of the docker containers
- "numfmt --to=iec" converts bytes to gigabytes
- From Dockerfile, certain pieces of lines of scripts are pulled to the side (layer) and added into the image, producing fewer layers (outputs) in the images 
- Beginning of the Dockerfile: FROM python:3 AS BASE
- "FROM base AS builder"
- Then the new layer of the image begins

<details>
<summary>Script Example</summary>

{% highlight bash %}
{% raw %}

# Do Prep Work in the Image

## Change to the notes directory:
cd notes

## Check the Dockerfile:
cat Dockerfile

## Build an image using the file:
docker build -t notesapp:default .

## Set a variable to view the layers of the image:
## Formats the output of a docker inspect to give the hashes of the layers used for the image.
export showLayers='{{ range .RootFS.Layers }}{{ println . }}{{end}}'

## Set a variable to show the size of the image:
export showSize='{{ .Size }}'

## Show the image layers:
docker inspect -f "$showLayers" notesapp:default

## Count the number of layers:
docker inspect -f "$showLayers" notesapp:default | wc -l

## Show the size of the image:
docker inspect -f "$showSize" notesapp:default | numfmt --to=iec

# Add a Build Stage

## Open the Dockerfile:
vim Dockerfile
Add a build stage by adding the following:
FROM python:3 AS base
ENV PYBASE /pybase
ENV PYTHONUSERBASE $PYBASE
ENV PATH $PYBASE/bin:$PATH

FROM base AS builder
RUN pip install pipenv
WORKDIR /tmp
COPY Pipfile .
RUN pipenv lock
RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile

FROM base
COPY --from=builder /pybase /pybase
COPY . /app/notes
WORKDIR /app/notes
EXPOSE 80
CMD [ "flask", "run", "--port=80", "--host=0.0.0.0" ]

## Save the file:
ESC
:wq

# Create a Smaller Image

## Build the image:
docker build -t notesapp:multistage .

## Show the multistage image layers:
docker inspect -f "$showLayers" notesapp:multistage

## Count the layers:
docker inspect -f "$showLayers" notesapp:multistage | wc -l

## Show the size of the image:
docker inspect -f "$showSize" notesapp:multistage | numfmt --to=iec

{% endraw %}
{% endhighlight %}
</details>
<hr style="3px solid #bbb">

## Container Logging

### Congifure syslog to use UDP and then configure Docker to use the syslog logging driver by default

<details>
<summary>Script Example</summary>

{% highlight bash %}
{% raw %}
#  Configure Docker to Use Syslog
## Open the rsyslog.conf file.
vim /etc/rsyslog.conf

## In the file editor, uncomment the two lines under Provides UDP syslog reception by removing #.

$ModLoad imudp
$UDPServerRun 514

## Then, start the syslog service.

systemctl start rsyslog

## Now that syslog is running, let's configure Docker to use syslog as the default logging driver. We'll do this by creating a file called daemon.json.

mkdir /etc/docker
vi /etc/docker/daemon.json

## In the vi editor, enter the following, making sure to replace <PRIVATE_IP> with the private IP of your cloud server:

{
  "log-driver": "syslog",
  "log-opts": {
    "syslog-address": "udp://<PRIVATE_IP>:514"
  }
}

## Next, save and quit. Then, start the Docker service.

systemctl start docker

## The next step is to see if there are any logs coming in from Docker.

tail /var/log/messages

## The output of this command tells us that there are. Now let's test our setup.

## We're going to create two new containers using the httpd image. The first one will be called syslog-logging and will use none for the log driver. The second will be called json-logging and will use the JSON file for the log driver.

## Create a container.

docker container run -d --name syslog-logging --log-driver none httpd

## Then run docker ps to make sure our container is up and running correctly. Then, execute the docker logs command to see what logs we have.

docker logs syslog-logging

## To confirm that we are logging to syslog, we can check the content of /var/log/messages. Verify that the syslog-logging container is sending its logs to syslog by running tail on the message log file.

tail /var/log/messages

## The output shows us the logs that are being input to syslog.Create our second test container. This time, we'll specify the log driver as the JSON file.

docker container run -d --name json-logging --log-driver json-file httpd

## Run the docker ps command again. This time, we should see two containers running. Verify that the json-logging container is sending its logs to the JSON file. Execute the docker logs command on the json-logging container.

docker logs json-logging
This time, the logs do not appear in /var/log/messages because they are being sent to a JSON file instead.

{% endraw %}
{% endhighlight %}
</details>
<hr style="3px solid #bbb">