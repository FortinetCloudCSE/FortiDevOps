---
title: "FortiCNAPP: Introduction to Cloud & DevOps"
linkTitle: "FortiCNAPP: Introduction to Cloud & DevOps"
weight: 1
archetype: "home"
# description: "FortiCNAPP: Introduction to Cloud & DevOpss"
---

<!--{{< launchdemoform >}}-->

### Welcome!

The aim of this workshop is to introduce some fundamental DevOps principles and concepts and some of the more common tools used to implement them. We'll start with an overview of a few workflows currently implemented on the FortinetCloudCSE team and then afterwards dive deeper on tooling.

### Start Your lab Environment:

During this class, you will need access to the following tools:

    - AWS
    - Git
    - Terraform
    - Docker
    - Kubernetes

As part of this class, you can provision a lab envirnoment that consists of and AWS Account as well as an EC2 Instance where you can run all of the above tools from.

#### Start your Lab

Start by Clicking on `Start Lab`: 
![](img/lab-1.png)

This will trigger the provisioning process for your labs, which should take about two minutes. Once the lab environment is provisioned, you should have access.
    - An AWS Account
    - An EC2 Instance that you can use to run all the labs

#### AWS Access 

There are two ways to access your AWS account:
    
    - Console access
    - CLI Access

##### Console Access:

To access the console, simply click on `Open Console` and use the `Username` and `Password` to access that `AWS Account`.

![](img/lab-2.png)

You will be greeted witht he Console Home page

![](img/aws-1.png)

Navigate to the `EC2 Service` and examine the running instances

![](img/aws-2.png)

You should see at least one instance:

![](img/aws-3.png)

#### CLI access

Later in this training, We will use the `Access Key ID` and the `Secret Access Key` to configure the CLI of the instance to be able to access the AWS APIs.

#### EC2 Instance Access

There are several ways for us to access the instance. We will use the AWS Console to do that.

Right click on the instance name and choose `Connect`

![](img/aws-4.png)

On the Connect to instance window, keep the default option of `EC2 Instance Connect` and click on `Connect`

![](img/aws-5.png)

If succesfull, you should see a terminal like interface within your AWS Console. This is where you will be executing different commands throughout this class.

![](img/aws-6.png)
