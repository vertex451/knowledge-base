# Why deploy with Terraform?

- **Full Lifecycle Management** - Terraform doesn't only create resources, it updates, and deletes tracked resources without requiring you to inspect the API to identify those resources.
- **Graph of Relationships** -  Terraform understands dependency relationships between resources. 
# Commands

- `terraform init`: creates `.terraform` dir and downloads needed providers
- `terraform plan`: shows the `execution plan`  - any changes that are required for your infrastructure.
- `terraform apply`: applies the `execution plan`
- `terraform show`: shows the current state(providers, resource, attributes)
- `terraform output` shows the custom output defined in the `output.tf` file.
- `terraform fmt`: formats indents, but not line breaks
- `terraform validate`: is being run before each of above commands
- `terraform destroy`: terminates resources managed by your Terraform project. 

# Diff symbols
`+`         create
`-`         destroy
`~`         update in place
`-/+`     destroy and then create replacement

# main.tf example

```
terraform {  
  required_providers {  
    google = {  
      source = "hashicorp/google"  
      version = "4.51.0"  
    }  
  }  
}  
  
provider "google" {  
  credentials = file("terraform-build-infrastructure-6e402ceb3a3f.json")  
  
  project = "terraform-build-infrastructure"  
  region  = "us-central1"  
  zone    = "us-central1-c"  
}  
  
resource "google_compute_network" "vpc_network" {  
  name = "terraform-network"  
}
```

# Providers

A provider is a plugin that Terraform uses to create and manage your resources. You can define multiple provider blocks in a Terraform configuration to manage resources from different providers. 
Examples: GCP, AWS, Azure

# Resources

Use `resource` blocks to define components of your infrastructure. A resource might be a physical component such as a server, or it can be a logical resource such as a Heroku application.

Resource blocks have two strings before the block: the resource type and the resource name:
```
resource "google_compute_network" "vpc_network"  {...}
```
Together, the resource type and resource name form a unique ID for the resource. For example, the ID for your network is `google_compute_network.vpc_network`

Resource blocks contain arguments which you use to configure the resource.

The [Terraform Registry GCP documentation page](https://registry.terraform.io/providers/hashicorp/google/latest/docs) documents the required and optional arguments for each GCP resource.

# State

When you applied your configuration, Terraform wrote data into a file called `terraform.tfstate`.

The Terraform state file is the only way Terraform can track which resources it manages, and often contains sensitive information, so you must store your state file securely. In production, we recommend [storing your state remotely](https://developer.hashicorp.com/terraform/tutorials/cloud/cloud-migrate) with Terraform Cloud or Terraform Enterprise.

# Introduce destructive changes

A destructive change is a change that requires the provider to replace the existing resource rather than updating it. (For instance, changing the disk image)

# Define input variables

```
variable "project" { }

variable "region" {
  default = "us-central1"
}
```

If a default value is set, the variable is optional. Otherwise, the variable is required.

Terraform automatically loads files called `terraform.tfvars` or matching `*.auto.tfvars` in the working directory when running operations.


# Define outputs

Terraform stores hundreds or thousands of attribute values for all your resources. As a user of Terraform, you may only be interested in a few values of importance. Outputs designate which data to display.

To use outputs you should define them at `outputs.tf` file and use `terraform output` command to display in the console.













