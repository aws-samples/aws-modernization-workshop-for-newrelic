+++
title = "Enabling New Relic integrations using CloudShell"
chapter = false
weight = 1
+++


## Installing the New Relic CLI with CloudShell

{{% notice warning %}}
If you are participating in an AWS and New Relic led workshop using EventEngine, please skip to the next section as CloudShell is not yet available within EventEngine.
{{% /notice %}}

### Open CloudShell

AWS CloudShell is a browser-based shell that makes it easy to securely manage, explore, and interact with your AWS resources. CloudShell is pre-authenticated with your console credentials. Common development and operations tools are pre-installed, so no local installation or configuration is required. With CloudShell, you can quickly run scripts with the AWS Command Line Interface (AWS CLI), experiment with AWS service APIs using the AWS SDKs, or use a range of other tools to be productive. You can use CloudShell right from your browser and at no additional cost.

Locate and open CloudShell using the search bar in the AWS Console:

![CloudShell](/images/enable_monitoring/open-cloudshell.png)

Within CloudShell, install the New Relic CLI tool by executing the following command:

```bash
pip3 install newrelic-lambda-cli --user
```

Now that the New Relic CLI tool is installed, we can link our accounts by executing the following command (YOUR_LINKED_ACCOUNT_NAME refers to your New Relic account and can be named anything you wish):

```bash
newrelic-lambda integrations install -e --nr-account-id YOUR_NR_ACCOUNT_ID --linked-account-name YOUR_LINKED_ACCOUNT_NAME --nr-api-key YOUR_NEW_RELIC_USER_KEY
```

If successful, you should see output similar to:

```
Validating New Relic credentials
Retrieving integration license key
Checking for a pre-existing link between New Relic and AWS
Creating the AWS role for the New Relic AWS Lambda Integration
Waiting for stack creation to complete... ✔ Done
...
✨ Install Complete ✨
```

Congratulations!  You have successfully linked your AWS account with New Relic!  

### Multiple AWS regions and accounts in a real-world environment

The newrelic-lambda CLI should be run once per region, with the --aws-region parameter. Use the same linked account name, and the tool will detect that the account link has been created already. The license key secret needs to be created in each region.

Similarly, several AWS accounts can be linked to a New Relic account. Give each account a different linked account name. The --aws-profile argument to the CLI tool will select the named profile. The tool uses the same configuration as the AWS CLI.

### Cleanup

If you no longer wish for your AWS account to be connected to New Relic, simply access CloudFormation from the AWS Console and delete any stacks that begin with NewRelic.
