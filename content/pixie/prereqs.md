---
title: "Prereqs"
date: 2021-5-01T09:00:00-00:00
weight: 5
draft: false
---

To get ready to use Pixie to debug an application on a Kubernetes cluster, we will deploy a demo microservices app named **TinyHat.me**.

# Creating your own tinyhat.me API 👒

## S3 Bucket
Click [here](https://console.aws.amazon.com/s3/home) to access the S3 console.

### Creating the bucket
Click `Create bucket`.

1. Name your bucket in the `Bucket name` field. (Example: `tinyhats`)
2. Edit the Public Access settings to match the configuration below:

![](/images/u6ZrfvH.png)
3. Leave all other settings as deafult and click `Create bucket`.

### Allowing public access
On the S3 console, click on the newly created S3 bucket by identifying it with the name you assigned it to. (Example: `tinyhats`)

1. Click on `Permissions` and scroll down to `Bucket policy`.
2. Click `Edit` and paste the below policy in the editor, **remembering to replace `tinyhats` with your actual bucket name, if it differs**
```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AddPerm",
            "Effect": "Allow",
            "Principal": "*",
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::tinyhats/*"
        }
    ]
}
```
3. Click `Save changes`. If you configured your bucket correctly, you should see the labels `Publicly accessible` and Access labeled as `Public`.
![](/images/cYq2MYc.png)

## Shhh.. Secrets!

### Obtaining AWS Access Keys
Click on your account name and click `My Security Credentials`.
![](/images/mvBb9l0.png)

Follow [this tutorial](https://www.msp360.com/resources/blog/how-to-find-your-aws-access-key-id-and-secret-access-key/) and keep your access key ID and secret somewhere safe!

Finally, modify these commands and enter them in your terminal to set the environment variables for secrets. Use the same terminal window to proceed to the next step.
``` 
export S3_ID=[AWS KEY ID]
export S3_SECRET=[AWS SECRET KEY]
```

## Working with EKS

Lets download and deploy the Tiny Hats code:

```
wget https://tinyhatkubefiles.s3.us-west-2.amazonaws.com/MySQL_Error.zip
unzip MySQL_Error.zip
kubectl apply -f MySQL_Error
```

Run `kubectl get pods --watch` to see when everything is finished spinning up.

### Adding a final secret
> To find the value of `ENDPOINT`, run `kubectl get services` and copy the URI corresponding to the `gateway-service`.

```
export ENDPOINT=[SEE ABOVE INSTRUCTIONS]
```

After exporting these environment variables, run `kubectl apply -f kube-bug-demo`

## AWS Rekognition
After you deploy your cluster, the last step is to ensure your node group has access to AWS Rekognition to retrieve face coordinates.

Go to the [IAM](https://console.aws.amazon.com/iamv2/home) dashboard and access [Roles](https://console.aws.amazon.com/iamv2/home?#/roles)

1. In the search bar, search for your cluster's name.
![](/images/I43b4Tz.png)
2. Select the role that contains `NodeInstanceRole` and then click on the blue button that says `Attach policies`.
3. Search for `rekognition`, select the permission named `AmazonRekognitionFullAccess`, and finally click `Attach policy`.
![](/images/F37R58I.png)

### You're done! 🎉
`tinyhats` should now be up and running! To test, run `kubectl get services` and copy the URI corresponding to the `gateway-service`. Paste the URL in the browser and make a GET request; an image in base64 should be in the response.

