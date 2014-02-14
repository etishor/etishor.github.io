---
title: myWeather widget
author: Iulian Margarintescu
layout: post
permalink: /programming/myweather-widget/
autometa:
  - myweather widget register_sidebar_widget register_sidebar_widget widget_weather_init widget_weather widget_weather widget_weather_init
head_meta:
  - name="keywords" content=""
ratings_users:
  - 0
ratings_score:
  - 0
ratings_average:
  - 0
views:
  - 11966
dsq_thread_id:
  - 306768913
categories:
  - Programming
tags:
  - administration interface
  - administrator
  - init
  - multiple users
  - myweather
  - page options
  - PHP
  - php function
  - plugin
  - return
  - sidebar
  - submenu
  - widget
  - wordpress
---
To create a quick and dirty widget from myWeather wordpress plugin just add the following to the end of myweather.php

<!--more-->

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw2">function</span> widget_weather_init<span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span> !<a href="http://www.php.net/function_exists"><span class="kw3">function_exists</span></a><span class="br0">&#40;</span><span class="st0">&#8216;register_sidebar_widget'</span><span class="br0">&#41;</span> <span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">return</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; register_sidebar_widget<span class="br0">&#40;</span><a href="http://www.php.net/array"><span class="kw3">array</span></a><span class="br0">&#40;</span><span class="st0">&#8216;Weather'</span>, <span class="st0">&#8216;widgets'</span><span class="br0">&#41;</span>, <span class="st0">&#8216;widget_weather'</span><span class="br0">&#41;</span>; &nbsp; &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw2">function</span> widget_weather<span class="br0">&#40;</span><span class="re0">$args</span><span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; <a href="http://www.php.net/extract"><span class="kw3">extract</span></a><span class="br0">&#40;</span><span class="re0">$args</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; <a href="http://www.php.net/echo"><span class="kw3">echo</span></a> <span class="re0">$before_widget</span> . <span class="re0">$before_title</span> . <span class="re0">$title</span> . <span class="re0">$after_title</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; wp_myweather<span class="br0">&#40;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; <a href="http://www.php.net/echo"><span class="kw3">echo</span></a> <span class="re0">$after_widget</span>;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        add_action<span class="br0">&#40;</span><span class="st0">&#8216;widgets_init'</span>, <span class="st0">&#8216;widget_weather_init'</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

Also the part where you search for the city could be added as a control for the widget but i didn't relay need it to be .

MyWeather plugin's [homepage][1].

# <a id="update">Update</a>

If you have multiple users with different credentials on your blog and you are the only administrator you can restrict the access to myWeather plugin administration interface by changing the function *myweather\_to\_admin()* to look like this (use 10 instead of 1 for required level) :

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw2">function</span> myweather_to_admin<span class="br0">&#40;</span><span class="br0">&#41;</span> <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">if</span> <span class="br0">&#40;</span><a href="http://www.php.net/function_exists"><span class="kw3">function_exists</span></a><span class="br0">&#40;</span><span class="st0">&#8216;add_submenu_page'</span><span class="br0">&#41;</span><span class="br0">&#41;</span> <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; add_submenu_page<span class="br0">&#40;</span><span class="st0">&#8216;options-general.php'</span>, <span class="st0">&#8216;myWeather'</span>,
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;<span class="st0">&#8216;myWeather'</span>, <span class="nu0">10</span>, <a href="http://www.php.net/basename"><span class="kw3">basename</span></a><span class="br0">&#40;</span><span class="kw2">__FILE__</span><span class="br0">&#41;</span>,<span class="st0">&#8216;myweather_admin'</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

 [1]: http://www.thedeveloperinside.com/blog/myweather-20-released/ "MyWeather"