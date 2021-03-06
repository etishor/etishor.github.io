---
title: 'C# HTTP Request'
layout: post
comments: true
category: NET
---
A few years ago I've written an article on [how to perform a synchronous HTTP][1] request using Qt 4.2. I I've seen this article today and since now I'm mostly working with C# i was wandering how fast can i do the same thing in c#. After very few minutes I've managed to came up with this:

{% highlight c# linenos %}
using System;
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
}
{% endhighlight %}

And of course the [WebReqest ][2] class allows you to specify all the properties you would ever need for any request, and also allows you to perform request in an asynchronous maner. Now i don't think you can be more expressive than this. I like qt a lot... but i love c# now.

### Update... 

Or you can do this:

{% highlight c# linenos %}
using System;
using System.Net;
using System.IO;

class Program
{
    static void Main(string[] args)
    {
        var content = new WebClient().DownloadString("http://www.google.com");
        Console.WriteLine(content);
    }
}
{% endhighlight %}

 [1]: http://www.erata.net/qt-boost/synchronous-http-request/
 [2]: http://msdn.microsoft.com/en-us/library/system.net.webrequest_members.aspx