+++
title = "Instrument Lambda Functions"
chapter = false
weight = 40
+++

## Instrumenting AWS Lambda Functions

Now that we have linked your AWS and New Relic accounts, let's install the New Relic Lambda layer for each of the AWS Lambda functions we wish to monitor in our AWS Bookstore application.

First, let's export an environment variable containing our New Relic account id to make it easier to instrument each of our Lambda functions:

```bash
export NR_ACCOUNT_ID=YOUR_ACTUAL_NR_ACCOUNT_ID_GOES_HERE
``` 

Now, copy the commands listed below into CloudShell to instrument your Lambda functions:

```bash
newrelic-lambda layers install --nr-account-id $NR_ACCOUNT_ID --function mybookstore-ListOrdersInCart --upgrade
newrelic-lambda layers install --nr-account-id $NR_ACCOUNT_ID --function mybookstore-AddToCart --upgrade
newrelic-lambda layers install --nr-account-id $NR_ACCOUNT_ID --function mybookstore-ListOrders --upgrade
newrelic-lambda layers install --nr-account-id $NR_ACCOUNT_ID --function mybookstore-GetCartItem --upgrade
newrelic-lambda layers install --nr-account-id $NR_ACCOUNT_ID --function mybookstore-Checkout --upgrade
newrelic-lambda layers install --nr-account-id $NR_ACCOUNT_ID --function mybookstore-UpdateCart --upgrade
newrelic-lambda layers install --nr-account-id $NR_ACCOUNT_ID --function mybookstore-GetBook --upgrade
newrelic-lambda layers install --nr-account-id $NR_ACCOUNT_ID --function mybookstore-RemoveFromCart --upgrade
newrelic-lambda layers install --nr-account-id $NR_ACCOUNT_ID --function mybookstore-ListBooks --upgrade
```

All future layers versions will include the Lambda extension by default.

To list your Lambda functions and verify that they are now instrumented, you can run the following command:

```bash
newrelic-lambda functions list
```

Congratulations!  You have successfully instrumented the Lambda functions for your AWS Bookstore application!
