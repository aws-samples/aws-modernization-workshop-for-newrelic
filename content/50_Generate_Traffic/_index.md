+++
title = "Generate Traffic"
chapter = false
weight = 50
+++

## Add an item to your cart and checkout

Now that we have instrumented our Lambda functions, let's confirm that instrumentation by generating some traffic on our Bookstore website.  Locate the URL of your Bookstore website by accessing CloudFormation (search for CloudFormation in the search bar) via the AWS Console and clicking on the **MyBookstore** CloudFormation stack:

![MyBookstore Stack](/images/generate_traffic/mybookstore-stack.png)

You can then find the CloudFront link to your website by clicking on the **Outputs** tab:

![CloudFront URL](/images/generate_traffic/cloudfront-url.png)

If you haven't done so already, sign up for an account by clicking the **Sign up to explore the demo** button.  Please provide a valid email address as a required confirmation code will be sent. 

Once logged in, Click on the **Add to cart** button under any book(s) listed on the homepage, and complete the checkout process:

![Checkout](/images/generate_traffic/add-to-cart.png)

Once you have completed the checkout process, click on **View receipt** to verify that it was processed correctly.

{{% notice note %}}
Leave this tab open for the remainder of the Workshop.
{{% /notice %}}
