---
title: "Diagnosing Issues - Scenario #1"
date: 2021-5-01T09:00:00-00:00
weight: 22
draft: false
---

### Set up Scenario #1
Run the below command in your Cloud9 Terminal.
```bash
cd ~/environment/1-mysql
for f in *.yaml; do envsubst < $f | kubectl apply -f -; done
```

### What's the problem?
Your TinyHat.me API has already taken off and users are actively requesting & uploading their own hats.  Unfortunately, there appears to be a bug in the code!

![image](https://user-images.githubusercontent.com/69332964/132967698-bba673cf-b433-499a-a045-20fe767a2e80.png)

*We are receiving complaints from our users that unmoderated hats are showing up on the website!* In order for a hat to appear on the frontend, admins of TinyHat.Me must approve them first. However, that's not what's happening...

Let's see if we can use Pixie to figure out why image uploads are bypassing our moderation queue.

### Replicating the Issue
First, let's upload a hat ourselves to see the bug in action.  

```bash
curl --location --request POST ${GATEWAYSERVICE}/add --form 'name="Tiny Hats are cool"' --form 'image=@"/home/ec2-user/environment/hat.png"'
```

If successful, you should see a response similar to this:

```json
{
    "result": {
        "key": "tmvxch9exufm1jh3",
        "fileName": "tmvxch9exufm1jh3.jpeg",
        "url": "https://tinyhats.s3.amazonaws.com/tmvxch9exufm1jh3.jpeg",
        "description": "Tiny Hats are cool",
        "approve": "false"
    }
}
```

Now, go back to the frontend and swipe through the gallery of hats. Yikes! Your unapproved picture should be there - that's not supposed to happen.

### Debugging with Pixie
Let's hop back over to Pixie and see if we can figure out what's going on. Refer back to "Run a PxL script" to recall how to access the Pixie dashboard.
#### 1. Are the admins secretly approving bad hats?
To get a high level view of what the application is doing, run the `px/cluster` script:

![image](https://user-images.githubusercontent.com/69332964/132966785-a29c4d74-153b-4f01-8c10-cb618e8e99b7.png)

Let's also specifically select the `default` namespace to narrow the pods down.

![image](https://user-images.githubusercontent.com/69332964/132966748-f8e55492-f427-4f6e-b36c-09cbbe6e67e1.png)

Below, notice two separate processes:
1. The `gateway-service` is calling `add-service` which then communicates with `upload-service` to suggest a hat.
2. The `gateway-service` is calling the `fetch-service` to retrieve the hats to display on the website.

> No moderation is occurring! Yet, the hats added in **process 1** are automatically shown to the public in **process 2.** 

We have now identified that nothing is being moderated (so the admins aren't lying), but we will need to dig a bit further to find the true problem...

![image](https://user-images.githubusercontent.com/69332964/132966829-665e645b-1ece-4825-aed8-2c75b154030a.png)

#### 2. How are the services communicating?

Since we know that the admins didn't approve the inapproriate hats, we can continue to narrow down the issue in Pixie by viewing the HTTP requests from pod to pod. The question now is: Are all of our services are correctly communicating with one another? Here are the suspect microservices - the ones that modify and send the hat data:
* `add-service`
* `fetch-service`

> For context, there is an attribute for each submitted hat: `approve`. If `approve` is true, the hat is authorized to appear on the page, whereas if it is false, it should not be on the website.

Run the `px/http_data_filtered` script:

![FilteredData](/images/pixie/http_data.png)

Then, select the `add-service` pod:

![AddService](/images/pixie/add-service.png)

You should see output similar to the image below:

![AddServiceSuccess](/images/pixie/add-service-success.png)

As we can see by the **RESP_STATUS** of *200*, this service appears to be functioning properly. Let's see what data is being sent back from this service by clicking on the most recent request:
![image](https://user-images.githubusercontent.com/69332964/132967246-4dddf661-e678-40e2-8f57-814fdee55ce6.png)
As clearly shown in Pixie, the `add-service` is **correctly identifying** the hat as unapproved: setting the `approve` attribute as "false." We can now rule out this service.


By the process of elimination, `fetch-service` now seems to be the troubler-maker in this situation. Let's take a look at the HTTP requests going through this service, using the *same process* we went through for `add-service`.

![image](https://user-images.githubusercontent.com/69332964/132967365-bc5ae7c3-399e-4d89-b77a-dd8043b902c1.png)

Once again based on **RESP_STATUS**, `fetch-service` does not seem to be erroring out. Looking at the body of the response that is filled with hats for the frontend to display, it is truncated. However, we now have enough clues to prove that this bug is an issue related to **data retrieval** and not **data storage**. 
1. Using the `px/http_data_filtered` script for `add-service`, we determined that the hat is correctly being **stored** as unauthorized.
2. Like `px/cluster` showed, `fetch-service` is the only service **retrieving** hats and providing the frontend with the "approved" hats to make available to users.
![image](https://user-images.githubusercontent.com/69332964/132967472-7fbbf0fd-49dd-4e17-ba6f-759b9f4afcc6.png)

#### 3. What is the issue with retrieving hats? 
Now that we know it's an issue with retrieving data, let's take a look at the SQL queries this cluster runs by executing `px/mysql_data` and clicking on the SQL query of first result we receive, which happens to be from `fetch-service`:

![MysqlData](/images/pixie/mysql_data.png)
![image](https://user-images.githubusercontent.com/69332964/132967540-79417ea6-dc49-4a32-8c58-fa24f019a818.png)

Having clicked on the SQL query, we can now see the complete JSON response of this particular transaction, which includes the full SQL query and the number of rows:

![MysqlQuery](/images/pixie/sql_query.png)
![image](https://user-images.githubusercontent.com/69332964/132967572-8cd2c22c-a2c3-4c53-be76-b2c418a15557.png)

### We have found our culprit!
The SQL query here is selecting **ALL images from the database** without specifying a **WHERE clause** to determine whether or not the image was actually ever approved. You could have also noticed that the number of resulting rows from the query was way too big! The correct query should have been:

```sql
SELECT * FROM main.images WHERE approve="true"
```
