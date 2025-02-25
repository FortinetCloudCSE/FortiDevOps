---
title: "Terraform Fundamentals"
linkTitle: "Terraform Fundamentals"
weight: 3
---

Terraform is an Infrastructure as Code (IaC) tool that allows you to define and provision cloud infrastructure using a high-level configuration language. In this guide, we'll walk through installing Terraform, setting up Azure credentials, and deploying a simple Virtual Machine (VM) in Microsoft Azure.

#### Prerequisites

Before getting started, ensure you have the following:

- AWS CLI installed and configured
- Terraform installed

#### Writing a Terraform Configuration

Create a new directory for your Terraform project and navigate into it:

```bash
cd ~
mkdir terraform
```
Pull the following basic terraform file that would create the following resources:

- VPC
- Subnets
- A group of instances

```bash
cd ~/terraform
curl https://raw.githubusercontent.com/Ahmed-AG/basic-web-page/refs/heads/main/ec2.tf > ec2.tf
```

Take a moment to examine the file

```bash
cat ec2.tf
```

#### Initializing and Applying the Terraform Configuration Run the following commands to deploy the VPC and the instance:

```bash
cd ~/terraform
terraform init
```

This initializes the Terraform project and downloads necessary providers.

```bash
terraform plan
```

This previews the changes Terraform will make.

```bash
terraform apply
```

Confirm the action when prompted by typing `yes`.

#### Verifying the Deployment
Once applied, you can check the virtual machine in the AWS consoleand examine the EC2 service. View the instances and the VPCs

#### Destroying the Infrastructure
To clean up resources, run:

```bash
terraform destroy
```
Confirm the action when prompted.

#### Conclusion
You have successfully installed Terraform and deployed an AWS VPC and INstances using Infrastructure as Code! This is just a starting point—Terraform can manage more complex infrastructure in Azure.
