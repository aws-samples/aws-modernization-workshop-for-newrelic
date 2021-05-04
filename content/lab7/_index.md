+++
title = "Lab 7: On Host Integrations"
chapter = true
weight = 7
+++

So far, we have had the pleasure of automation. This is the recommended experience for New Relic customers too. However, as an engineer completing New Relic tasks in the field it is important you have some experience of how to install a New Relic agent.

For this lab, you will install open source nginx on the Lab 4 instance and configure the New Relic plugin. This will serve a reverse proxy to the Tomcat 8 service. In the second part of this lab you will install and configure the New Relic Python agent.

# Step 1: Creating the nginx reverse proxy
On your Lab 4 instance install nginx as such: `sudo apt install nginx`

Then perform the following two commands to enable nginx, and to ensure it turns on automatically.
```
sudo systemctl start nginx
sudo systemctl enable nginx
```

Now to create the configuration file: `cd /etc/nginx/conf.d/`

Then use vim to create tomcat.conf: `sudo vi tomcat.conf`

And in the editor mode (press **i**):
```json
server {
	listen 80;
	listen[::]: 80;
	server_name INSTANCE_PUBLIC_IP;
	location / {
		proxy_pass http: //localhost:8080/;
	}
}
```
Make sure you replace INSTANCE_PUBLIC_IP with the public IP of your Lab 4 instance. The rest remains the same. Then once finished hit escape and type `:wq` to write and quit.

Now test the nginx configuration: `sudo nginx -t`

And if successful, reload the new configuration: `sudo nginx -s reload`

Now you can visit your Lab 4 instance at the public IP and this should display your Tomcat 8 service.

# Step 2: Enabling the New Relic OHI
Now that we have nginx installed and active, you should enable the On Host Integration where you can find instructions [here](https://docs.newrelic.com/docs/integrations/host-integrations/host-integrations-list/nginx-monitoring-integration).

# Pro tips
There is some quirk to this lab, and we would like you to discover what has to be done. You have been able to modify an nginx file as above and have a link to the documentation so you should be all set.

Another piece of advice, you do **not** need to rebuild nginx.

All going well, you should have an OHI appear as such:
![](/images/hAppO8O.png)

