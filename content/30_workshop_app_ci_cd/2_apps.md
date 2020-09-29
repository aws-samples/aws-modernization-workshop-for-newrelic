+++
title = "APM Agent Deployment"
chapter = false
weight = 3
+++

With New Relic's Java agent, you can track everything from performance issues to tiny errors within your code. New Relic's Java agent monitors your Java app and provides visibility into the behavior of your JVM. After installing, you will be able to quickly monitor transactions, dive deep into errors, and more.

There are two required files for installation:

* ***newrelic.jar***: This contains the agent class files.</li>

* ***newrelic.yml***: This contains configuration information for the New Relic agent, including your license key, application name, etc.

First, push the application to the CodeCommit repo.  The following sets a new origin for the application repo to CodeCommit modernization-workshop, configures a credential helper needed for CodeCommit, and pushes the source code to the repo.  This step is necessary for an automated pipeline as CodeBuild will build the application directly from this repo.

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

```text
Counting objects: 9525, done.
Compressing objects: 100% (5900/5900), done.
Writing objects: 100% (9525/9525), 33.75 MiB | 2.65 MiB/s, done.
Total 9525 (delta 3240), reused 9525 (delta 3240)
remote: processing 
To https://git-codecommit.us-west-2.amazonaws.com/v1/repos/modernization-workshop
 * [new branch]      master -> master
```

Next, download the ***newrelic.jar*** file:

```bash
cd ~/environment/modernization-workshop/java-app
wget https://download.newrelic.com/newrelic/java-agent/newrelic-agent/current/newrelic.jar
```

Then, download the ***newrelic.yml*** configuration file:

```bash
wget https://bit.ly/31P1Wkn -O newrelic.yml
```

You will need to modify the configuration file to include your New Relic license key.  

To obtain your license key, login to http://one.newrelic.com, click on your name in the top right-hand corner of the page, and then click on **Account Settings**:

![Settings](/images/newrelic-login.png)

You should see your license key near the top of the Account Settings page on the right hand side:

![License Key](/images/newrelic-license-key.png)

You are now ready to edit the New Relic configuration file using the built-in Cloud9 editor. First, click on **Open** from the **File** menu:

![Open File](/images/cloud9-open-file.png)

Then, click on ***newrelic.yml***:

{{% notice info %}}
You may see what appears to be two copies of this and other files in your Cloud 9 environment as ***app*** is a symlink to the ***java-app*** directory.  They are the same files.
{{% /notice %}}

![Open Config File](/images/cloud9-open-newrelic-config.png)

This will open the ***newrelic.yml*** configuration file as seen below:

![Config File](/images/cloud9-newrelic-config.png)

Simply replace the text *<%= license_key %>* that is located between the single quotes with your actual license key and save the file:

![Save File](/images/cloud9-save-file.png)

Next, you will need to add the following two lines to the ***Dockerfile*** to include the New Relic APM agent in future application builds:

```text
COPY ./newrelic.jar /opt/jboss/newrelic.jar
COPY ./newrelic.yml /opt/jboss/newrelic.yml
```

Add these two lines at the bottom of the *configuration* section as shown below and save the file:

![Dockerile](/images/cloud9-dockerfile.png)

Lastly, you will need to add the following line **before** the final line in ***docker-entrypoint.sh*** to ensure the New Relic APM agent is launched alongside the application:

```text
JBOSS_LOG_MANAGER_LIB="$(echo $JBOSS_HOME/modules/system/layers/base/org/jboss/logmanager/main/jboss-logmanager-*.jar)" # resolve logmanager jar
export JAVA_OPTS=" -Xbootclasspath/p:$JBOSS_LOG_MANAGER_LIB -server -Xms64m -Xmx512m -XX:MetaspaceSize=96M -XX:MaxMetaspaceSize=256m -Djava.net.preferIPv4Stack=true -Djboss.modules.system.pkgs=org.jboss.byteman,org.jboss.logmanager -Djava.util.logging.manager=org.jboss.logmanager.LogManager -Djava.awt.headless=true -javaagent:/opt/jboss/newrelic.jar"
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
