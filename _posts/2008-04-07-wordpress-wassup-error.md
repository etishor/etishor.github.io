---
title: WordPress WassUp Error
author: Iulian Margarintescu
excerpt: |
  I\'ve upgraded the wassup plugin to the latest version ( 1.5 ) and an error started to show in the pages:
  
  [ wp_wassup_tmp\' doesn\'t exist ]
  
  The solution is to deactivate the plugin and then reactivate it... witch should be the right way to upgrade your plugins.
layout: post
permalink: /programming/wordpress-wassup-error/
views:
  - 10121
dsq_thread_id:
  - 315338883
categories:
  - Programming
tags:
  - dashboard
  - database error
  - date
  - error
  - itemstot
  - mysql
  - 'null'
  - page wassup
  - PHP
  - sql
  - sql syntax
  - syntax error
  - time chart
  - wassup
  - wordpress
---
I've upgraded the wassup plugin to the latest version ( 1.5 ) and an error started to show in the pages:

[ wp_wassup_tmp' doesn't exist ]

The solution is to deactivate the plugin and then reactivate it... witch should be the right way to upgrade your plugins.

**Update:**

I also get a syntax error from the wassup plugin, on the dashboard:

```{
***WordPress database error:*** [You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'AND 1207656596' at line 1]
{% highlight sql %}
SELECT COUNT(DISTINCT wassup_id) AS itemstot FROM wp_wassup WHERE wassup_id IS NOT NULL  AND timestamp BETWEEN  AND 1207656596
}```

It seems that the errore comes from a debug query that was left in the wassupDashChart() function. To fix the error you need to comment two lines (1631,1632) of code in wassup.php:

{% highlight php linenos %}
// This is the function to print out a chart's preview in the dashboard
function wassupDashChart() {
global $wpdb;
$table_name = $wpdb-&gt;prefix . "wassup";
$to_date = wassup_get_time();
$Chart = New MainItems;
$Chart-&gt;tableName = $table_name;
$Chart-&gt;to_date = $to_date;{% endhighlight %}

//$itemstot = $Chart-&gt;calc_tot("count", $search, null, "DISTINCT");
//print $itemstot;

echo '&lt;h3&gt;WassUp Stats &lt;cite&gt;&lt;a href="admin.php?page=wassup"&gt;More »&lt;/a&gt;&lt;/cite&gt;&lt;/h3&gt;';
echo $Chart-&gt;TheChart(1, "400", "125", "", $wassup_options-&gt;wassup_chart_type, "bg,s,00000000", "dashboard", "left");
} //end function wassupDashChart
{% endhighlight %}
