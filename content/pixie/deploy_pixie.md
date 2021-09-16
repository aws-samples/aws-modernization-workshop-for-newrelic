---
title: "Deploy Pixie"
date: 2021-5-01T09:00:00-00:00
weight: 10
draft: false
---

### Install Pixie

Pixie can be deployed alongside New Relic's Kubernetes Intgeration using the New Relic One guided install:

* Navigate to your [New Relic One homepage](https://one.newrelic.com/?utm_source=devrel&utm_medium=event&utm_campaign=global-fy22-q2-k8s-pixie-content-workshop&utm_content=workshop)
* Click on the green **Add more data** button at the top right hand side of your screen

![GuidedInstall](/images/pixie/add-more-data.png)

* Click on **Guided install**

![GuidedInstall](/images/pixie/guided-install.png)

* Select your New Relic account from the dropdown box, and then click the **Continue** button

![GuidedInstall](/images/pixie/account-selection.png)

* Click the **Kubernetes** button

![GuidedInstall](/images/pixie/kubernetes.png)

* Enter `tinyhats` as the name of your cluster, and then click the **Continue** button:

![GuidedInstall](/images/pixie/cluster-name.png)

{{% notice info %}}
If you have instrumented other Kubernetes clusters in your New Relic account, you will need to first click on the **Instrument new cluster** link before being able to enter a cluster name.
{{% /notice %}}

* Ensure the options selected match those in the image below:

![GuidedInstall](/images/pixie/setup-options.png)

* Copy the command and paste it into the shell of your *Cloud9* environment:

![GuidedInstall](/images/pixie/helm-command.png)

{{% notice info %}}
Deploying Pixie will take about 5 minutes.
{{% /notice %}}

* Check if the Pixie components have been deployed:

```bash
kubectl get pods -n newrelic
```

You should see output similar to below. All pods should be ready and available before proceeding.

{{< output >}}
NAME                                      READY   STATUS    RESTARTS   AGE
kelvin-7749f484bc-9vmrd                   1/1     Running   0          2m14s
nats-operator-7d686798f4-tkjn4            1/1     Running   0          2m28s
pl-nats-1                                 1/1     Running   0          2m21s
vizier-certmgr-5bc8d5dcc8-grrqt           1/1     Running   0          2m14s
vizier-cloud-connector-5cdf4ccb94-crz8p   1/1     Running   0          2m14s
vizier-metadata-0                         1/1     Running   0          2m13s
vizier-pem-5fs5d                          1/1     Running   0          2m13s
vizier-pem-5kxm5                          1/1     Running   0          2m13s
vizier-pem-b79fz                          1/1     Running   0          2m13s
vizier-pem-c5bh8                          1/1     Running   0          2m13s
vizier-pem-ddjvw                          1/1     Running   0          2m13s
vizier-pem-rx7f4                          1/1     Running   0          2m13s
vizier-proxy-7c75497cdd-4lzm5             1/1     Running   0          2m13s
vizier-query-broker-6f7fc849f-wdpcz       1/1     Running   0          2m13s
{{< /output >}}

* Back in your New Relic One tab, click the **Continue** button.  If your cluster was instrumented successfully, you should see the following message:

![GuidedInstall](/images/pixie/install-success.png)

Congratulations!  You have successfully instrumented your EKS cluster with both the New Relic Kubernetes integration and New Relic's Pixie!
