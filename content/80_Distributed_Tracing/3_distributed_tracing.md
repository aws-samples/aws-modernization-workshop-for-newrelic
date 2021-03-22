+++
title = "New Relic Serverless for AWS Lambda: Logs"
chapter = false
weight = 3
+++

Now that we have intentionally instituted an error in our code and generated some traffic on our Bookstore application since implementing that error, let's take a look at how the New Relic Logs feature can help us diagnose problems like this that might occur in the real world.

Hop on over to the logs page by clicking on the **Logs** link located in the navigation menu on the left side of the screen in the section labeled *MORE VIEWS* (You may have to expand this section by clicking on the *MORE VIEWS* text):

![Distributed Tracing](/images/distributed_tracing/distributed-tracing.png)

Since New Relic Logs automatically highlights errors in log entries, you should be able to quickly scan to see where the error occured:

![Latest Trace](/images/distributed_tracing/latest-trace.png)

Clicking on the line, we can see a detailed view of the log entry that quickly shows the exact line in the file the error occured on:

![Log Error](/images/distributed_tracing/log-error.png)

Keep in mind that in addition to the curated New Relic One interfaces you have seen displayed in this workshop that allow you to analyze and solve issues with your serverless applications, ***New Relic Alerts*** can also be easily created for a more proactive approach to surfacing issues immediately when they occur. Links to additional self-paced workshops covering Alerts and many other features of the New Relic One platform can be found in the Conclusion section of this workshop. 
