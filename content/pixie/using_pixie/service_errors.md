---
title: "Diagnosing Issues - Scenario #1"
date: 2021-5-01T09:00:00-00:00
weight: 22
draft: false
---

![TonyHatMe](/images/pixie/tinyhat.png)

Your TinyHat.me API has already taken off and users are actively uploading images.  Unfortunately, there appears to be a bug in the code!

![Bug1](/images/pixie/bug_1.png)

Let's see if we can use Pixie to figure out why image uploads are bypassing our moderation queue.

First, let's upload a picture ourselves.  You can use a picture of any indivudal you like and upload it to your Cloud9 environment via the **File** menu as follows:

![UploadCloud9](/images/pixie/upload_cloud9.png)

Now, let's upload image using the TinyHat.me API making sure to replace ***name_of_image.jpg*** with the actual name of the image file you uploaded to your Cloud9 environment:

```bash
curl --location --request POST 'http://a01b26901e64c48d1a98bb18f811d60b-1730600791.us-west-2.elb.amazonaws.com/add' \
> --form 'name="Tiny Hats are cool"' \
> --form 'image=@"/home/ec2-user/environment/name_of_image.jpg"'
```

If successfull, you should see a response similar to this:

![UploadSuccess](/images/pixie/upload_success.png)

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
