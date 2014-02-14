---
title: 'Flash on FreeBSD - Gnash'
author: Iulian Margarintescu
excerpt: "Watching flash content on FreeBSD was and still is a problem. It\'s sad Adobe won\'t publish a native version of flash for FreeBSD, or even better open the sources for the flash player. It\'s strange they don\'t, unless the sources are a real mess I don\'t see any strong reason for not doing so. Now that rumors are found about new technologies for web graphics likesvg, html 5, flash might not be so popular in the future."
layout: post
permalink: /freebsd/flash-on-freebsd-gnash/
views:
  - 2509
dsq_thread_id:
  - 312303416
categories:
  - FreeBSD
tags:
  - Adobe
  - flash
  - flash7
  - FreeBSD
  - gnash
  - Linux
  - linux plugin
  - linux-flash
  - player
  - plugin
  - reason
  - svg
  - web
  - web graphics
  - Youtube
---
Watching flash content on FreeBSD was and still is a problem. It's sad Adobe won't publish a native version of flash for FreeBSD, or even better open the sources for the flash player. It's strange they don't, unless the sources are a real mess I don't see any strong reason for not doing so. Now that rumors are found about new technologies for web graphics like svg, html 5, flash might not be so popular in the future.<!--more-->

About the linux plugin for native firefox i don't have much to say. I does not work reliable. Yes you can watch some movies in some conditions with linux-flash7 with nspluginwrapper but still with a lot of problems depending on arch/site/browser/movie version. With linux-flash9 even more problems. I realized that in the end it's more frustration than satisfaction and the feeling was that I'm fighting the wrong battle in trying to make the linux-flash work on FreeBSD.

Now I'm living with [gnash][1] ( graphics/gnash ). Well, it's not perfect but at least it's open source and does play a lot of movies. And the best thing about it was that with the release of a new version ( 0.8.2 ) movies that did not work with 0.8.1 are now working so it's progressing. Youtube is working pretty well also.

In conclusion if you really need flash on FreeBSD try [supporting gnash.][1] As an open source alternative to the flash plugin it's starting to look more than promising.

 [1]: http://www.gnu.org/software/gnash/ "Gnash"