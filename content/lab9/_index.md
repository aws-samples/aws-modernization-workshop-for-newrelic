+++
title = "Lab 9: Pro Features Lab"
chapter = true
weight = 9
+++

The professional features lab will walk through enabling features that come with a professional level subscription in New Relic, and advanced use cases supporting them - such as alternative methods for injecting the Browser JavaScript.

# Using New Relic Browser
Browser was activated automatically as a module in PHP, you can view the calls sent to New Relic APM in the network view in your browser. The results of which can be seen in New Relic Browser.
![](/images/LP1SUrd.png)

Be sure to activate the session tracing capability so that New Relic captures traces for the browsers page construction including the DOM, and browser timings
![](/images/37J6GaW.png)

1. Scroll through and find when did the first meaningful paint of the browser window take place?
2. When did the DomContentLoaded event take place this time? (Hint: look for the yellow section)
3. When did the Load event take place this time? (Hint: look for the red section)

# Understanding automatic Browser injection by New Relic APM
![](/images/oLbHNSN.png)

Navigate to hostname :8080/examples/jsp/jsp2/el/basic-arithmetic.jsp and open the network view, and press refresh.
![](/images/2t9Fz3b.png)

# When you can't inject
There might be some scenarios when you are unable to inject the snippet automatically. For that you can use the code snippet.

**Injecting the code snippet**, dynamically Please note, this is fully unsupported and should be entirely tested in production.

1. Upload your New Relic code snippet (removing the `<script>` tags) to an S3 bucket with public accessibility.
   
2. Add the following line beneath the proxy_pass directive in /etc/nginx/tomcat.conf:
```
sub_filter </head> '<script src="//YOUR-SNIPPET.js"></script></head>';
```
This replaces every instance that `</head>` is seen with our New Relic JavaScript snippet. It is really important to remember to add `</head>` back into the replacement string.

3. Test the configuration: `nginx -t`
4. Apply the configuration: `nginx -s reload`s