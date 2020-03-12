+++
title = "New Relic One: Dashboards"
chapter = false
weight = 4
+++

New Relic One dashboards allow you to combine data from anywhere in the New Relic platform to build flexible, interactive visualizations. You can easily explore your data and correlate connected sources with tailored, user-friendly charts, and quickly learn the state of your system and applications for faster, more efficient troubleshooting.

Dashboards, along with the chart builder and the rest of the New Relic One platform, have been designed for users regardless of their experience level. Anyone can surface data and tailor the visualization with little effort.

You can create your own dashboards from scratch or import existing dashboards using the New Relic API Explorer.  In this exercise, you will be importing a dashboard containing 3 charts.

First, you will need to create an Admin User Key for your New Relic account. Navigate to http://one.newrelic.com and click on <b>Account Settings</b>:

![Account Settings](/images/newrelic-login.png)

Next, click on <b>API keys</b> under the <i>Integrations</i> section on the left hand side of the page:

![API Keys](/images/settings-apikeys.png)

Then, in the table underneath <i>Regenerate all</i> near the bottom right-hand side of the page, click on the small circular icon to generate a new key:

![API Key](/images/settings-generatekey.png)
 
You are now ready to import dashboards into your New Relic account.  Navigate to the New Relic API Explorer: https://rpm.newrelic.com/api/explore/dashboards/create.

You will need to select your newly created key from the dropdown box on the top left-hand side of the page:

![API Explorer](/images/newrelic-apiexplorer.png)

Back inside your <b>Cloud9</b> editor, copy the entire contents of the <b>dashboards.json</b>:

![Dashboard File](/images/cloud9-dashboard.png)

Back on the New Relic API Explorer page, paste the contents you just copied into the <b>dashboard</b> textbox, scroll down, and click on <b>Send Request</b>:

![Upload Dashboard](/images/upload-dashboard.png)

Your new dashboard is now ready for viewing!  Navigate to http://one.newrelic.com and click on <b>Dashboards</b>.  You should see something very similar to the following:

![Dashboard View](/images/newrelic-dashboards.png)

Click on the name of the dashboard: <b>AWS Worskhop</b>.  You should see a fully functioning dashboard displaying information about your application and the AWS resources it is consuming:

![Dashboard](/images/workshop-dashboard.png)

Feel free to experiment more with the dashboarding interface in New Relic One before moving on to the conclusion of this workshop.
 
