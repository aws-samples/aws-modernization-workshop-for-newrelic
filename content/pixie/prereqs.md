---
title: "Prereqs"
date: 2021-5-01T09:00:00-00:00
weight: 5
draft: false
---

{{% notice note %}}
You will need a New Relic account. If you’re not already using New Relic, [sign up here](https://newrelic.com/signup?utm_source=devrel&utm_medium=event&utm_campaign=global-fy22-q2-k8s-pixie-content-workshop&utm_content=workshop) to get free, forever access. 
{{% /notice %}}

To get ready to use Pixie to debug an application on a Kubernetes cluster, we will deploy a demo microservices app named **TinyHat.me**, a simple API that provides virtual hats.

### Working with EKS

Lets download and deploy the Tiny Hats Application. 

First run the bash command to download the kubefiles 
```bash
wget -q -O tmp.zip https://tinyhatkubefiles.s3.us-west-2.amazonaws.com/tinyhats.zip && unzip tmp.zip && rm tmp.zip
```

To apply the exported environment variables, run

```bash 
cd ~/environment/tinyhats/0-setup
for f in *.yaml; do envsubst < $f | kubectl apply -f -; done
```

Then, run
```bash 
kubectl get pods --watch
```  
to see when everything is finished spinning up.

{{% notice warning %}}
You may notice the fetch service on a Crash Loop. This is completely normal because it takes MySQL a few minutes to spin up. 
{{% /notice %}}

### You're done! 🎉
`tinyhats` should now be up and running! All your pods are ready to roll once you see this output from the command 
```bash
kubectl get pods --watch
```

```
tinyhats:~/environment/tinyhats $ kubectl get pods --watch
NAME                                    READY   STATUS    RESTARTS   AGE
add-service-6b4466569b-w6blg            1/1     Running   0          67s
admin-service-6fcf54cdb5-p7kkf          1/1     Running   0          67s
fetch-service-68bc9fbf64-87wv5          1/1     Running   2          67s
frontend-service-58bcff7ddd-lhdbt       1/1     Running   0          67s
gateway-service-5dcb66f6ff-949nx        1/1     Running   0          67s
manipulation-service-57679fc544-f4tch   1/1     Running   0          67s
moderate-service-7c859f9f5d-k8h7n       1/1     Running   0          67s
mysql-576f9d87d9-xdh2l                  1/1     Running   0          66s
upload-service-7f57868797-4cmr4         1/1     Running   0          66s
``` 

Finally, run

```bash
kubectl get services
```

![gateway](/images/pixie/0-kubectl.png)

Export the URLs associated with the `Gateway-Service` by running. Make sure to replace `<YOUR_DATA_GOES_HERE>` with your Gateway Service `EXTERNAL-IP` from above.

```bash
export GATEWAYSERVICE=<YOUR_GATEWAY_GOES_HERE>
```

Try pasting the URL for `Frontend-Service` in the browser. You should be greeted with the wonderful face of Bob Ross. Keep this URL handy as you will need it later in the workshop. 
