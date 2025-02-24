# Overview of the Performance Insights dashboard<a name="USER_PerfInsights.UsingDashboard.Components"></a>

The dashboard is the easiest way to interact with Performance Insights\. The following example shows the dashboard for a DB instance\. By default, the Performance Insights dashboard shows data for the last hour\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_0b.png)

The dashboard is divided into three parts:

1. **Counter Metrics** – Shows data for specific performance counter metrics\.

1. **DB Load Chart** – Shows how the DB load compares to DB instance capacity as represented by the **Max vCPU** line\.

1.  **Top *items*** – Shows the top waits, SQL, hosts, and users contributing to DB load\.

**Topics**
+ [Counter metrics chart](#USER_PerfInsights.UsingDashboard.Components.Countermetrics)
+ [Database load chart](#USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions)
+ [Top dimensions table](#USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable)

## Counter metrics chart<a name="USER_PerfInsights.UsingDashboard.Components.Countermetrics"></a>

 The **Counter metrics** chart displays data for performance counters\. The default metrics depend on the DB engine:
+ MySQL and MariaDB – `db.SQL.Innodb_rows_read.avg`
+ Oracle – `db.User.user calls.avg`
+ Microsoft SQL Server – `db.Databases.Active Transactions(_Total).avg`
+ PostgreSQL – `db.Transactions.xact_commit.avg`

![\[Counter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/oracle_perf_insights_counters.png)

To change the performance counters, choose **Manage Metrics**\. You can select multiple **OS metrics** or **Database metrics**, as shown in the following screenshot\. To see details for any metric, hover over the metric name\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_select_metrics.png)

For more information, see [Adding counter metrics to the Performance Insights dashboard](USER_PerfInsights_Counters.md)\.

## Database load chart<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions"></a>

The **Database load** chart shows how the database activity compares to DB instance capacity as represented by the **Max vCPU** line\. By default, the stacked line chart represents DB load as average active sessions per unit of time\. The DB load is sliced \(grouped\) by wait states\. 

![\[Database load\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_2.png)

You can choose to display load as active sessions grouped by waits, SQL, users, or hosts\. You can also choose a line graph\.

![\[Filter metrics\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_2b.png)

To see details about a DB load item such as a SQL statement, hover over the item name\.

![\[Database load item details\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_2c.png)

To see details for any item for the selected time period in the legend, hover over that item\.

![\[Time period details for DB load\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_3.png)

## Top dimensions table<a name="USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable"></a>

The Top dimensions table slices DB load by different dimensions\. A dimension is a category or "group by" for different characteristics of DB load\. If the dimension is SQL, **Top SQL** shows the SQL statements that contribute the most to DB load\. For example, 95% of active database sessions might run `SELECT SUM(order_total) FROM orders`, making this a top SQL query\.

![\[Top N dimensions\]](http://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/images/perf_insights_4c.png)

Choose any of the following dimension tabs:
+ Top SQL – The SQL statements that are currently running
+ Top waits – The event for which the database backend is waiting
+ Top hosts – The host name of the connected client
+ Top users – The user logged in to the database
+ Top databases – The name of the database to which the client is connected \(PostgreSQL, MySQL, and MariaDB only\)
+ Top applications \(PostgreSQL only\) – The name of the application that is connected to the database
+ Top session types \(PostgreSQL only\) – The type of the current session

To learn how to analyze queries by using the **Top SQL** tab, see [Overview of the Top SQL tab](USER_PerfInsights.UsingDashboard.Components.AvgActiveSessions.TopLoadItemsTable.TopSQL.md)\.