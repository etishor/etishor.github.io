---
title: Web application security flows
author: Iulian Margarintescu
layout: post
permalink: /security/web-application-security-flows/
autometa:
  - application category database server quot error input security vulnerability
views:
  - 783
head_meta:
  - name="keywords" content=""
ratings_users:
  - 0
ratings_score:
  - 0
ratings_average:
  - 0
dsq_thread_id:
  - 418204310
categories:
  - Security
tags:
  - application
  - author
  - database server
  - error
  - first security
  - http
  - member
  - PHP
  - Programming
  - query
  - safe
  - Security
  - server side programming
  - server side programming language
  - side programming language
  - sql
  - way
  - web
  - web application
  - Web application security
  - web applications
  - WHERE
---
Here goes&#8230; my first security related post <img src="http://www.erata.net/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley" /> 

### Intro

First i must say that this will not be a way to prove that your application is secure, it will only be a quick & dirty way of finding common bugs in web applications. The following will be a few techniques that i tend to use the first time i see an web application ( and generally a database application ). I will use PHP for the few code examples but the vulnerabilities presented are not limited to PHP, in fact are independent of the server-side programming language used.

<!--more-->

### Unexpected Input

The first and most common bug is unsanitized user input. The easy way to check for this error is to start replacing variables passed on the URL with characters that need to be escaped when used in an SQL query. Let&#8217;s say the after submitting some data or clicking a link in the application you end up in a page like

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        http://www.example.com/products.php?category=1
      </div>
    </li>
  </ol>
</div>

In an vulnerable application this most probably generates a query like

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        <span class="re0">$query</span>=<span class="st0">&#8216;SELECT * FROM products WHERE category_id=&#8217;</span>.<span class="re0">$_GET</span><span class="br0">&#91;</span><span class="st0">&#8216;category&#8217;</span><span class="br0">&#93;</span>
      </div>
    </li>
  </ol>
</div>

Now changing the value of the category to a special SQL character is straightforward:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        http://www.example.com/products.php?category=&#8217;
      </div>
    </li>
  </ol>
</div>

And if the assumption about how the variable is used is correct you will probably end up in a page containing an error message from the database server telling you that there was an error executing the query. Now the executed query looks like this:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        <span class="kw1">SELECT</span> * <span class="kw1">FROM</span> products <span class="kw1">WHERE</span> category_id=<span class="st0">&#8216;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="st0"</span>
      </div>
    </li>
  </ol>
</div>

Hmm&#8230; what next? Server said error, it was an error so what&#8217;s the big deal? Very big deal. Now let&#8217;s change the variable to something that actually does something.

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        http://www.example.com/products.php?category=1;drop table products;
      </div>
    </li>
  </ol>
</div>

witch will generate a query like

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        <span class="kw1">SELECT</span> * <span class="kw1">FROM</span> products <span class="kw1">WHERE</span> category_id=<span class="nu0">1</span>;DROP <span class="kw1">TABLE</span> products;
      </div>
    </li>
  </ol>
</div>

Is this big deal enough ? Or maybe something like:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        http://www.example.com/products.php?category=1;insert into users values( null , &#8216;x&#8217;,password(&#8216;x&#8217;),true );
      </div>
    </li>
  </ol>
</div>

assuming that there is a user system in the application and a table users ( user\_id , user\_name , user\_password , is\_admin ) the query will become:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        <span class="kw1">SELECT</span> * <span class="kw1">FROM</span> products <span class="kw1">WHERE</span> category_id=<span class="nu0">1</span>;insert <span class="kw1">INTO</span> users <span class="kw1">VALUES</span><span class="br0">&#40;</span> <span class="kw1">NULL</span> , <span class="st0">&#8216;x&#8217;</span>,password<span class="br0">&#40;</span><span class="st0">&#8216;x&#8217;</span><span class="br0">&#41;</span>,true <span class="br0">&#41;</span>;
      </div>
    </li>
  </ol>
</div>

which, if the assumptions are correct, will insert a new user x witch is admin.

This example is only supposed to give you an idea of this attack vector on an application. Evan if this example is very simplistic I&#8217;m amazed of how many application i found ( and find every day ) that are vulnerable to this attack.

As a habit when i see an URL that contains something like variable=value i usually try changing value to an &#8216; and see what happens. This takes only a few seconds. If i end up in an empty page most probably the attack works but errors are not displayed. If the application keeps working but says an error has occurred again most probably this attack works. If no error is displayed and the application keeps working as expected it only means that the particular variable that I&#8217;ve changed is probably checked and escaped before building the query string.

Again i have to say that if you test your application like this and nothing happens it DOES NOT mean that your application is secure, only that the particular variables that you tried are probably escaped.

I&#8217;ll stop here for now, but just remember to change the URL every time you see a variable and do report the problem to the author or owner of the application so that maybe the web will become a safer place.

&#8230; more to come soon &#8230;

<img src="http://imgs.xkcd.com/comics/exploits_of_a_mom.png" alt="Exploits of a mom" width="666" height="205" />