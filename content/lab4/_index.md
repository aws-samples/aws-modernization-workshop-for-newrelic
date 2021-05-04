+++
title = "Lab 4: Using Infrastructure"
chapter = true
weight = 4
+++

New Relic Infrastructure helps Ops teams handle an increasing amount of stress while reducing infrastructure issues -- and pager/alerts: you know, the ones that usually happen in the middle of night. New Relic Infrastructure, AWS, Kubernetes and Docker native, was built from the ground up to help today’s Ops teams correlate dynamic infrastructure health metrics with the config changes and other events that may cause them. By doing this, teams can cut through the noise and quickly see what infrastructure changes happened and what the impact is. Searching and alerting based on the infrastructure terms Ops teams use allows them to focus on optimising infrastructure, not just putting out fires.

# Step 1: Explore New Relic Infrastructure
In New Relic Infrastructure, you will also see the connected host and a list of events having happened on the agent during the install. It is important to note that the New Relic agent was one of the first things to be installed on the EC2 host so we are able to capture the Wordpress and MySQL installation there.

# Step 2: Deploy your testing instance
For the remainder of this lab we will use a new instance, and as such require an Ubuntu machine. You can deploy this by using the [Lab 4 AWS CloudFormation template provided](lab4.template). This will deploy a t2.micro instance to AWS London (EU-West-2) region. Important: make sure to edit the two places in the template replacing YOUR-LICENSE-KEY with your RPM license key. For this reason there is no auto-provision link, but you can upload the modified script [here](https://eu-west-2.console.aws.amazon.com/cloudformation/home?region=eu-west-2#/stacks/create/template).

Select *Upload a template file* and provide your edited template file here. And select *Next*

Enter a stack name, select your key pair set and select *Next*

On the stack options, leave them as default and select *Next*

On the review page, select *Create stack*

This will take a few minutes to create the Security Group and EC2 instance. After some time under "Outputs" you will see a PublicDNS record. Keep a note of this, at this address on port :8080 is where the Tomcat 8 demo service will be running which will be used in the Advanced APM lab. You also will use this publicDNS to connect to SSH on port :22 in the below section.

# Step 2: Access your instance
First of all, SSH into your EC2 instance:
```
ssh -i privateKey.pk ubuntu@host-name.compute.amazonaws.com
```

keeping in mind the path should be set to where your copy of privateKey.pk is saved. You had to select your key pair set when you created the instance, so remember it is the same one.

# Step 3: Performing a stress test
Now we will install a new package to the machine:
```
sudo apt -y install stress-ng
```
And generate some load:
```
stress-ng -c 2 -i 1 -m 1 --vm-bytes 256M -t 60s
```

# Knowledge Check
1. How did the package install show up in the New Relic Infrastructure user interface?
2. How did the stress command affect your instance?