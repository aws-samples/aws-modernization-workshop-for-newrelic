+++
title = "Lab 5: Advanced APM"
chapter = true
weight = 5
+++

Advanced APM builds extends lab 2, taking what was out of the box to the next level by creating some custom attributes to our Wordpress blog, adding some custom instrumentation to our Tomcat application, tracking key transactions and building a New Relic Insights dashboard to give the business a tailored view into users logging in.

# Step 1: Custom Attributes
While you can send data to the Event API. There also exists the possibility to [send custom attributes](https://docs.newrelic.com/docs/insights/insights-data-sources/custom-data/add-custom-attributes-new-relic-apm-data) to decorate events with further useful information, such as attaching usernames to failing requests to identify users having a particularly bad experience.

In this lab we will be using the Wordpress blog from the first lab to attach usernames to the Events sent to Insights. Later, we will discover the Tomcat 8 demo apps you deployed in lab 4 and instrument extra classes from the APM UI.

# Step 2: Access your Wordpress instance
First of all, SSH into your Wordpress EC2 instance:
```
ssh -i privateKey.pk ec2-user@host-name.compute.amazonaws.com
```
Keeping in mind the path should be set to where your copy of privateKey.pk is saved

# Step 3: Attaching custom attributes
Since Wordpress is open source, we can discover the source code ourselves and find what data might be valuable to expose. To simplify this you can view the function to get the current user info in the [Wordpress Codex](https://codex.wordpress.org/Function_Reference/get_currentuserinfo#Examples). For your own applications you should consult documentation or developers if you have access to them.

We will be using [PHP Custom Attributes](https://docs.newrelic.com/docs/agents/manage-apm-agents/agent-data/collect-custom-attributes#php-att) that we can add to our Wordpress application.

As above, we can add the New Relic method call in the template file (which is what the user loads). Our example Wordpress instance is using the Twenty Seventeen template. In your ec2 instance change directory to:
```
cd /var/www/html/wordpress/wp-content/themes/twentyseventeen/
```

There we will edit the file index.php. You can do this by performing
```
sudo vi /var/www/html/wordpress/wp-content/themes/twentyseventeen/index.php
```

Beneath `get_header();` add:
```
<?php 
    newrelic_add_custom_parameter ('userDisplayName', $current_user->display_name);
    newrelic_add_custom_parameter ('userEmail', $current_user->user_email); 
?>
```
Pro tip: press `i` to insert in vim. Once you have added those two lines beneath `get_header()`, hit `esc` then `:wq` to write and quit the file.

Now navigate back to your blog (make sure you are logged in), refresh the homepage. Check New Relic Data Explorer for your event and you should see an event with your username and email address associated with the Event in the Data Explorer.
![](/images/uYBn9vb.png)

It would be a good idea to display the visits in a table
![](/images/sWrumZq.png)

## NRQL:
```sql
FROM Transaction SELECT userDisplayName, userEmail WHERE userDisplayName IS NOT NULL SINCE 1 week ago
```
![](/images/VcyoSwW.png)

## NRQL
```sql
FROM Transaction SELECT average(duration) WHERE userDisplayName IS NOT NULL SINCE 1 week ago FACET userEmail
```
![](/images/jd8fH0s.png)

## NRQL
```sql
FROM Transaction SELECT count(userEmail) WHERE userDisplayName IS NOT NULL SINCE 1 week ago FACET userEmail
```
Here we can see user sessions. We could even have used uniqueCount to find unique user visits in a specific time period.

# Step 4: Access your Lab 4 instance
First of all, SSH into your Lab 4 EC2 instance:

```
ssh -i privateKey.pk ec2-user@host-name.compute.amazonaws.com
```
keeping in mind the path should be set to where your copy of `privateKey.pk` is saved

# Step 5: Tomcat 8 demo app
For this lab, we will be using the Tomcat 8 demo application that was deployed as part of the Lab 4. First up, check that the Tomcat 8 demo application is running by visiting the hostname of that instance in the web, on port `:8080`

If it working, drive some traffic to the example applications on `:8080/examples/jsp/`

In case Tomcat is not running, check the status on your lab instance executing `systemctl status tomcat8` and if it is not, run `systemctl start tomcat8`. If this has not helped, please get in touch with your New Relic technical contact.

Now that your demo app is up and running, let us drive some traffic to two specific demo URLs:
- `:8080/examples/servlets/servlet/RequestParamExample`
- `:8080/examples/async/stockticker`

Now navigate to New Relic APM and open your demo application. It should by default appear in "My Application".

Navigate to transactions and select the stock transaction.
![](/images/BQSbslg.png)
Tasks: (will be used in the self assessment sheet)
1. Track /stock as a key transaction
2. What percentage of time was spent in Async Processing for /stock?
3. How long did the /stock transaction take end to end?
4. Inside the /stock trace, what was the first method?

# Step 6: Adding custom instrumentation
New Relic will instrument automatically in supported frameworks. Though sometimes application developers build custom pieces of logic above this which may not always be captured, or may simply be not instrumented and deemed "not interesting enough" when the agent comes to make an instrumentation decision, as is the standard with all similar production level profilers. To address this, [custom instrumentation](https://docs.newrelic.com/docs/agents/manage-apm-agents/agent-data/custom-instrumentation) can be used to extend the methods captured in tracing and be automatically applied. Thereafter, in future transactions, the agent will capture this method if it is running when it comes to making an instrumentation decision.

For this lab we will start a profiling session for 3 minutes and in that time, navigate to `:8080/examples/async/stockticker`, for there is a method we will instrument there.

Navigate to New Relic APM and select My Application, and select Thread Profiler
![](/images/PwPlVcC.png)

While that is running, in another browser tab, navigate to :8080/examples/async/stockticker. It is important that there is some activity happening on our JVM generated by the user, otherwise we will only see native methods on the JVM (such as GC activity).

After the trace is completed, it will appear like below:
![](/images/e6DnOdR.png)

Now view the results of the profiler session, such as below:
![](/images/nqesuYl.png)

In your profile session, you also should notice async.Stockticker.run() that is listed as uninstrumented, like above. Select this item and from the options select Instrument async.Stockticker.run(). Then select to save/apply this instrumentation.
![](/images/zr8bOWW.png)

This will take you to the custom instrumentation tab. From here you can enter manual instrumentation (in the case you already know the methods you wish to instrument). Where you can also export/import custom instrumentation sets. Where you also have the ability to explicitly remove instrumentation. In our case, we just wish to deploy the instrumentation for Stockticker so press Deploy instrumentation changes.
![](/images/HzVC3wY.png)

This will bring you a warning about a brief period of overhead. This is because when you deploy the custom instrumentation, a transformation on the classes containing the selected methods happens. This retransformation happens after the deploy instruction is sent, and threads involved with these classes are briefly paused while the transformation takes place. You should always test custom instrumentation in a non production environment before this takes place in a production environment.

Since this is just our demo application, it is safe for us to go ahead and apply the instrumentation.
![](/images/indmTdt.png)

After the transformation has taken place, observe that the success (or failure) of this is presented.
![](/images/HpAk8Us.png)

After the instrumentation is successful, navigate in another tab to `:8080/examples/async/stockticker` so we can view the results of our custom instrumentation. You will notice not a whole lot has changed there really. But now this method will always be factored into the instrumentation decision whether it performs quickly or slowly.

# Troubleshooting
## Tomcat 8 not started
Check the status of Tomcat running `sudo systemctl status tomcat8`

## Tomcat 8 not starting or App not appearing in New Relic
Check the status of the JAVA OPTS in `/usr/share/tomcat8/bin/catalina.sh` specifically for if the license input has any typo