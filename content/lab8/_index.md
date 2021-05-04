+++
title = "Lab 8: Alerting"
chapter = true
weight = 8
+++

New Relic Alerts help users keep out of war rooms by finding issues faster, with less noise. Alerts can be raised from all components making up the New Relic platform, including using NRQL.

# Step 1: Creating an alert
In order for alerts to be sent to a destination, there should be an alerting notification channel. Your first task is to create a notification channel. For this lab, you can just use email from the channel types.

# Step 2: Creating the alert criteria
First step is to create an alerting condition. For this, we would like to know when the `:8080/examples/async/stockticker` is performing slowly (which runs on our Lab 4 instance). Because the stock transaction is marked as a key transaction this means it can have its own configurable performance thresholds. As such the product owning this entity is APM and the stock transaction is a Key transaction metric. After selecting this you will be presented with the list of key transactions - select /stock (or if you renamed it, select its appropriate name in your environment).

# Step 3: Alert configuration
You are now presented with the configuration for alerting on the stock transaction. For these testing purposes, let us make a sensitive threshold. Let us have a critical alert when response time is above 25 seconds on average at least once in 5 minutes. If you have driven some traffic through the stock transaction in the last few hours you will notice a chart is presented and updated automatically - this gives you a visual guide to how the alert looks compared to what is in NRDB. Then select Create condition.
![](/images/q9wQ2Gv.png)
You should then add a notification channel to the alert policy. You do not need to have a notification channel to an alert policy - because perhaps you just wish to visually see the alert in New Relic Alerts. You can do this by selecting Add notification channels and either selecting the email channel for yourself that you created earlier, or, selecting users, and selecting yourself from the list of users.

# Step 4: Alerting on food allergy alerts
## A continuation of Lab 3

In Lab 3, you created a synthetics test to check for new food alerts from the food agency website. The task now is to alert on new food alerts as they are noticed in New Relic.