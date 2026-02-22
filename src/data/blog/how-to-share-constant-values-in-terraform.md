---
title: "How to share constant values in Terraform?"
description: "This tutorial will help you to solve the problem where you want to share common values among TF files or across modules."
author: "Sukumaar"
pubDatetime: 2024-11-29T00:00:00Z
image: "../../assets/images/how-to-share-constant-values-in-terraform.png"
tags: ["tutorial","bigdata-cloud"]
draft: false
featured: true
---

This tutorial will help you to solve the problem where you want to share common values among TF files or across modules.

Before approaching the problem, let's get some overview of Terraform and IaC.
![Default alt text](../../assets/images/how-to-share-constant-values-in-terraform.png)
___
Terraform and IaC:
* Terraform is an infrastructure-as-code (IaC) tool that allows users to define and manage cloud and on-premises infrastructure using a human-readable configuration file.
* By writing infrastructure configurations in a high-level language, such as HashiCorp Configuration Language (HCL), Terraform enables users to version, reuse, and share their infrastructure configurations just like they would with application code.
* At its core, Terraform uses a declarative approach, where users define what infrastructure they want to create, rather than how to create it. This means that Terraform takes care of the underlying complexity of provisioning and configuring infrastructure resources, allowing users to focus on defining the desired state of their infrastructure.
* When a user runs Terraform, it reads the configuration file and creates a plan for the desired infrastructure state. Terraform then uses this plan to create, update, or delete infrastructure resources, such as virtual machines, networks, and storage, across a wide range of cloud and on-premises environments.
* Terraform's IaC approach provides numerous benefits, including version control, reproducibility, and consistency, making it an essential tool for DevOps teams and infrastructure engineers.
___
### Problem:
Let's come back to the problem statement, the agenda of this tutorial is to share common values among TF files.

### Why it is little complicated to do it in terraform?
Terraform's modular design and lack of a built-in constant management system make it challenging to share constants across multiple modules and configurations.

### Solution:
There are many ways to share common variables/constants/values, but I am going to focus on a method where we are going to create a separate module.

**Let's assume this is the structure of your current terraform infra code directory:**
```bash
.
├── instances.tf
├── outputs.tf
├── terraform.tfvars
├── variables.tf
└── version.tf
```

**Shareable/common value can be added this way:**
1. Add a module by creating a directory with the name of your choice, here I am common_shared_vars as directory name.
2. Under that, I will create `output.tf`
3. I will mention the variable this way in the output.tf file
```hcl
output "vm_prefix" {
    value = "sample-tf-vm"
}
```
4. Import the module by creating tf file, I am naming it as same as module directory for simplicity, common_shared_vars.tf .
5. In the common_shared_vars.tf file, source common_shared_vars directory. Like this:
```hcl
module "common_shared_vars"{
    source = "./common_shared_vars" 
}
```
6. After this, all output values from `common_shared_vars/output.tf` file can be used like:
```bash
${module.common_shared_vars.vm_prefix}
```
7. Usage example, here I want to use `vm_prefix` in name of the vm resource.
```hcl
resource "aws_instance" "my_instance" {
  ami           = "ami-abcdxyz"  # Update with a valid AMI ID
  instance_type = "t2.micro"
  tags = {
    Name = "${module.common_shared_vars.vm_prefix}_${var.vm_name}"
  }
}
```
8. Infrastructure code directory will look something like this:
```bash
.
├── common_shared_vars
│   └── output.tf
├── common_shared_vars.tf
├── instances.tf
├── outputs.tf
├── terraform.tfvars
├── variables.tf
└── version.tf
```
9. This is not the only way to solve the common shareable value/constant, there are other ways too. But, I prefer this one.
___
_All codes related to this tutorial on my GitHub. [Click here for Github link of this tutorial](https://github.com/sukumaar/tf-common-var)._