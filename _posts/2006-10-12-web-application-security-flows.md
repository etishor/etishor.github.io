---
title: Web application security flows
author: Iulian Margarintescu
layout: post
permalink: /security/web-application-security-flows/
comments: true
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
Here goes... my first security related post :)

### Intro

First i must say that this will not be a way to prove that your application is secure, it will only be a quick & dirty way of finding common bugs in web applications. The following will be a few techniques that i tend to use the first time i see an web application ( and generally a database application ). I will use PHP for the few code examples but the vulnerabilities presented are not limited to PHP, in fact are independent of the server-side programming language used.

### Unexpected Input

The first and most common bug is unsanitized user input. The easy way to check for this error is to start replacing variables passed on the URL with characters that need to be escaped when used in an SQL query. Let's say the after submitting some data or clicking a link in the application you end up in a page like

{% highlight text %}
http://www.example.com/products.php?category=1
{% endhighlight %}

In an vulnerable application this most probably generates a query like

{% highlight php%}
$query='SELECT * FROM products WHERE category_id='.$_GET['category']
{% endhighlight %}

Now changing the value of the category to a special SQL character is straightforward:

{% highlight text %}
http://www.example.com/products.php?category='
{% endhighlight %}

And if the assumption about how the variable is used is correct you will probably end up in a page containing an error message from the database server telling you that there was an error executing the query. Now the executed query looks like this:

{% highlight sql%}
SELECT * FROM products WHERE category_id='
{% endhighlight %}

Hmm... what next? Server said error, it was an error so what's the big deal? Very big deal. Now let's change the variable to something that actually does something.

{% highlight text %}
http://www.example.com/products.php?category=1;drop table products;
{% endhighlight %}

witch will generate a query like

{% highlight sql%}
SELECT * FROM products WHERE category_id=1;DROP TABLE products;
{% endhighlight %}

Is this big deal enough ? Or maybe something like:

{% highlight text %}
http://www.example.com/products.php?category=1;insert into users values( null , 'x',password('x'),true );
{% endhighlight %}

assuming that there is a user system in the application and a table users ( user_id , user_name , user_password , is_admin ) the query will become:

{% highlight sql %}
SELECT * FROM products WHERE category_id=1;insert into users values( null , 'x',password('x'),true );
{% endhighlight %}

which, if the assumptions are correct, will insert a new user x witch is admin.

This example is only supposed to give you an idea of this attack vector on an application. Evan if this example is very simplistic I'm amazed of how many application i found ( and find every day ) that are vulnerable to this attack.

As a habit when i see an URL that contains something like variable=value i usually try changing value to an ' and see what happens. This takes only a few seconds. If i end up in an empty page most probably the attack works but errors are not displayed. If the application keeps working but says an error has occurred again most probably this attack works. If no error is displayed and the application keeps working as expected it only means that the particular variable that I've changed is probably checked and escaped before building the query string.

Again i have to say that if you test your application like this and nothing happens it DOES NOT mean that your application is secure, only that the particular variables that you tried are probably escaped.

I'll stop here for now, but just remember to change the URL every time you see a variable and do report the problem to the author or owner of the application so that maybe the web will become a safer place.

... more to come soon ...

![Exploits of a mom](http://imgs.xkcd.com/comics/exploits_of_a_mom.png)