---
title: NHibernate Cache Provider for Membase
layout: post
permalink: /net/nhibernate-cache-provider-membase/
views:
  - 698
dsq_thread_id:
  - 313328707
category: .NET
---
I've written a cache provider for [NHibernate][1] which uses [Membase][2] as a cache engine.

You can find the souce code on [github][3].

Example configuration can be found in the [test project][4].  
  
To use the cache provider add the following lines to the hnibernate configuration:

{% highlight xml linenos %}
<property name='cache.provider_class'>Nhibernate.Caches.Membase.MembaseCacheProvider, Nhibernate.Caches.Membase</property>
<property name='cache.use_second_level_cache'>true</property></pre>
{% endhighlight %}

If you encounter any problems with it drop me a line.

 [1]: http://nhforge.org "NHibernate"
 [2]: http://membase.org "Membase"
 [3]: https://github.com/etishor/NHibernate.Caches.Membase "NHibernate.Caches.Membase"
 [4]: https://github.com/etishor/NHibernate.Caches.Membase/blob/master/NHibernate.Caches.Membase.Tests/App.config "App.config"