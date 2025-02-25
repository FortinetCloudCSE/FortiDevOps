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

#### Installing Terraform

To install Terraform, follow these steps based on your operating system:

### Linux/macOS

```bash
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add - sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt-get update && sudo apt-get install terraform
```
Alternatively, use Homebrew on macOS:

```bash
brew tap hashicorp/tap
brew install hashicorp/tap/terraform
```

### Windows
Download the latest Terraform binary from [Terraform Downloads](https://www.terraform.io/downloads.html) and add it to your system `PATH`.

#### Verifying Installation

Run the following command to check if Terraform is installed correctly:

```bash
terraform version
```

#### Configuring Azure Credentials

Before deploying infrastructure, authenticate with Azure using the Azure CLI:

```bash
az login
```

If you have multiple subscriptions, set the desired subscription:

```bash
az account set --subscription "<SUBSCRIPTION_ID>"
```

#### Writing a Terraform Configuration

Create a new directory for your Terraform project and navigate into it:

```bash
mkdir terraform-azure-vm && cd terraform-azure-vm 
```

Create a new file named `main.tf` and add the following configuration:

```hcl
provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "East US"
}

resource "azurerm_virtual_network" "example" {
  name                = "example-network"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "example" {
  name                 = "example-subnet"
  resource_group_name  = azurerm_resource_group.example.name
  virtual_network_name = azurerm_virtual_network.example.name
  address_prefixes     = ["10.0.1.0/24"] }

resource "azurerm_network_interface" "example" {
  name                = "example-nic"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name

  ip_configuration {
    name                          = "internal"
    subnet_id                     = azurerm_subnet.example.id
    private_ip_address_allocation = "Dynamic"
  }
}

resource "azurerm_linux_virtual_machine" "example" {
  name                  = "example-vm"
  resource_group_name   = azurerm_resource_group.example.name
  location              = azurerm_resource_group.example.location
  size                  = "Standard_B1s"
  admin_username        = "adminuser"
  network_interface_ids = [azurerm_network_interface.example.id]

  admin_ssh_key {
    username   = "adminuser"
    public_key = file("~/.ssh/id_rsa.pub")
  }

  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }

  source_image_reference {
    publisher = "Canonical"
    offer     = "UbuntuServer"
    sku       = "18.04-LTS"
    version   = "latest"
  }
}
```

#### Initializing and Applying the Terraform Configuration Run the following commands to deploy the Azure VM:

```bash
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
Once applied, you can check the virtual machine in the Azure Portal under `Virtual Machines`.

#### Destroying the Infrastructure
To clean up resources, run:

```bash
terraform destroy
```
Confirm the action when prompted.

#### Conclusion
You have successfully installed Terraform and deployed an Azure Virtual Machine using Infrastructure as Code! This is just a starting point—Terraform can manage more complex infrastructure in Azure.
