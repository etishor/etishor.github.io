---
title: 'C# HTTP Request'
author: Iulian Margarintescu
layout: post
permalink: /net/csharp-http-request/
views:
  - 810
dsq_thread_id:
  - 315100233
categories:
  - .NET
tags:
  - .NET
  - class
  - csharp
  - google
  - http
  - main string
  - maner
  - qt
  - request
  - response request
  - string args
  - synchronous
  - System.Net
  - today
  - void
  - webrequest
---
A few years ago I&#8217;ve written an article on [how to perform a synchronous HTTP][1] request using Qt 4.2. I I&#8217;ve seen this article today and since now I&#8217;m mostly working with C# i was wandering how fast can i do the same thing in c#. After very few minutes I&#8217;ve managed to came up with this:

<pre class="brush:csharp">using System;
using System.Net;
using System.IO;

class Program
{
    static void Main(string[] args)
    {
        WebRequest request = HttpWebRequest.Create("http://www.google.com");
        WebResponse response = request.GetResponse();
        using(StreamReader reader = new StreamReader(response.GetResponseStream()))
            Console.WriteLine(reader.ReadToEnd());
        Console.ReadKey();
    }
}</pre>

And of course the [WebReqest ][2] class allows you to specify all the properties you would ever need for any request, and also allows you to perform request in an asynchronous maner. Now i don&#8217;t think you can be more expressive than this. I like qt a lot&#8230; but i love c# now.

 [1]: http://www.erata.net/qt-boost/synchronous-http-request/
 [2]: http://msdn.microsoft.com/en-us/library/system.net.webrequest_members.aspx