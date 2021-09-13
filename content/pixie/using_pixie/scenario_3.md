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

### Using `px/flamegraph` to find the potential fix
We are going to use Pixie's Always-On Profiling feature to investigate this slowdown, using a flamegraph to identify a performance issue within the application code.

Every ~10ms, the Pixie profiler samples the current stack trace on each CPU. The stack trace includes the function that was executing at the time of the sample, along with the ancestor functions that were called to get to this point in the code.

The collected samples are aggregated across a larger 30 second window that includes thousands of stack traces. These stack traces are then grouped by their common ancestors. At any level, the wider the stack, the more often that function appeared in the stack traces. Wider stack traces are typically of more interest as it indicates a significant amount of the application time being spent in that function.

The background color of each box in the flamegraph adds an extra dimension of data:

- Dark blue bars indicate K8s metadata info (node, namespace, pod, container).
- Light blue bars represent user space application code.
- Green bars represent kernel code.

![image](/images/pixie/3-flamegraph-1.png)

However, this flamegraph has too much information, we want to narrow this down to the namespace that we care about - `/default`. 

![image](/images/pixie/3-flamegraph-2.png)

Much better. it looks like based on the calls for `/manipulate` and `admin`, we individually generate the sample image every time the `/admin` route is being called. Instead of making so many HTTP requests everytime the admin dashboard is being accessed, we should save the mockup when the user uploads the image to improve page load speeds!