+++
title = "Enable Serverless Monitoring"
chapter = true
weight = 30
+++

{{% notice note %}}
To continue this workshop, a New Relic account is required.  A new account is recommended and can be created by clicking [here](https://newrelic.com/signup)
{{% /notice %}}

## Serverless Monitoring for AWS Lambda

Serverless monitoring for AWS Lambda offers in-depth performance monitoring for your Lambda functions. When you enable serverless monitoring using our Lambda extension, this is what happens:

![Monitoring Diagram](/images/enable_monitoring/lambda-monitoring.png)

 1. You configure your Lambda function to include our layer for the runtime you've chosen.
 1. As your code runs, our Lambda layer gathers telemetry data about the invocation and its execution.
 1. Just before execution finishes, the Lambda layer sends the data it has gathered to the New Relic Lambda extension, which is bundled with the layer.
 1. The extension sends the data to New Relic, along with additional information from AWS Lambda.

### What's in the New Relic Lambda layer?
The layer for your runtime contains the New Relic Lambda extension  . This executable extends your Lambda function. The extension sends telemetry data to New Relic, and interacts with AWS directly to enhance the data we gather, while minimizing the impact of instrumentation on your application's performance.

For Node.js and Python, the layer contains the New Relic agent code, and a wrapper for your Lambda handler. For other runtimes, we take an SDK approach, providing you with the tools to instrument your code, while taking advantage of emerging standards like OpenTracing and OpenTelemetry.

### Enable serverless monitoring
Two things have to happen to let New Relic gather telemetry from your Lambda functions.

 1. Link your AWS account with your New Relic account.
 1. Configure each of your functions to include our Lambda extension.

In this section we will be linking your AWS account with your New Relic account.

### Link your AWS account with your New Relic account
When you link your AWS account to New Relic, you're granting permission to New Relic to create an inventory of your AWS account, and gather CloudWatch metrics for your Lambda functions. Resources in your AWS account then show up as entities in the entity explorer, decorated with config information.

**To link your account, you will need the following information:**

 * Your New Relic account ID.
 * Your New Relic personal User key

### Create a New Relic User key

Navigate to your [New Relic One homepage](https://one.newrelic.com/) and once again click on the button at the top right hand corner of the screen.  This time, select ***API keys*** from the dropdown dialog:

![API Keys](/images/enable_monitoring/api-keys.png)

On the API keys page, click on the ***Create key*** button located toward the right hand side of your screen:

![Create key](/images/enable_monitoring/create-key.png)

You will notice that your account number is listed in the dropdown box under the ***Account*** heading.  Please open a text file in your favorite text editor and type this in for easy access later on in this workshop.

Create your key by filling out the form on the right hand side of your screen as shown below, ensuring that the **Key type** field is set to **User**, and clicking on the ***Create key*** button:

![Create key dialog](/images/enable_monitoring/create-key-dialog.png)

Copy your newly created key (as shown below) and paste it into the text file as well:

![Copy key](/images/enable_monitoring/copy-key.png)

{{% notice note %}}
Click [here]({{< ref "414_cloud9.md" >}}) if you are at an AWS event where the Event Engine is being used
{{% /notice %}}

{{% notice note %}}
Click [here]({{< ref "1_install_nr_cli_cloudshell.md" >}}) if you are running this individually in your own AWS Account
{{% /notice %}}
