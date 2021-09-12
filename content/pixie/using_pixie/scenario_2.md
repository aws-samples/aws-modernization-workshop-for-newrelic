---
title: "Diagnosing Issues - Scenario #2"
date: 2021-5-01T09:00:00-00:00
weight: 23
draft: false
---
### Set up Scenario #2

```bash
cd 2-lowerCase
for f in *.yaml; do envsubst < $f | kubectl apply -f -; done
```
Now, restart the MySQL database to reset your hats! It may take a second for everything to be ready again, so use `kubectl get pods --watch` to monitor the status.

```bash
kubectl rollout restart deployment mysql
```
### And... We have another bug!
We have been getting reports that certain customers are getting 404 errors in our app! Let's try to figure out what's going on here.
![image](https://user-images.githubusercontent.com/69332964/132967850-c1f68202-6f53-44a7-9f19-13283b2d9c24.png)
### Replicating the Issue
First, try requesting for the `PIXIE` hat on your frontend by  - it shouldn't work. Remember to use `kubectl get services` in order to get the URL. Then, 
![ezgif-4-a3dc76ed42f6](https://user-images.githubusercontent.com/69332964/132959460-b0126cd9-63f8-4d0f-862b-a399b6697151.gif)

Hmm the request doesn't seem to work for the `PIXIE` hat but does work for others, like `pepe`. Let's dig into this further...

Try using this command to make a request for the PIXIE hat. **Remember to change the {GATEWAY_URL_ENDPOINT} into your `gateway-service` url, which can be retrieved using `kubectl get services`.**
```jsx
curl --location --request GET '{GATEWAY_URL_ENDPOINT}/PIXIE'
```
Just like the user in the Tweet said, the GET request for the "PIXIE" hat also returns an error and a message saying that it does not exist. Strange!

**The engineering team has identified a couple possible root causes of this problem:**

[❓](#1-whats-wrong-with-the-frontend) The `frontend-service` is not displaying the hat image correctly due to the hat's special casing. 

❓ The `gateway-service` making an HTTP request for only hats with lowercase styles. 

❓ The `upload-service` corrupted the hat's data when it was uploaded to the MySQl database and/or the S3 Bucket. 

❓ The `fetch-service` executes unnecessary instructions that results in a case-sensitive situation when retrieving hats.

> Now, it's your job to identify the culprit using the powers of Pixie.
### Using `px/cluster` to survey the situation

Let's open Pixie to figure out exactly what's going on. 

![Screen Shot 2021-09-09 at 10.06.46 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.06.46_PM.png)

Click into the particular namespace

![Screen Shot 2021-09-09 at 10.07.43 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.07.43_PM.png)

As you can see in the **ERROR_RATE** column, it looks like the error rates are high for the `gateway-service`, `frontend-service`, and `fetch-service` (50%, 25%, and 60% respectively). 

However, this does not provide much detail on what's actually going on - except for the fact that something's not working!

### 1. What's wrong with the frontend?
Let's figure out what's going on each service. Let's first go to the `px/service_stats` script and select the `frontend-service`:

![image](https://user-images.githubusercontent.com/69332964/132999302-59b833ef-f461-4f73-b54f-235c9386f362.png)

We can see here the the three status codes that are being returned are **404, 304, and 200**. 

Let's head to `px/http_data_filtered` to figure out the errors that are returning for `frontend`. Try editing the **status_code** filter to view the requests for different codes. Below, we have a collection of the status **200** codes, which are the requests that were successful. 

> The frontend seems to be functioning properly for other images.

![Screen Shot 2021-09-09 at 10.41.10 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.41.10_PM.png)

If you filter for the status **400** codes, the requests that error out, you can see all the failed requests for the "PIXIE" hat the users wanted to try out. The body of the request reports that the hat supposedly "doesn't exist" even though it does in the SQL database.

![Screen Shot 2021-09-09 at 10.42.28 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.42.28_PM.png)

Hmm... We don't get much information other than the fact that Pixie cannot return the `PIXIE` hat, but can return other hats. Let's see what the gateway service is getting. 

![Screen Shot 2021-09-09 at 10.45.10 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.45.10_PM.png)

![Screen Shot 2021-09-09 at 10.46.03 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.46.03_PM.png)

![Screen Shot 2021-09-09 at 10.46.52 PM.png](/images/pixie/Screen_Shot_2021-09-09_at_10.46.52_PM.png)

![image](https://user-images.githubusercontent.com/69332964/133000635-22157085-825d-4d97-9a8b-ddb6d2ae7030.png)

![Screen Shot 2021-09-10 at 12.20.00 AM.png](/images/pixie/Screen_Shot_2021-09-10_at_12.20.00_AM.png)

We found the culprit - We are making a case-sensitive MySQL call from the `fetch` function with a lower case pixie. The ticket has been filed and the fix will be deployed shortly! Onwards!