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

[ wp\_wassup\_tmp' doesn't exist ]

The solution is to deactivate the plugin and then reactivate it... witch should be the right way to upgrade your plugins.<!--more-->

**Update:**

I also get a syntax error from the wassup plugin, on the dashboard:  
<cite><br /> <strong>WordPress database error:</strong> [You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near 'AND 1207656596' at line 1]<br /> <code>SELECT COUNT(DISTINCT wassup_id) AS itemstot FROM wp_wassup WHERE wassup_id IS NOT NULL  AND timestamp BETWEEN  AND 1207656596</code><br /> </cite>

It seems that the errore comes from a debug query that was left in the wassupDashChart() function. To fix the error you need to comment two lines (1631,1632) of code in wassup.php:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">// This is the function to print out a chart's preview in the dashboard</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw2">function</span> wassupDashChart<span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <a href="http://www.php.net/global"><span class="kw3">global</span></a> <span class="re0">$wpdb</span>;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="re0">$table_name</span> = <span class="re0">$wpdb</span>-><span class="me1">prefix</span> . <span class="st0">"wassup"</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="re0">$to_date</span> = wassup_get_time<span class="br0">&#40;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="re0">$Chart</span> = <span class="kw2">New</span> MainItems;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="re0">$Chart</span>-><span class="me1">tableName</span> = <span class="re0">$table_name</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="re0">$Chart</span>-><span class="me1">to_date</span> = <span class="re0">$to_date</span>;
      </div>
    </li>
  </ol>
</div>

//$itemstot = $Chart->calc_tot("count", $search, null, "DISTINCT");  
//print $itemstot;

echo &#8216;<h3>WassUp Stats <cite><a href="admin.php?page=wassup">More »</a></cite></h3>';  
echo $Chart->TheChart(1, "400&#8243;, "125&#8243;, "", $wassup\_options->wassup\_chart_type, "bg,s,00000000&#8243;, "dashboard", "left");  
} //end function wassupDashChart