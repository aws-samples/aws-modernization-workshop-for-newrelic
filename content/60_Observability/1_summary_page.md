+++
title = "New Relic Serverless for AWS Lambda: Summary Page"
chapter = false
weight = 1
+++

{{% notice note %}}
Please note that it can take up to 5 minutes after *initially* generating traffic on your application before any data can be seen in New Relic.
{{% /notice %}}

New Relic One's Summary page for AWS Lambda presents a quick view into your function's most important performance data.

Let's give it a look by navigating to the [New Relic One website](http://one.newrelic.com) and hovering on **Explorer** near the top left hand side of the screen.  This should reveal a search bar; in the search bar, type in **GetCartItem**.  This should present you with one of the Lambda functions we instrumented earlier in this workshop.  Click on **mybookstore-GetCartItem** to access the Summary page for this function:


![New Relic One](/images/observability/entity-explorer.png)

On the Summary page, you will notice a collection of charts Lambda function. A description of each of these charts is shown below:  

![Summary Page](/images/observability/summary-page.png)

1. **Duration**: The total time a function ran.
1. **Invocations**: The total amount of times a function has been run. This includes direct REST API calls through the AWS API Gateway as well as through chained event requests.
1. **Error rate**: The percentage of invocations that have resulted in errors.
1. **Cold starts**: The number of times that a function was invoked that resulted in a cold start. (If a container hosting a function is not created before the function is invoked—a cold start—the function may seem excessively slow).
1. **Max Memory Used**: The maximum amount of memory used by your Lambda function at a given time.
1. **Metadata**: A list of metadata describing the function.
