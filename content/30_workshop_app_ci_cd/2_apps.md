+++
title = "APM Agent Deployment"
chapter = false
weight = 3
+++

With New Relic's Java agent, you can track everything from performance issues to tiny errors within your code. New Relic's Java agent monitors your Java app and provides visibility into the behavior of your JVM. After installing, you will be able to quickly monitor transactions, dive deep into errors, and more.

There are two required files for installation:

<ul>
 <li><b><i>newrelic.jar</i></b>: This contains the agent class files.</li>
 <li><b><i>newrelic.yml</i></b>: This contains configuration information for the New Relic agent, including your license key, application name, etc.
</ul>

First, push the application to the CodeCommit repo.  The following sets a new origin for the application repo to CodeCommit unicorn-store, configures a credential helper needed for CodeCommit, and pushes the source code to the repo.  This step is necessary for an automated pipeline as CodeBuild will build the application directly from this repo.

```bash
cd ~/environment/modernization-workshop/
git remote set-url origin https://git-codecommit.us-west-2.amazonaws.com/v1/repos/modernization-workshop
git config --global credential.helper '!aws codecommit credential-helper $@'
git config --global credential.UseHttpPath true
git push origin master
```

{{% notice info %}}
If successful, you should see a similar message to the one below.
{{% /notice %}}

<pre>
Counting objects: 9525, done.
Compressing objects: 100% (5900/5900), done.
Writing objects: 100% (9525/9525), 33.75 MiB | 2.65 MiB/s, done.
Total 9525 (delta 3240), reused 9525 (delta 3240)
remote: processing 
To https://git-codecommit.us-west-2.amazonaws.com/v1/repos/modernization-workshop
 * [new branch]      master -> master
</pre>

Next, download the <b><i>newrelic.jar</i></b> file:

```bash
cd ~/environment/modernization-workshop/java-app
wget https://download.newrelic.com/newrelic/java-agent/newrelic-agent/current/newrelic.jar
```

Then, download the <b><i>newrelic.yml</i></b> configuration file:

```bash
wget https://bit.ly/31P1Wkn -O newrelic.yml
```

You will need to modify the configuration file to include your New Relic license key.  

To obtain your license key, login to http://one.newrelic.com, click on your name in the top right-hand corner of the page, and then click on <b>Account Settings</b>:

![Settings](/images/newrelic-login.png)

You are now ready to edit the New Relic configuration file using the built-in Cloud9 editor. First, click on <b>Open</b> from the <b>File</b> menu:

![Open File](/images/cloud9-open-file.png)

Then, click on <b>newrelic.yml</b>:

![Open Config File](/images/cloud9-open-newrelic-config.png)

This will open the <b><i>newrelic.yml</i></b> configuration file as seen below:

![Config File](/images/cloud9-newrelic-config.png)

Simply replace the text <i><%= license_key %></i> that is located between the single quotes with your actual license key and save the file:

![Save File](/images/cloud9-save-file.png)

Next, you will need to add the following two lines to the <b><i>Dockerfile</i></b> to include the New Relic APM agent in future application builds:

```text
COPY ./newrelic.jar /opt/jboss/newrelic.jar
COPY ./newrelic.yml /opt/jboss/newrelic.yml
```

Add these two lines at the bottom of the <i>configuration</i> section as shown below and save the file:

![Dockerile](/images/cloud9-dockerfile.png)

Lastly, you will need to add the following line <b>before</b> the final line in <b><i>docker-entrypoint.sh</i></b> to ensure the New Relic APM agent is launched alongside the application:

```text
export JAVA_OPTS="-server -Xms64m -Xmx512m -XX:MetaspaceSize=96M -XX:MaxMetaspaceSize=256m -Djava.net.preferIPv4Stack=true -Djboss.modules.system.pkgs=org.jboss.byteman -Djava.awt.headless=true -javaagent:/opt/jboss/newrelic.jar"
```

It should look like the image below:

![docker-entrypoint](/images/cloud9-docker-entrypoint.png)

You are now ready to commit your changes to have your application built to include the New Relic APM agent:

```bash
cd ~/environment/modernization-workshop/
git rm java-app --cached
rm -rf .git/modules/java-app/
git add java-app
git commit -m "Added New Relic APM agent"
git push origin master
```
