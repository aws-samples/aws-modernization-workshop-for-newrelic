---
title: "Run a PxL script"
date: 2021-5-01T09:00:00-00:00
weight: 21
draft: false
---

Let's use Pixie's Live UI to run a built-in PxL script.

### Run a PxL Script

* Open your [New Relic One homepage](https://one.newrelic.com/)
* Click on the **Kubernetes** link

![Pixie](/images/pixie/nr1-kubernetes.png)

* Click on the name of your cluster

![Pixie](/images/pixie/select-cluster.png)

* Click on **Live debugging with Pixie**

![Pixie](/images/pixie/live-debugging.png)

* Select the `px/namespace` script from the `script` drop-down menu.

![select_script](/images/pixie/select_script.png)

4. This script has one required argument (as denoted with an `*`). Enter `default` for the `namespace` argument.

![select_namespace](/images/pixie/select_namespace.png)

After the script executes, you should see a view similar to the following:

![px_namespace](/images/pixie/px_namespace.png)

This script (`px/namespace`) shows:

- A service graph of HTTP1/2 traffic between the pods in the specified namespace.
- A list of the services and pods with high-level resource and application metrics.

This script takes one other argument, `start_time`. This argument is specified using the Time Picker dropdown at the top right hand side of your screen and provides the time window for the query.

![select_starttime](/images/pixie/select_starttime.png)
