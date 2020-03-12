+++
title = "Exploring New Relic APM"
chapter = false
weight = 2
+++

To access your applications that are monitored by New Relic APM, please visit:  https://rpm.newrelic.com/

You should see a screen similar to the one below with the application we have deployed listed, which is named <b>New Relic AWS Workshop</b>.

![APM Apps](/images/apm-applications.png)

Clicking on the <b>New Relic AWS Workshop</b> link will take you to the APM Overview page for your application that contains charts such as Transactions, Throughput, and Error rate:

![APM Overview](/images/apm-overview.png)

In the <b>Service maps</b> view, you can see your application and any external dependencies it has.  In this case, you should see a Postgres database listed:

![Service Maps](/images/apm-service-maps.png)

In the <b>Databases</b> view, you can see that New Relic automatically captures all calls made to the database without any configuration required:

![Databases](/images/apm-databases.png)

APM data is also available on the New Relic One platform which allows you to collect data from any source and extend observability across your digital landscape.  To access your application data in New Relic One, simply navigate to http://one.newrelic.com and click on <b>Entity explorer</b>:

![Entity Explorer](/images/nr1-entity-explorer.png)
 
If you click on the name of your application, <b>New Relic AWS Workshop</b>, you will see an overview page similar to the one you previously viewed in APM:

![NR1 APM](/images/nr1-apm.png)

New Relic One gathers all of your APM, Infrastructure, Browser, and Synthetics data in a single place.  If you'd like to learn more, links to all New Relic products are provided in the Conclusion section of this workshop.
