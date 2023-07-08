---
title:  "Test post from last year"
categories: jekyll update
permalink: test-post-from-last-year.html
tags: [news]
---
## Plan, deploy, and cleanup infrastructure

1. Apply changes without being prompted to enter 'yes'
   - ``` terraform apply --auto-approve ```
2. Destroy/cleanup deployment without being prompted 'yes'
   - ``` terraform destroy --auto-approve ```
3. Output the deployment plan to plan.out
   - ``` terraform plan -out plan.out ```
4. Use the plan.out plan file to deploy infrastructure
   - ``` terraform plan -destroy ```
5. Outputs a destroy plan
   - ``` terraform apply -target=aws_instance.my_ec2 ```
6. Only apply/deploy changes to the targeted resource
   - ``` terraform apply -var my_region_variable=us-east-1 ```
7. Pass a variable via command-line while applying a configuration
   - ``` terraform apply -var my_region_variable=us-east-1 ```
8. Lock the state file so it can't be modified by any other Terraform apply or modification action (Possible only where backend allows locking)
   - ``` terraform apply -lock=true ```
9. Do not reconsolie state file with real-world resources (helpful with large complex deployments for saving deployment time)
    - ``` terraform apply refresh=false ```
10. Number of simultaneous resource operations
    - ``` terraform apply --parallelism=5 ```
11. Reconsile the state in Terraform state file with real-world resources
    - ``` terraform refresh ```
12. Get information about providers used in current configuration
    - ``` terraform providers ```

## Terraform Workspaces

1. Create a new workspace
   - ``` terraform workspace new mynewworkspace ```
2. Change to the selected workspace
   - ``` terraform workspace select default ```
3. List out all workspaces
   - ``` terraform workspace list ```

## Terraform state manipulation

1. Show details stored in Terraform state for the resource
   - ```terraform state show aws_instance.my_ec2```
2. Download and output terraform state to a file
3. - ```terraform state pull terraform.tfstate```
4. Move a resource tracked via state to different module
   - `terraform state my aws_iam_role.my_ssm_role module.custom_mopdule`
5. Replace existing provider with another
   - ` terraform state replace-provider hashicorp/aws registry.custom.com/aws `
6. List all the resources tracked in the current state file
   - ` terraform state list `
7. Unmanage a resource, delete it from Terraform state file
   - ` terraform state rm aws_instance.myinstance `

## Terraform import and Outputs

1. Import EC2 instance with id id-abcd1234 into Terraform resource named "new_ec2_instance" of type "aws_instnace"
   - ` terraform import aws_instance.new_ec2>instance i-abcd1234 `
2. Same as above, imports a real-world resource into an instance of Terraform resource
   - ` terraform import 'aws_instance.new_ec2_instance[0]' i-abcd1234 `
3. List all Outputs as state in code
   - ` terraform output `
4. List a specific declared output
   - ` terraform output instance_public_ip `
5. List all outputs in JSON format
   - `terrafrom output -json`

## Terraform CLI tricks

1. Setup tab auto-completion, requires logging back in
   - `terraform -install-autocomplete`

## Format and validate Terraform code

1. Format code per HCL canoical standard
   - `terraform fmt`
2. Validate code for syntax
   - `terraform validate`
3. Validate code skip backend validation
   - `terraform validate -backend=false`

## Initialize your Tewrraform working directory

1. Initialize directory, pull down providers
   - `terraform init`
2. Initialize directory, do not download plugins
   - `terraform init -get-plugins=false`
3. Initialize direwctory, do not verify plugins for Hashicorp signature
   - `terraform init -verify-plugins=false`

## Terraform misc. commands

1. Display Terraform binary version, also warns if version is old
   - `terraform version`
2. Download and update modules in the "root" module
   - `terraform get -update=true`

## Terraform Console (Test out Terraform interpolations)

1. Echo an expression into terraform console and see its expected result as output
   - `echo 'join(",",["foo","bar"])' | terraform console`
2. Terraform console also has an interactive CLI just enter "terraform console"
   - `echo '1 + 5' | terraform console`
3. Display the pulic IP against the "my_ec2" Terraform resource as seen in the Terraform state file
   - `echo "aws_instance.my_ec2.public_ip" | terraform console`

## Terraform Graph (Dependency graphing)

1. Produce a PNG diagram showing relationship and deplendencies between Terraform resources in your configuration and code
   - `terraform graph | dot -Tpng > graph.png`

## Terraform Taint/Untaint

1. Taint resource to be recreated on next apply
   `terraform taint aws_instance.my_ec2`
2. Remove taint from a resource
   `terraform untaint aws_instance.my_ec2`
3. Force-unlock a locked state file, LOCK_ID provided when locking the State file beforehead
   - `terraform force-unlock LOCK_ID`

## Terraform Cloud

1. Obtain and save API token for Terraform cloud
   - `terraform login`
2. Log out of Terraform Cloud, defaults to hostname app.terraform.io
   - `terraform logout`

{% include links.html %}
