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
check installation guide : https://developer.hashicorp.com/terraform/tutorials/aws-get-started/install-cli
Verfify installation : `terrraform version`

# HCL 
HCL (HashiCorp Configuration Language) is a declarative language(define "what" you want . don't need how) used in Terraform to define infrastructure configurations
1. Block : blocks is define everyting in hcl .Blocks have a `type` that can have `zero or more required labels` followed by `{ }` brackets that contain the block's body. Blocks can be nested inside each other.The basic structure of a block is as follows:
```block_type "label_1" "label_2" {
  parameter = value
  nested_block {
    parameter = value
  }
}```
