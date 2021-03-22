+++
title = "Enabling New Relic integrations using Cloud9"
chapter = false
weight = 418
+++


{{% notice warning %}}
If you already installed the New Relic CLI with CloudShell, please skip this section.
{{% /notice %}}

## Installing the New Relic CLI with Cloud9

Within the shell in your Cloud9 environment, install the New Relic CLI tool by executing the following command:

```bash
pip3 install newrelic-lambda-cli --user
```

![Xloud9 Shell](/images/getting_started/cloud9-shell-nr-cli.png)

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
