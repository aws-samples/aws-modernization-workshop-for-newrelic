+++
title = "Lab 1: Deploying the Infrastructure agent"
chapter = true
weight = 1
+++

For this lab we will be deploying using AWS CloudFormation. This lab will automatically deploy the Infrastructure, Browser, and APM agents. Please note that you will need a keypair in the geography you are running the lab (London region required).

# Step 1: Building in AWS
_Recent updates to AWS CloudFormation means screenshots may differ_

**Using AWS Region eu-west-2 (London)**

In CloudFormation, select **Create Stack**.

We will be using a pre prepared template, so under specify template select Upload a template file and use [this template file](/lab1_NewRelic-WP-Infra-APM.template).

In *Specify Stack Details* is where the configuration for the build is done.

Give the Stack some meaningful name
![](/images/yT7nRWZ.png)

Select the key pair you wish to use
![](/images/NAvKCjS.png)

Apply your license key [instructions](https://docs.newrelic.com/docs/accounts/install-new-relic/account-setup/license-key)
![](/images/eSjxJUj.png)

You, optionally, can provide a custom name for the agent here (no spaces, same conventions as applying an agent anywhere) or leave this blank
![](/images/S7GWZAK.png)

Also, you must provide some details for the MySQL password that will be created automatically. Use only letters here.
![](/images/jZH2wft.png)

You can leave the other fields blank. And select *Next*

In *Configure Stack Options* we will leave everything as is, and scroll down to select **next**

In *Review* we will again scroll down to **Create Stack**

# Step 2: Viewing your stack

The process will take a few minutes. Behind the scenes, AWS is provisioning a Security Group and an EC2 instance. CloudFormation is listening to hear back. Then CloudFormation automatically will provision the infrastructure agent, the Wordpress and MySQL installation, and New Relic APM agent for PHP.

You can see the status of the stack creation by clicking the refresh icon in the stack event status.
![](/images/ZQMdwDk.png)

You can see the resources CloudFormation created in the resources tab
![](/images/8I1qIEp.png)

Once the items are provisioned, you should see the URL to complete the installation in the output tab
![](/images/J7VAgw6.png)