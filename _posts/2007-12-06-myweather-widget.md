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


{% highlight php linenos %}
function widget_weather_init(){
  if ( !function_exists('register_sidebar_widget') )
    return;
  register_sidebar_widget(array('Weather', 'widgets'), 'widget_weather');
}

function widget_weather($args){
  extract($args);
  echo $before_widget . $before_title . $title . $after_title;
  wp_myweather();
  echo $after_widget;
}

add_action('widgets_init', 'widget_weather_init');
{% endhighlight %}

Also the part where you search for the city could be added as a control for the widget but i didn't relay need it to be .

MyWeather plugin's <a title="MyWeather" href="http://www.thedeveloperinside.com/blog/myweather-20-released/">homepage</a>.

### Update

If you have multiple users with different credentials on your blog and you are the only administrator you can restrict the access to myWeather plugin administration interface by changing the function <em>myweather_to_admin()</em> to look like this (use 10 instead of 1 for required level) :

{% highlight php linenos %}
function myweather_to_admin() {
  if (function_exists('add_submenu_page')) {
    add_submenu_page('options-general.php', 'myWeather',
    'myWeather', 10, basename(__FILE__),'myweather_admin');
  }
}
{% endhighlight %}
