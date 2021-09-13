---
title: "Diagnosing Issues - Scenario #3"
date: 2021-5-01T09:00:00-00:00
weight: 23
draft: false
---
### Set up Scenario #3
Run the below command in your Cloud9 Terminal.
```bash
cd 3-sampleimage
for f in *.yaml; do envsubst < $f | kubectl apply -f -; done
```
Now, restart the MySQL database to reset your hats! It may take a second for everything to be ready again, so use `kubectl get pods --watch` to monitor the status. Once everything is "ready," get your `frontend-service` url and let's begin...

```bash
kubectl rollout restart deployment mysql
```

### We shipped a new feature!
The engineering team at Tinyhat.me has shipped a new feature to allow admins to preview their hats before it gets approved. However, initial beta testers have revealed some frustrations with the service, as page load speeds are reaching unbearable lengths. 

Try navigating to `{FRONTEND URL}/admin?password=ilovecats`, which is the approval interface for admins. Then, open Pixie to see what's going on!

![image](/images/pixie/3-cluster.png)

It looks like indeed the frontend service is getting a lot of latency. Let's figure out what's going on. 

### Using `px/service` to do root-cause analysis

![image](/images/pixie/3-service-1.png)
![image](/images/pixie/3-service-2.png)


### Using the flamegraph to find the potential fix
![image](/images/pixie/3-flamegraph-1.png)
![image](/images/pixie/3-flamegraph-2.png)