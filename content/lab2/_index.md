+++
title = "Lab 2: Using APM"
chapter = true
weight = 2
+++

(... and your fancy new blog)

In today’s digital world, the success of your applications often determines the success of your business. That makes Application Performance Monitoring (APM) more important than ever—you have to know exactly what your applications and systems are doing at all times. You need visibility to spot anomalies early, before your customers do. APM gives you the power to truly understand your digital business.

# Step 1: Configure Wordpress
Now is the time to navigate to the output URL from lab 1. At the output URL, you create your Wordpress username and password. We will destroy this instance after the tutorial so do not worry too much about what you set these details to
![](images/lab2/lab2-1.png)

You should also visit the homepage of your new blog and generate some browser traffic.
![](/images/3jHHVwo.png)

# Step 2: Exploring APM
On your New Relic interface you should see your blog application has appeared. If you gave a custom name, you will see New Relic has picked that up. If you have not, it will appear as My Application which is a catch all for any agents with the default agent names.
![](images/lab2/lab2-2.png)

You can see the traffic from the moment you started to configure Wordpress on your instance.
![](images/lab2/lab2-3.png)

In the web transactions view, you can compare the performance over time.
![](images/lab2/lab2-4.png)

New Relic automatically captures the call the application makes into the database. Without having to install anything, or opening up access to the database itself. This is captured at the driver, and in this example, the Wordpress PHP application uses mysqli.
![](images/lab2/lab2-5.png)

External services are automatically captured. External services were used by Wordpress at the installation time to gather latest packages, for extensions, and is used regularly in checking for updates and third party extensions.
![](images/lab2/lab2-6.png)

Not only do we have everything above, but New Relic automatically identifies Wordpress as an application and delivers a set of specific screens for Wordpress out of the box, such as the functionality of themes extensions, and hooks - which can all have a considerable performance impact on a Wordpress deployment.
![](images/lab2/lab2-7.png)

Locate your agents configuration files on your lab instance using the cat command on the following files:

```
/etc/newrelic-infra.yml
/etc/php.d/newrelic.ini
```