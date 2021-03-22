+++
title = "Edit Lambda Code"
chapter = false
weight = 2
+++

It's hard to believe, but sometimes code changes that fix one problem often cause another.  Let's modify one of our Lambda functions and intentionally introduce an error.

Navigate to Lambda within the AWS Console by searching for it in the search bar at the top of your AWS Console screen. Then, click on the **mybookstore-GetCartItem** function link:

![Lambda Link](/images/distributed_tracing/lambda-link.png)

Scroll down to the section labeled *Function code* and double-click file named **index.js** to open it:

![Function Code](/images/distributed_tracing/function-code.png)

Rename the constant *dynamoDB* to *dynamo_DB*:

![Rename Constant](/images/distributed_tracing/rename-constant.png)

Click the orange **Deploy** button located at the top right hand side of the *Function code* section of the page:

![Deploy Function](/images/distributed_tracing/deploy-function.png)

Once again, repeat the **Generate Traffic** section of this workshop. (Make sure you reload the homepage of your Bookstore application before generating new traffic)
