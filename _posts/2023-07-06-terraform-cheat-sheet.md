---
title:  "Test post from last year"
categories: jekyll update
permalink: test-post-from-last-year.html
tags: [news]
---
## Plan, deploy, and cleanup infrastructure

1. Apply changes without being prompted to enter 'yes'
   ``` terraform apply --auto-approve ```
2. Destroy/cleanup deployment without being prompted 'yes'
    ``` terraform destroy --auto-approve ```
3. Output the deployment plan to plan.out
   ``` terraform plan -out plan.out ```
4. Use the plan.out plan file to deploy infrastructure
   ``` terraform plan -destroy ```
5. Outputs a destroy plan
   ``` terraform apply -target=aws_instance.my_ec2 ```
6. Only apply/deploy changes to the targeted resource
   ``` terraform apply -var my_region_variable=us-east-1 ```
7. Pass a variable via command-line while applying a configuration
   ``` terraform apply -var my_region_variable=us-east-1 ```
8. Lock the state file so it can't be modified by any other Terraform apply or modification action (Possible only where backend allows locking)
   ``` terraform apply -lock=true ```
9. Do not reconsolie state file with real-world resources (helpful with large complex deployments for saving deployment time)
    ``` terraform apply refresh=false ```
10. Number of simultaneous resource operations
    ``` terraform apply --parallelism=5 ```
11. Reconsile the state in Terraform state file with real-world resources
    ``` terraform refresh ```
12. Get information about providers used in current configuration
    ``` terraform providers ```

## Terraform Workspaces

1. Create a new workspace
2. Change to the selected workspace
3. List out all workspaces

## Terraform state manipulation

1. Show details stored in Terraform state for the resource
2. Download and output terraform state to a file
3. Move a resource tracked via state to different module
4. Replace existing provider with another
5. List all the resources tracked in the current state file
6. Unmanage a resource, delete it from Terraform state file

## Terraform import and Outputs

1. Import EC2 instance with id id-abcd1234 into Terraform resource named "new_ec2_instance" of type "aws_instnace"
2. Same as above, imports a real-world resource into an instance of Terraform resource
3. List all Outputs as state in code
4. List a specific declared output
5. List all outputs in JSON format

## Terraform CLI tricks

1. Setup tab auto-completion, requires logging back in

## Format and validate Terraform code

1. Format code per HCL canoical standard
2. Validate code for syntax
3. Validate code skip backend validation

## Initialize your Tewrraform working directory

1. Initialize directory, pull down providers
2. Initialize directory, do not download plugins
3. Initialize direwctory, do not verify plugins for Hashicorp signature

## Terraform misc. commands

1. Display Terraform binary version, also warns if version is old
2. Download and update modules in the "root" module

## Terraform Console (Test out Terraform interpolations)

1. Echo an expression into terraform console and see its expected result as output
2. Terraform console also has an interactive CLI just enter "terraform console"
3. Display the pulic IP against the "my_ec2" Terraform resource as seen in the Terraform state file

## Terraform Graph (Dependency graphing)

1. Produce a PNG diagram showing relationship and deplendencies between Terraform resources in your configuration and code

## Terraform Taint/Untaint

1. Taint resource to be recreated on next apply
2. Remove taint from a resource
3. Force-unlock a locked state file, LOCK_ID provided when locking the State file beforehead

## Terraform Cloud

1. Obtain and save API token for Terraform cloud
2. Log out of Terraform Cloud, defaults to hostname app.terraform.io

{% include links.html %}
