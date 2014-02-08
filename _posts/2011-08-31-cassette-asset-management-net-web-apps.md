---
title: 'Cassette &#8211; Asset management for .NET web apps'
author: Iulian Margarintescu
layout: post
permalink: /net/cassette-asset-management-net-web-apps/
views:
  - 289
dsq_thread_id:
  - 400686806
categories:
  - .NET
tags:
  - asp.net mvc
  - cassette
  - combine
  - JavaScript
  - minify
  - stylesheets
  - web
---
A few weeks ago i&#8217;ve started using [Cassette][1], a very nice asset management tool for .net web applications. It will basically take your assets ( javascript, coffescript, css, images ) and do the right thing with them ( combine, minify etc).

While working with it i had a few issues which I&#8217;ve  managed to solve and i would like to share them.<!--more-->

<div id="attachment_288" style="width: 81px" class="wp-caption aligncenter">
  <a href="http://getcassette.net/"><img class="size-full wp-image-288" title="Cassette" src="http://www.erata.net/wp-content/uploads/2011/08/casette.png" alt="Cassette" width="71" height="56" /></a><p class="wp-caption-text">
    Cassette
  </p>
</div>

## File not found in module.txt

The first problem was related to a .css file for the jquery-ui theme that was present in the ~Content/themes/redmond/jquery-ui.medsentry.css folders. My module.txt looked like this:

<pre class="brush:plain">themes/redmond/jquery-ui-1.8.16.custom.css
Site.css
# other css files</pre>

After compiling cassette locally and using the debug symbols to step trough the code I&#8217;ve noticed that cassette was storing a list of files ( assets ) it found in the Content folder but the path separator was &#8220;\&#8221; instead of &#8220;/&#8221;. After changing the slashes in the included paths in my module.txt everything works as expected. Now the module.txt looks like this:

<pre class="brush:plain">themes\redmond\jquery-ui-1.8.16.custom.css
Site.css
# other css files</pre>

## Using Cassette modules with IIS6

Due to some external constaints i had use IIS6 on a test instance of the application. After the deployment I&#8217;ve noticed that the images in the .css files are not working when using cassette modules to combine the files. This was due to cassette rewriting the paths and using Routes to manage them. In IIS6 you have to do one additional step for this to work. Open IIS Manager, locate your application and open the properties window.  On the Virtual Directory tab open the Configuration dialog, located in the Application settings section of the tab. In the Wildcard application maps section click Insert and add &#8220;C:\WINDOWS\Microsoft.NET\Framework64\v4.0.30319\aspnet\_isapi.dll&#8221; or whatever the path to aspnet\_isapi.dll is on your system. Click Ok and Apply and enjoy.

Another way to manage the images in the css files is to embed them as data links. Cassette [supports this][2], BUT Internet Explorer <= 7 does not. Also IE 8 has some size limitations.

## Image Optimizer for Visual Studio

Not related to cassette but i&#8217;d also like to recommend the [Image Optimizer for Visual Studio Extension][3]. Exactly what you would expect  from an extension with this name :).

 [1]: http://getcassette.net/ "Get Cassette"
 [2]: https://github.com/andrewdavey/cassette/blob/master/src/Example/CassetteConfiguration.cs "Data uri conversion"
 [3]: http://blogs.microsoft.co.il/blogs/gilf/archive/2011/05/14/visual-studio-2010-image-optimizer-extension.aspx "Image optimizer for visual studio"