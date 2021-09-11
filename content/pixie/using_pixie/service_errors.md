---
title: "Diagnosing Issues - Scenario #1"
date: 2021-5-01T09:00:00-00:00
weight: 22
draft: false
---

![image](https://user-images.githubusercontent.com/69332964/132958577-a69d7b4a-0e8c-4e5a-b02a-87e233f3fd6b.png)

### What's the problem?
Your TinyHat.me API has already taken off and users are actively requesting & uploading their own hats.  Unfortunately, there appears to be a bug in the code!

![bug1](https://user-images.githubusercontent.com/69332964/132958482-736d60fb-26ef-4ad6-abcc-e542dda5d33f.png)

*We are receiving complaints from our users that unmoderated hats are showing up on the website!* In order for a hat to appear on the frontend, admins of TinyHat.Me must approve them first. However, that's not what's happening...

Let's see if we can use Pixie to figure out why image uploads are bypassing our moderation queue.

### Replicating the Issue
First, let's upload a hat ourselves to see the bug in action.  You can use any picture you like and upload it to your Cloud9 environment via the **File** menu as follows:

![UploadCloud9](/images/pixie/upload_cloud9.png)

Now, let's upload image using the TinyHat.me API making sure to replace ***your_api_gateway*** with the actual address of your gateway service and  ***name_of_image.jpg*** with the actual name of the image file you uploaded to your Cloud9 environment.

To obtain your API gateway address, execute `kubectl get services` in your terminal and copy paste the URL corresponding to `gateway-service`.
![gateway](https://user-images.githubusercontent.com/69332964/132958710-42ea09d0-a46a-44a0-b13a-0ff85d2ca175.png)


```bash
curl --location --request POST 'http://your_api_gateway/add' \
> --form 'name="Tiny Hats are cool"' \
> --form 'image=@"/home/ec2-user/environment/name_of_image.jpg"'
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
Let's hop back over to Pixie and see if we can figure out what's going on.  To get a high level view of what the application is doing, run the `px/cluster` script:

![ClusterView](/images/pixie/tinyhats_cluster.png)

You can see that the `gateway` service is succesfully communicating with `add-service`.  `add-service`, in turn, is  properly calling both `email-service` and `upload_service`. We will need to dig a bit further...

Let's confirm that the cluster view is correct and that all of our services are correctly communicating with one another, starting with `add_service`. Run the `px/http_data_filtered` script:

![FilteredData](/images/pixie/http_data.png)

Then, select the `add-service` pod:

![AddService](/images/pixie/add-service.png)

You should see output similar to the image below:

![AddServiceSuccess](/images/pixie/add-service-success.png)

As we can see by the **RESP_STATUS** of *200*, this service appears to be functioning properly. Repeat the same steps above, but this time for `email-service`:

![EmailServiceSuccess](/images/pixie/email-service-success.png)

As with `add-service`, you can see that `email-service` is also functioning properly.  Lastly, let's look at `upload-service`:

![UploadServiceSuccess](/images/pixie/upload-service-success.png)

`upload-service` too appears to be functioning properly.  We're going to have to dig even deeper.  Let's take a look at the backend by executing `px/mysql_data` and clicking on the SQL query of first result we receive:

![MysqlData](/images/pixie/mysql_data.png)

Having clicked on the SQL query, we can now see the complete JSON respons of this particular transaction, which includes the full SQL query:

![MysqlQuery](/images/pixie/sql_query.png)

We have found our culprit!  The SQL query here is selecting ALL images from the database without specifying a WHERE clause to determine whether or not the image was actually ever approved.
