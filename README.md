# Infrastructure-as-code(IAC)
Infrastructure as Code (IaC) tools allow you to manage infrastructure using configuration files rather than through a graphical user interface/Command line interface/Api. Most of cloud provider has own infrastructure as code machanism. Following are the example list
1. Iac tool for AWS is CloudFormation template
2. Iac tool for azure is Azure Resource Manager
3. Iac tool for Openstack is Head teamplate
4. Iac tool for GCP is Google Cloud Deployment Manager

# Why terraform 
We can manage multiple cloud platform using single Iac tool called terraform. we don't need individual cloud iac tool.
Competitor for terraform called `pulumi`/crossplane

# Install terraform 
check installation guide : https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli.<br />
Verfify installation : `terrraform version`

# HCL 
HCL (HashiCorp Configuration Language) is a declarative language(define "what" you want . don't need how) used in Terraform to define infrastructure configurations.HCL files typically have a .tf extension

1. Block : blocks is define everyting in hcl .Blocks have a `type` that can have `zero or more required labels` followed by `{ }` brackets that contain the block's body. Blocks can be nested inside each other.The basic structure of a block is as follows:
```
block_type "label_1" "label_2" {
  parameter = value
  nested_block {
    parameter = value
  }
}
```

# Terraform Block
The terraform  block contains Terraform settings. for example
1. `required_version`. Defines the Terraform version requirements
3. `backend`: Configures the location where the Terraform state is stored.
2. required providers
    ```
    <local_name> = {
      source  = "<source_address>"
      version = "version"
    }
    ```

    i.`local_name` . its unique identifier within this module(module-specific). every provider has a preferred local name(we recommend using a provider's preferred local name, which is usually the same as the "type" portion of its source address), which it uses as a prefix for all of its resource types. (For example, resources from hashicorp/aws all begin with aws, like aws_instance or aws_security_group.)

    *** it's sometimes necessary to use two providers with the same preferred local name in the same module, usually when the providers are named after a generic infrastructure type. Terraform requires unique local names for each provider in a module, so you'll need to use a non-preferred name for at least one of them.



    ii.`source` . A provider's source address is its global identifier. Examples of valid provider source address formats include: HOSTNAME/NAMESPACE/TYPE

    ** `HOSTNAME` (optional): The hostname of the Terraform registry that distributes the provider. If omitted, this defaults to registry.terraform.io, the hostname of the public Terraform Registry.

    ** `NAMESPACE`: An organizational namespace within the specified registry. For the public Terraform Registry(registry.terraform.io) and for HCP Terraform's private registry, this represents the organization that publishes the provider. This field may have other meanings for other registry hosts.

    ** `Type`: A short name for the platform or system the provider manages. Must be unique within a particular namespace on a particular registry host.

    iii. `version` define source version (Optional) . default recent version

# Provider Block
configures the specified provider

```
provider "<local_name>" {
  parameter = value
}
```

For aws : 
1. `region`: Defines the AWS region to work in
2. `profile` : if you don't want to use aws cli `default` profile then override it . if dont want to use profile you can directly use access key and key(not recommendate)
   `access_key` = "your-aws-access-key"
   `secret_key` = "your-aws-secret-key"
3.`alias` : Allow to create multiple configurations for the same provider .It is used at `resource` block . check it there
4.`version`:  which we no longer recommend depricate (use provider requirements instead)   

For google : 

```
provider "google" {
  project = "my-gcp-project"
  region  = "us-central1"
  credentials = file("path-to-credentials.json")
}```

For Azure
```
provider "azurerm" {
  features {}
}
```

# Resource Block
Define components of your infrastructure. A resource might be a physical or virtual component such as an EC2 instance, or it can be a logical resource such as a Heroku application.

```
resource "<resource_type>" "resource_name" {
  parameter = value
}
```

The prefix of the type maps to the name of the provider.For example, an `aws_instance` resource uses the default `aws` provider configuration
Together, the resource type and resource name form a unique `ID` for the resource
1.Resource Arguments : check documentation which arguments are available . [Link](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)

2. Meta-arguments
  i. `provider` : specify your provider by the form `<PROVIDER NAME>.<ALIAS> `. if no specify then it use default provider (which one is no `alias` property). Documentation link [Link](https://developer.hashicorp.com/terraform/language/resources/syntax)


# Life cycle of terraform
1. `terraform init` : Initializing a configuration directory downloads and installs the providers defined in the configuration. Its `mandatory`
2. `terraform fmt` : Automatically updates/format(fmt) configurations in the current directory for readability and consistency.
3. `terraform validate`: Make sure your configuration is syntactically valid and internally consistent
4. `terraform plan`: to preview the changes that will be made to your infrastructure
5. `terraform apply`: Apply the configuration to your infrastructure. its `mandatory`
6. `terraform show` : After apply, Terraform wrote data into a file called `terraform.tfstate`. It track which resources it manages so that it can update or destroy those resources going forward and often contains sensitive information,so you must store your state file securely. This command inspect/show the current state. 
  For storing state file remotely you can use following 
  1. `HCP Terraform/ Terraform enterprise`. It is recommendate . https://developer.hashicorp.com/terraform/tutorials/cloud/cloud-migrate
  2. `Third party backend remote` : 
  https://developer.hashicorp.com/terraform/language/backend/configuration
7.`terraform state` : for advanced state management .for example `terraform state list` list the resource of your project.  


# Create first terraform project

It will create single ec2 instance. Create folder->main.tf then add following content

```
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.16"
    }
  }

  required_version = ">= 1.2.0"
}

provider "aws" {
  region = "ca-central-1"
}

resource "aws_instance" "app_server" {
  ami           = "ami-00498a47f0a5d4232"
  instance_type = "t2.micro"

  tags = {
    Name = "ExampleAppServerInstance"
  }
}

```

1. `AMI(Amazon machine image)` : check list ec2->Images->AMI
2. `instance_type`: Define instance type
3. `count`: Define how many instance you need

  Aws instance doc
  https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/instance

For Execution use `mandatory` command  

# Terrafrom module 
Modules are the main way to package and `reuse` resource configurations with Terraform.

In the structure:
`Root module`: The main.tf in the root folder contains the top-level configurations.
`Submodule`: The modules/aws_instance folder contains the reusable EC2 instance module.

From `Root module`  calls other modules by following syntax

```
module "<module_name>"{
  source ="<path for submodule main.tf>"
}
```
1. Move `main.tf` content to `module->aws_instance->main.tf` to create `submodule`
2. update main.tf with following content
```
module "ec2" {
  source = "./module/aws_instance"
}
````

