---
title: "Update IAM settings for your Workspace"
chapter: false
weight: 417
---

1. Return to your Cloud9 workspace and click the gear icon (in top right corner).
1. Select **AWS Settings**.
1. Turn off **AWS managed temporary credentials**. 
1. Close the **Preferences** tab.

![c9disableiam](/images/c9disableiam.png)

5. Copy and run the shell commands below in your Cloud9 terminal. These shell commands will: 

- Install jq- jq is a command-line tool for parsing JSON

- Ensure temporary credentials aren’t already in place.

- Remove any existing credentials file.

- Set the region to work with our desired region.

- Validate that our IAM role is valid. 

```sh
sudo yum -y install jq
rm -vf ${HOME}/.aws/credentials
export ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')
test -n "$AWS_REGION" && echo AWS_REGION is "$AWS_REGION" || echo AWS_REGION is not set
echo "export ACCOUNT_ID=${ACCOUNT_ID}" | tee -a ~/.bash_profile
echo "export AWS_REGION=${AWS_REGION}" | 
tee -a ~/.bash_profile
aws configure set default.region ${AWS_REGION}
aws configure get default.region
aws sts get-caller-identity --query Arn | grep NR-Lambda-Workshop -q && echo "IAM role valid" || echo "IAM role NOT valid"
```

If the IAM role is confirmed as valid, you will see the following output in your shell after executing the commands above:

```sh
IAM role valid
```

If the IAM role is not valid, <span style="color: red;">**DO NOT PROCEED**</span>. Go back and confirm the steps on this page. 
