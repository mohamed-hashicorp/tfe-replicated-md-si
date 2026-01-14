# Terraform Enterprise Replicated mounted disk Deployment on AWS

This repository provides an automated way to deploy Terraform Enterprise (TFE) on AWS using:

- Replicated
- Mounted disk
- Terraform

This project automates the entire process, providing a repeatable, consistent, and reliable way to deploy TFE in minutes using Terraform.

## Prerequistes
This guide was executed on MacOS so it assumes the following:

- You have Git installed.
- AWS Credentials are configured.
- Terraform is installed (tested with Terraform 1.5.7)
- TFE Replicated license file

## Clone the repository
- Clone the Github repo
```
git clone https://github.com/mohamed-hashicorp/tfe-replicated-md-si.git
```
- Change the directory
```
cd tfe-replicated-md-si
```

## Configure your variables
- Rename the `terraform.tfvars.example`
```
cp terraform.tfvars.example terraform.tfvars
```
- Set the TFE image tag, license and encrytion password
```
#acme prod
#acme_server_url         = "https://acme-v02.api.letsencrypt.org/directory"

# acme staging
acme_server_url         = "https://acme-staging-v02.api.letsencrypt.org/directory"

dns_record              = "server2.mohamed-abdelbaset.sbx.hashidemos.io"
email                   = "user@domain.tld"
hosted_zone_name        = "mohamed-abdelbaset.sbx.hashidemos.io"
instance_type           = "t2.large"
name                    = "my-vm"
rds_password            = "Mystrongpassword123!"
region                  = "eu-west-1"
tfe_admin_password      = "Mystrongpassword123"
tfe_encryption_password = "Mystrongpassword123"
tfe_release_sequence    = "811"
```

## Upload license
- Copy the license to your working directory
```
cp <Location of license> .
```

## Create Infrastructure
- Run Terraform init
```
terraform init
```
- Run Terraform apply
```
terraform apply
```

## Verify
- Check that TFE installation is accessible from your browser.
- Login to https://server2.mohamed-abdelbaset.sbx.hashidemos.io:8800/ to check if the console UI
- Click on `Open App`
- Create an admin user
- Login with your admin user
- Click on `Create organization`
- Set the organization name and click on `Create organization`
- In the organization page, select `CLI-Driven Workflow`
- Set the Workspace name and click on `Create`
- Open a terminal
- Run the terraform login command `terraform login server5.mohamed-abdelbaset.sbx.hashidemos.io`
- You will be redirected to a webpage click on generate token
- Copy the token
- Paste the token in the terminal and press enter
```
Token for server5.mohamed-abdelbaset.sbx.hashidemos.io:
  Enter a value:
Retrieved token for user admin
---------------------------------------------------------------------------------
Success! Logged in to Terraform Enterprise (server5.mohamed-abdelbaset.sbx.hashidemos.io)
```
- Create a new directory
```
mkdir ~/test
```
- Change directory
```
cd ~/test
```
- Create a `main.tf` with the following
```
terraform { 
  cloud { 
    hostname = "server1.mohamed-abdelbaset.sbx.hashidemos.io" 
    organization = "organization" 
    workspaces { 
      name = "workspace" 
    } 
  } 
}
resource "null_resource" "test" {
}
```
- Run terraform init and apply
```
$ terraform init
Initializing HCP Terraform...
Initializing provider plugins...
- Finding latest version of hashicorp/null...
- Installing hashicorp/null v3.2.4...
- Installed hashicorp/null v3.2.4 (signed by HashiCorp)
Terraform has created a lock file .terraform.lock.hcl to record the provider
selections it made above. Include this file in your version control repository
so that Terraform can guarantee to make the same selections by default when
you run "terraform init" in the future.
HCP Terraform has been successfully initialized!
You may now begin working with HCP Terraform. Try running "terraform plan" to
see any changes that are required for your infrastructure.
If you ever set or change modules or Terraform Settings, run "terraform init"
again to reinitialize your working directory.
$ terraform apply -auto-approve
2025-12-12T15:57:50.344+0100 [INFO]  Terraform version: 1.14.1
2025-12-12T15:57:50.345+0100 [INFO]  Go runtime version: go1.25.4
2025-12-12T15:57:50.345+0100 [INFO]  CLI args: []string{"terraform", "apply", "-auto-approve"}
2025-12-12T15:57:50.345+0100 [INFO]  Loading CLI configuration from /Users/mohamedayman/.terraform.d/credentials.tfrc.json
2025-12-12T15:57:50.345+0100 [INFO]  CLI command args: []string{"apply", "-auto-approve"}
2025-12-12T15:57:50.757+0100 [INFO]  cloud: starting Apply operation
Running apply in Terraform Enterprise. Output will stream here. Pressing Ctrl-C
will cancel the remote apply if it's still pending. If the apply started it
will stop streaming the logs, but will not stop the apply running remotely.
Preparing the remote apply...
To view this run in a browser, visit:
https://server1.mohamed-abdelbaset.sbx.hashidemos.io/app/organization/workspace/runs/run-mv2w5rq3jif3ey1z
Waiting for the plan to start...
Terraform v1.13.4
on linux_amd64
Initializing plugins and modules...
Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create
Terraform will perform the following actions:
  # null_resource.test will be created
  + resource "null_resource" "test" {
      + id = (known after apply)
    }
Plan: 1 to add, 0 to change, 0 to destroy.
null_resource.test: Creating...
null_resource.test: Creation complete after 0s [id=1603355538400899483]
Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```
- Check the status of the run from the UI


## Delete Infrastructure
- When done, you can remove the resources with terraform destroy, type:
```
terraform destroy
```