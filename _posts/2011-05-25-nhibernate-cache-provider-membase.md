---
title: NHibernate Cache Provider for Membase
author: Iulian Margarintescu
layout: post
permalink: /net/nhibernate-cache-provider-membase/
views:
  - 698
dsq_thread_id:
  - 313328707
categories:
  - .NET
tags:
  - cache
  - membase
  - nhibernate
  - provider
---
I've written a cache provider for [NHibernate][1] which uses [Membase][2] as a cache engine.

You can find the souce code on [github][3].

Example configuration can be found in the [test project][4].  
<!--more-->

  
To use the cache provider add the following lines to the hnibernate configuration:

<pre class="brush:xml">&lt;property name='cache.provider_class'&gt;Nhibernate.Caches.Membase.MembaseCacheProvider, Nhibernate.Caches.Membase&lt;/property&gt;
&lt;property name='cache.use_second_level_cache'&gt;true&lt;/property&gt;</pre>

If you encounter any problems with it drop me a line.

 [1]: http://nhforge.org "NHibernate"
 [2]: http://membase.org "Membase"
 [3]: https://github.com/etishor/NHibernate.Caches.Membase "NHibernate.Caches.Membase"
 [4]: https://github.com/etishor/NHibernate.Caches.Membase/blob/master/NHibernate.Caches.Membase.Tests/App.config "App.config"