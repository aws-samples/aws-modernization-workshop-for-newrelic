---
title: "Launch EKS"
date: 2018-08-07T13:34:24-07:00
weight: 20
---


{{% notice warning %}}
**DO NOT PROCEED** with this step unless you have [validated the IAM role](/020_prerequisites/workspaceiam/#validate-the-iam-role) in use by the Cloud9 IDE. You will not be able to run the necessary kubectl commands in the later modules unless the EKS cluster is built using the IAM role.
{{% /notice %}}

#### Challenge:

**How do I check the IAM role on the workspace?**

{{%expand "Expand here to see the solution" %}}
Run `aws sts get-caller-identity` and validate that your _Arn_ contains `eksworkshop-admin`and an Instance Id.

```output
{
    "Account": "123456789012",
    "UserId": "AROA1SAMPLEAWSIAMROLE:i-01234567890abcdef",
    "Arn": "arn:aws:sts::123456789012:assumed-role/eksworkshop-admin/i-01234567890abcdef"
}
```

If you do not see the correct role, please go back and [validate the IAM role](/020_prerequisites/workspaceiam/#validate-the-iam-role) for troubleshooting.

If you do see the correct role, proceed to next step to create an EKS cluster.
{{% /expand %}}

### Create an EKS cluster

{{% notice warning %}}
`eksctl` version must be 0.38.0 or above to deploy EKS 1.20, [click here](/030_eksctl/prerequisites) to get the latest version.
{{% /notice %}}

Create an eksctl deployment file (tinyhats.yaml) use in creating your cluster using the following syntax:

```bash
cat << EOF > tinyhats.yaml
---
apiVersion: eksctl.io/v1alpha5
kind: ClusterConfig

metadata:
  name: tinyhats
  region: ${AWS_REGION}
  version: "1.20"

availabilityZones: ["${AZS[0]}", "${AZS[1]}"]

managedNodeGroups:
- name: nodegroup
  desiredCapacity: 2
  instanceType: t3.2xlarge
  ssh:
    enableSsm: true

# To enable all of the control plane logs, uncomment below:
# cloudWatch:
#  clusterLogging:
#    enableTypes: ["*"]

secretsEncryption:
  keyARN: ${MASTER_ARN}
EOF
```

{{% notice info %}}
Later on in this workshop, we will be installing Pixie to monitor our EKS cluster. Pixie requires a minimum of 2GB of RAM per node and recommends a minimum of 8GB per node. When deploying in a real world environment, if you see constant churning of your Pixie pods, you may wish to increase the amount of RAM allocated to each of your nodes.
{{% /notice %}}

Next, use the file you created as the input for the eksctl cluster creation.

```bash
eksctl create cluster -f tinyhats.yaml
```

{{% notice info %}}
Launching EKS and all the dependencies will take approximately 25 minutes
{{% /notice %}}
