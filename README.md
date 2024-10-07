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
provider "google" {
  project = "my-gcp-project"
  region  = "us-central1"
  credentials = file("path-to-credentials.json")
}

For azaure
provider "azurerm" {
  features {}
}

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


# Life cycle of terraform project
1. `terraform init` : Initializing a configuration directory downloads and installs the providers defined in the configuration