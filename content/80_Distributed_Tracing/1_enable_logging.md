+++
title = "Enable Logging"
chapter = false
weight = 1
+++

{{% notice note %}}
In a real-world environment, enabling the streaming of Lambda logs can increase both your AWS and New Relic bills.
{{% /notice %}}

Sometimes the errors page alone may not provide enough information for sufficient or quick analysis.  New Relic Logs allows you to dig deeper into issues.

Let's hop back into the AWS Console and stream one of our Lambda function's log groups to New Relic.  First, open up CloudWatch in the AWS Console by searching for it in the search bar at the top of your screen.  Then, click on the **Log groups** link in the navigation menu on the left hand side of your screen located in the *Logs* section:

![Log Groups](/images/distributed_tracing/log-groups.png)

Select the ***mybookstore-GetCartItem*** function, click the arrow next to the ***Actions*** dropdown menu, and select **Create Lambda subscription filter**:

![Create Filter](/images/distributed_tracing/create-filter.png)

Next, in the *Lambda function* dropdown dialog box, select the ***newrelic-log-ingestion*** function (this was deployed automatically for you earlier in the workshop when you linked your AWS account to New Relic):

![New Relic Lambda](/images/distributed_tracing/newrelic-lambda.png)

Then, select ***JSON*** from the *Log format* dialog dropdown box:

![Format Selection](/images/distributed_tracing/json-format.png)

Provide a name for your filter (*New Relic Log Ingestion* is used in the screenshot below):

![Filter Name](/images/distributed_tracing/filter-name.png)

Finally, scroll down and click the orange **Start streaming** button:

![Start Streaming](/images/distributed_tracing/start-streaming.png)
