---
title: "Diagnosing Issues - Scenario #2"
date: 2021-5-01T09:00:00-00:00
weight: 23
draft: false
---

We have been getting reports that certain customers are getting 404 errors in our app! Let's try to figure out what's going on here.

### 1. Apply the Kube Files

```jsx
cd 2-lowerCase
for f in *.yaml; do envsubst < $f | kubectl apply -f -; done
```

### 2. Try sending the CURL request to the endpoint

[Sep-09-2021 22-01-22.mp4](/images/pixie/Sep-09-2021_22-01-22.mp4)

```jsx
curl --location --request GET '{GATEWAY_URL ENDPOINT}/PIXIE'
```

Hmm the request doesn't seem to work for the `PIXIE` hat but does work for others, like `pepe`. Let's dig into this further

### 3. Open on Pixie

Let's open Pixie to figure out exactly what's going on. 

![Screen Shot 2021-09-09 at 10.06.46 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.06.46_PM.png)

Click into the particular namespace

![Screen Shot 2021-09-09 at 10.07.43 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.07.43_PM.png)

It looks like the error rates are high for the `gateway-service`, `frontend-service`, and `fetch-service`. 

Let's figure out what's going on each service. Let's first go to the `px/service_stats` script and select the `frontend-service`

![Screen Shot 2021-09-09 at 10.38.58 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.38.58_PM.png)

We can see here the the three status codes that are being returned are 404, 304, and 200. Let's head to `px/http_data_filtered` to figure out the errors that are returning for `frontend`

![Screen Shot 2021-09-09 at 10.41.33 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.41.33_PM.png)

![Screen Shot 2021-09-09 at 10.41.10 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.41.10_PM.png)

![Screen Shot 2021-09-09 at 10.42.28 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.42.28_PM.png)

Hmm... We don't get much information other than the fact that Pixie cannot return the `PIXIE` hat, but can return other hats. Let's see what the gateway service is getting. 

![Screen Shot 2021-09-09 at 10.45.10 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.45.10_PM.png)

![Screen Shot 2021-09-09 at 10.46.03 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.46.03_PM.png)

![Screen Shot 2021-09-09 at 10.46.52 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.46.52_PM.png)

![Screen Shot 2021-09-10 at 12.20.00 AM.png](/images/pixie/Screen_Shot_2021-09-10_at_12.20.00_AM.png)

We found the culprit - We are making a case-sensitive MySQL call from the `fetch` function with a lower case pixie. The ticket has been filed and the fix will be deployed shortly! Onwards!