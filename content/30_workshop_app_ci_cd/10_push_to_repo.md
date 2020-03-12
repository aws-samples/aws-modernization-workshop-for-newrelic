+++
title = "Build & Push to Repo"
chapter = false
weight = 10
+++

### Push to Amazon Elastic Container Repository (ECR)
Now it's time to compile and package your code.  Copy and paste the below code into Cloud9's terminal window

```bash
cd ~/environment/modernization-workshop/java-app
docker build -t modernization-workshop .

docker tag modernization-workshop:latest $(aws ecr describe-repositories --repository-name modernization-workshop --query=repositories[0].repositoryUri --output=text):latest


eval $(aws ecr get-login --no-include-email)
docker push $(aws ecr describe-repositories --repository-name modernization-workshop --query=repositories[0].repositoryUri --output=text):latest
```

If you watch the screen you should see the docker image build process animating the terminal

{{% notice info %}}
If successful, you should see the message as below.
{{% /notice %}}

<pre>
The push refers to repository [1234567891011.dkr.ecr.us-west-2.amazonaws.com/modernization-workshop]
8d2f7b95f78d: Pushed 
82852e5eaa9d: Pushed 
9df07df94e41: Pushed 
aa90bcce39de: Pushed 
d9ff549177a9: Pushed 
latest: digest: sha256:4229b5fe142f6d321ef2ce16ff22070e410272ee140e7eec51540a823dcd315a size: 1369
</pre>

