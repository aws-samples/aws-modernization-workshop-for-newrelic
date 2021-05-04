+++
title = "Lab 10: Kubernetes"
chapter = true
weight = 10
+++

Kubernetes (k8s) is becoming more and more popular among New Relic customers. The New Relic platform delivers unrivalled context into what is happening inside your Kubernetes clusters. This lab will involve running your own local Kubernetes cluster and setting up the New Relic integration. We will also go further, and be deploying a sample pod application that exposes open metrics, and send these custom metrics to New Relic.

# Step 1: Download and install Docker Desktop
[Download Docker Desktop](https://www.docker.com/products/docker-desktop).

# Step 2: Enable Kubernetes
Select the Docker desktop icon, select preferences, select Kubernetes.

In the selection, check **Enable Kubernetes** and **Deploy Docker Stacks to Kubernetes by default**. This will take a while to finish. You will see the status of Kubernetes from Docker desktop which will illustrate that Kubernetes is running.

After Kubernetes is installed, check that kubectl is installed by running the command kubectl version. If it is not, [install kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-macos) and check again.

You should also check that your kubectl is in the correct context. The context kubectl has instructs what cluster the command line interface will issue commands to. If the current context is not docker-for-desktop then issue command: `kubectl config use-context docker-for-desktop`

Check your Kubernetes node name using: `kubectl get nodes`

Check what services are deployed using: `kubectl get services`

# Step 3: Install Kubernetes Integration
[Follow the instructions](https://docs.newrelic.com/docs/integrations/kubernetes-integration/installation/kubernetes-installation-configuration#install) to install the Infrastructure integration. You should also ensure that your cluster meets [the compatibility and requirements](https://docs.newrelic.com/docs/integrations/kubernetes-integration/get-started/introduction-kubernetes-integration#compatibility).

# Step 4: Confirm the integration is configured
Run Insights command:
`SELECT * FROM K8sPodSample since 1 day ago` to ensure the cluster is properly configured and connected.
![](/images/lFf1B0T.png)

You should also verify that you have data in the New Relic [Kubernetes Cluster Explorer](https://one.newrelic.com/launcher/k8s-cluster-explorer-nerdlet.cluster-explorer-launcher)
![](/images/Pcpthbu.png)

# Step 5: Integrate with Prometheus
Download the NRI Prometheus manifest:
```
curl -O https://download.newrelic.com/infrastructure_agent/integrations/kubernetes/nri-prometheus-latest.yaml
```
Open the yaml file and update to include your **license key** and your **cluster name** ensuring it matches your cluster as checked earlier in step 2

Deploy the Prometheus integration:
```
kubectl apply -f nri-prometheus-latest.yaml
```

Verify that the OpenMetrics integration is running:
```
kubectl describe pod -l "app=nri-prometheus"
```

# Step 6: View metrics in New Relic
Wait a minute or so, and then in Insights run the query:
```
SELECT uniques(metricName) FROM Metric
```
![](/images/X27wOqz.png)

# Step 7: Installing a demo application
HAProxy is an open source program that provides high availability load balancing and proxying. It also works nicely with Prometheus!

Install HAProxy:
```
kubectl apply -f https://raw.githubusercontent.com/haproxytech/kubernetes-ingress/master/deploy/haproxy-ingress.yaml
```

HAProxy will live in another namespace. Check that it is running:
```
kubectl get svc --namespace=haproxy-controller
```

Wait for a minute before checking if the metrics are there in New Relic, using Insights query:
```
SELECT uniques(metricName) FROM Metric
```

**Would you be seeing HAProxy Metrics?**

Most likely not, right? And that is because of how we set up the integration earlier. For Prometheus scrape you will recall that the label has to be applied to the pods for which are scraping candidates.

# Step 8: Now add the scrape label, and check again
View the pods and their labels running command:
```
kubectl get pods --show-labels --namespace haproxy-controller
```

Add a new label to your HAProxy pod (set your pod name!):
```
kubectl label pods prometheus.io/scrape=true --namespace haproxy-controller
```

Validate the label is correctly applied:
```
kubectl get pods --show-labels --namespace haproxy-controller
```

Now wait for a minute or so, and check in Insights again using query: 
```
SELECT uniques(metricName) FROM Metric
```
![](/images/stIUgJp.png)

# Step 9: Kubernetes Events in New Relic
Kubernetes Events in New Relic is currently in beta release. This integration watches for events that take place in your Kubernetes clusters and sends those events to New Relic. You can visualise Kubernetes events either in New Relic One, in the Kubernetes Explorer under the events tab, or through the events tab in the Infrastructure UI, and of course through Insights!

[Follow the instructions on NR Docs](https://docs.newrelic.com/docs/integrations/kubernetes-integration/kubernetes-events/new-relic-kubernetes-events-integration).

After completing the installation steps, navigate in New Relic One to the Kubernetes Cluster Explorer and selectthe Events tab. It is most likely you do not have any events there yet but you will notice that you no longer have the instruction to deploy the integration. So let us go forth and deploy a demo application to generate some events.

# Step 10: Deploying nginx on Kubernetes
Let us automatically deploy the nginx manifest file, which will create a deployment of nginx and a replicaset of 2.
```
kubectl apply -f https://k8s.io/examples/application/deployment.yaml
```

Display the information about the deployment, and compare against the events now in the k8s cluster explorer events tab:
```
kubectl describe deployment nginx-deployment
```
![](/images/6cKGuG3.png)

Delete the deployment,and observe the events in the k8s cluster explorer events tab:
```
kubectl delete deployment nginx-deployment
```
![](/images/5MHDpkk.png)