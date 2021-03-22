+++
title = "Install Demo App"
chapter = false
weight = 1
+++


## Instructions

***IMPORTANT NOTE**: Creating this demo application in your AWS account will create and consume AWS resources, which **will cost money**.  We estimate that running this demo application will cost ~**$0.45/hour** with light usage.  Be sure to shut down/remove all resources once you are finished to avoid ongoing charges to your AWS account (see instructions on cleaning up/tear down below).*

### Getting started

To get AWS Bookstore Demo App up and running in your own AWS account, follow these steps (if you do not have an AWS account, please see [How do I create and activate a new Amazon Web Services account?](https://aws.amazon.com/premiumsupport/knowledge-center/create-and-activate-aws-account/)):

1. Log into the [AWS console](https://console.aws.amazon.com/) if you are not already.  
*Note: If you are logged in as an IAM user, ensure your account has permissions to create and manage the necessary resources and components for this application.* 
2. Choose one of the **Launch Stack** buttons below for your desired AWS region to open the AWS CloudFormation console and create a new stack. AWS Bookstore Demo App is supported in the following regions:

Region name | Region code | Launch
--- | --- | ---
US West (Oregon) |	us-west-2 | [![Launch Stack](https://cdn.rawgit.com/buildkite/cloudformation-launch-stack-button-svg/master/launch-stack.svg)](https://console.aws.amazon.com/cloudformation/home?region=us-west-2#/stacks/new?stackName=MyBookstore&templateURL=https://s3.amazonaws.com/aws-bookstore-demo/master-fullstack.yaml) 

3. Continue through the CloudFormation wizard steps
    1. Name your stack, e.g. MyBookstore
    2. Name your S3 bucket (must be lowercase and has to be unique across all existing bucket names in Amazon S3).  See [bucket naming rules](https://docs.aws.amazon.com/AmazonS3/latest/dev//BucketRestrictions.html#bucketnamingrules).
    3. Provide a project name (must be lowercase, letters only, and **under twelve (12) characters**).  This is used when naming your resources, e.g. tables, search domain, etc.
    4. After reviewing, check the blue box for creating IAM resources.
4. Choose **Create stack**.  This will take ~20 minutes to complete.
5. Once the CloudFormation deployment is complete, check the status of the build in the [CodePipeline](https://console.aws.amazon.com/codesuite/codepipeline/pipelines) console and ensure it has succeeded.
6. Sign into your application 
    1. The output of the CloudFormation stack creation will provide a CloudFront URL (in the **Outputs** table of your stack details page).  Click the link or copy and paste the CloudFront URL into your browser.
    2. You can sign into your application by registering an email address and a password.  Choose **Sign up to explore the demo** to register.  The registration/login experience is run in your AWS account, and the supplied credentials are stored in Amazon Cognito.
*Note: given that this is a demo application, we highly suggest that you do not use an email and password combination that you use for other purposes (such as an AWS account, email, or e-commerce site).*
    3. Once you provide your credentials, you will receive a verification code at the email address you provided. Upon entering this verification code, you will be signed into the application.

### Cleaning up

To tear down your application and remove all resources associated with AWS Bookstore Demo App, follow these steps:

1. Log into the [Amazon S3 Console](https://console.aws.amazon.com/s3) and  delete the buckets created for the demo app.  
   - There should be two buckets created for AWS Bookstore Demo App.  The buckets will be titled "X" and "X-pipeline", where "X" is the name you specified in the CloudFormation wizard under the AssetsBucketName parameter.  
   - *Note: Please be **very careful** to only delete the buckets associated with this app that you are absolutely sure you want to delete.*
2. Log into the AWS CloudFormation Console and find the stack you created for the demo app
3. Delete the stack

*Remember to shut down/remove all related resources once you are finished to avoid ongoing charges to your AWS account.*

&nbsp;

---

&nbsp;

### AWS Lambda

AWS Lambda is used in a number of different places to run the application.  The Lambda functions that will be instrumented as part of this workshop are shown below:

&nbsp;

**ListBooks**
Lambda function that lists the books in the specified product category

**GetBook**
Lambda function that will return the properties of a book.


**ListOrdersInCart**
Lambda function that lists the orders a user has placed.


**AddToCart**
Lambda function that adds a specified book to the user's cart.  Price is included in this function's request so that the price is passed into the cart table in DynamoDB.  This could reflect that the price in the cart may be different than the price in the catalog (i.e. books table) perhaps due to discounts or coupons.


**RemoveFromCart**
Lambda function that removes a given book from the user's cart.  


**GetCartItem**
Lambda function that returns the details of a given item the user's cart.


**UpdateCart**
Lambda function that updates the user's cart with a new quantity of a given book.


**ListOrders**
Lambda function that lists the orders for a user.


**Checkout**
Lambda function that moves the contents of a user's cart (the books) into the checkout flow, where you can then integrate with payment, etc.

