+++
title = "Lab 6: REST API"
chapter = true
weight = 6
+++

APIs are essential in the connected modern world we now live in. New Relic has an extensive set of APIs to automate your business around New Relic including not only sending and updating items in New Relic, but taking the data out of New Relic and applying it in your business.

New Relic offers a variety of APIs that allow you to:
- Retrieve data from the New Relic platform
- Insert data into the platform
- Adjust settings

In this lab we will use the New Relic REST API to create a new synthetic test in New Relic, setup a new alert, and to send a deployment marker event on the Lab 4 instance. For this lab, it is recommended to use [Postman](https://www.getpostman.com/) You could alternatively use cURL or the API Explorer.

# Step 1: Using the API Explorer
The New Relic [API Explorer](https://rpm.newrelic.com/api/explore) provides interactive access to the API, including the example calls, responses, and possibilities. Explore further [on the docs](https://docs.newrelic.com/docs/features/getting-started-with-new-relics-api-explorer).

# Step 2: Authenticating against the API
API calls will be using Postman variables displayed as such `{{variableName}}`. Learn more about that here. You will also need an API key, learn more about that [here](https://docs.newrelic.com/docs/apis/getting-started/intro-apis/understand-new-relic-api-keys).

You can authenticate against the New Relic API using the header: `X-Api-Key {{apiKey}}`

# Step 3: GET a monitor
HTTP verb GET method allows the retrieval of data. To retrieve the HTTP monitors associated with our account, execute:
**Postman:**
```
https://synthetics.newrelic.com/synthetics/api/v3/monitors
Host: synthetics.newrelic.com
X-Api-Key: {{apiKey}}
```

** cURL:**
```
GET /synthetics/api/v3/monitors HTTP/1.1
Header: X-Api-Key {{apiKey}}
```

# Step 4: POST a monitor
HTTP verb POST is used to submit an entity. To create a HTTP monitor, you can execute:

**Postman:**
```
https://synthetics.newrelic.com/synthetics/api/v3/monitors
Header:
X-Api-Key {{apiKey}}
Content-Type application/json
Body:
{
 "name": "Gary Homepage",
 "type": "SIMPLE",
 "frequency": 30,
 "uri": "https://my-lab-4-hostname.com:8080/wordpress/",
 "locations": ["AWS_US_WEST_1","AWS_EU_WEST_2"],
 "status": "ENABLED",
 "slaThreshold": "1.0",
 "options": {
    "validationString": "New Relic",
    "verifySSL": false,
    "treatRedirectAsFailure": false
    }
}
```

**cURL:**
```
POST /synthetics/api/v3/monitors HTTP/1.1
Host: synthetics.newrelic.com
X-Api-Key: {{apiKey}}
Content-Type: application/json
{
 "name": "Gary Homepage",
 "type": "SIMPLE",
 "frequency": 30,
 "uri": "https://my-lab-4-hostname.com:8080/wordpress/",
 "locations": ["AWS_US_WEST_1","AWS_EU_WEST_2"],
 "status": "ENABLED",
 "slaThreshold": "1.0",
 "options": {
    "validationString": "New Relic",
    "verifySSL": false,
    "treatRedirectAsFailure": false
    }
}
```
The payload sent to the New Relic API is in JSON format. You can see the various options to configure the test. Not only can we provide the basics such as a name, type, frequency and locations, we can also send validation criteria and select options such as redirect and SSL handling.

# Step 5: Setting a deployment marker
You can use [New Relic Deployment Markers](https://docs.newrelic.com/docs/apm/new-relic-apm/maintenance/record-deployments) to inform New Relic of a deployment having taken place in your environment. This can be used for before/after reporting of events, and to keep everybody informed of changes in the environment. There are different mechanisms for sending a deployment marker, and this lab will focus on using the [Java agents Command Line Interface](https://docs.newrelic.com/docs/agents/java-agent/instrumentation/monitor-deployments-java-agent)
1. SSH into your Lab 4 instance
2. Run the command <br /> `echo "Change #1234 deployed" | java -jar /usr/share/tomcat8/bin/newrelic/newrelic.jar deployment DEPLOYMENT_OPTIONS --user=email@example.co`
3. Test further deployments with the [different command line options](https://docs.newrelic.com/docs/agents/java-agent/instrumentation/monitor-deployments-java-agent#Manual) to see how they appear in New Relic
4. Observe the changes in your APM screen. Can you find the comparison report? Can you see use cases for this?

# Step 6: Retrieving data from key transactions
Find the key transactions -> list option in the API Explorer [here](https://rpm.newrelic.com/api/explore/key_transactions/list). This paginated list view can show the alerting status for key transactions against their own Apdex threshold value. You can also use the show call to retrieve the value for an individual key transaction.