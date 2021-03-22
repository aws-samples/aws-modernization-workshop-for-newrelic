+++
title = "Delete DynamoDB Table"
chapter = false
weight = 1
+++

Amazon DynamoDB is a key-value and document database that delivers single-digit millisecond performance at any scale. It is one of the more common AWS services coupled with AWS Lambda to produce web application backend services.

To simulate the inability of our Bookstore application to access DynamoDB, let's delete a table used by the application's checkout process.

First, open DynamoDB in the AWS Console by searching for it via the search bar at the top of the screen.  Then, select the ***mybookstore-Cart*** table and click the **Delete table** button.
 
{{% notice note %}}
Make sure you tick the checkbox for **Create a backup before deleting this table**.
{{% /notice %}}

Upon clicking the **Delete table** button, you will be presented with the following dialog for deletion confirmation:

![New Relic One](/images/troubleshooting/delete-table.png)

Once the table has shown as being deleted, attempt to repeat the previous section in this workshop named **Generate Traffic**. (Make sure you leave the New Relic One interface tab open in your browser) You should note that, unlike last time:

 * You are unable to add any books to your cart
 * When you click on the cart icon on the top right hand side of your screen, you receive a network error
