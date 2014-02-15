---
title: RavenDb EnsureDatabaseExists extension method
layout: post
permalink: /net/ravendb-ensuredatabaseexists-extension-method/
views:
  - 20
dsq_thread_id:
  - 460877763
category: .NET
---
Just a quick hint for others like me, who spent some minutes searching for the EnsureDatabaseExists method when trying to use [RavenDb][1] with  Multi-Databases. 

EnsureDatabaseExists is an extension method on IDatabaseCommands defined in the Raven.Client.Extensions namespace. 

To make it work you need to add a using statement for this namespace.


{% highlight c# linenos %}
using Raven.Client;
using Raven.Client.Extensions;

using (DocumentStore store = new DocumentStore()
{
    Url = "http://localhost:8080/" 
})
{
    store.Initialize();
    store.DatabaseCommands.EnsureDatabaseExists("SomeDatabase");
}
{% endhighlight %}

 [1]: http://ravendb.net "RavenDb"