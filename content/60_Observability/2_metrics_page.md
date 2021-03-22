+++
title = "New Relic Serverless for AWS Lambda: Metrics Page"
chapter = false
weight = 2
+++

Now, let's take a look at the Metrics page by clicking on **CloudWatch metrics** located in the navigation menu on the left side of the screen in the section labeled *MONITOR*:

![CloudWatch metrics](/images/observability/cloudwatch-metrics.png)

The **Metrics** page displays AWS Lambda data collected from CloudWatch:

![Metrics Page](/images/observability/metrics-page.png)

1. **Invocations**: The total amount of times a function has been run. This includes direct REST API calls through AWS API Gateway, as well as through chained event requests.
1. **Duration**: The total time a function ran.
1. **Iterator age**: Emitted for stream-based invocations—functions triggered by an Amazon DynamoDB stream or Amazon Kinesis stream—this measures the age of the last record for each batch of records processed. Age is calculated as the difference between the time AWS Lambda received the batch and the time the last record in the batch was written to the stream.
1. **Concurrent executions**: Measures the sum of concurrent executions for a function at a given point in time for that specific function.
1. **Errors**: The number of times an invocation has resulted in an error.
1. **Throttles**: The number of times a function has been throttled after hitting the service-defined limit for concurrent executions in an AWS account.
1. **Dead letter errors**: The number of times a function was invoked via a queue, was unable to successfully run, and was left as an unfulfilled invocation request.

{{% notice note %}}
Leave this page open as it will be used again in the next section of this workshop.
{{% /notice %}}
