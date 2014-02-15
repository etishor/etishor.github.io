---
title: Flash Plugin on FreeBSD
author: Iulian Margarintescu
layout: post
permalink: /freebsd/flash-plugin-on-freebsd/
comments: true
dsq_thread_id: 20 http://www.erata.net/weblog/freebsd/2006/10/16/flash-plugin-on-freebsd/
categories:
  - FreeBSD
tags:
  - bsd
  - firefox
  - flash
  - flash player 7
  - flash player plugin
  - flash7
  - FreeBSD
  - install
  - install flash player 7
  - Linux
  - linux tar
  - player
  - ports
  - root
  - rtld
  - solution
---
## Problem to solve

Make a recent flash player plugin work in firefox on FreeBSD 6.

## Update

There is a new [article][1] on enabling the linux-flash7 plugin with native browsers.

With the release of FreeBSD 6.2 and the update on linuxpluginwrapper this tutorial has become obsolete. It's only recommended if you need to stick with an older version of freebsd and/or flashplugin.

## Solution

First thing to do is get [this][2] patch for /usr/src/libexec/rtld-elf/rtld.c, copy it in /usr/src and do the following: (you must have the source tree in /usr/src and the version of the sources must be the same with the version of the installed world, if they are different or you are not sure it's probably best to rebuild your world ( FreeBSD world that is :) ) and kernel )

{% highlight bash linenos %}
root#cd /usr/src
root#patch -p0 < rtld_dlsym_hack.diff
root#cd libexec/rtld-elf/
root#make && make install
{% endhighlight %}

After successfully installing the patch you need to fetch and unpack the linuxflashplayer7:

{% highlight bash linenos %}
root#cd /usr/ports/www/linux-flashplugin7/
root#make fetch
root#cd /usr/local/lib/firefox/plugins/
root#tar zxvf /usr/ports/distfiles/flashplugin/7.0r68/install_flash_player_7_linux.tar.gz
root#cp install_flash_player_7_linux/libflashplayer.so libflashplayer.so
root#cp install_flash_player_7_linux/flashplayer.xpt flashplayer.xpt
{% endhighlight %}

Now make sure you have the plugin files ( libflashplayer.so and flashplayer.xpt ) in /usr/local/lib/firefox/plugins.You now need to install the linuxpluginwrapper:

{% highlight bash linenos %}
root#cd /usr/ports/www/linuxpluginwrapper/ && make install clean
{% endhighlight %}

Finally you have to change /etc/libmap.conf:


{% highlight text linenos %}
[/usr/local/lib/firefox/plugins/libflashplayer.so]
libpthread.so.0 libpthread.so.2
libdl.so.2 pluginwrapper/flash7.so
libz.so.1 libz.so.3
libm.so.6 libm.so.4
libc.so.6 pluginwrapper/flash7.so
{% endhighlight %}

Now open your browser and go to about:plugins. Flash 7 plugins is active.

 [1]: http://www.erata.net/freebsd/updateflash7-with-native-freebsd-browsers/
 [2]: https://gist.github.com/etishor/9019433 "rtld dlsym hack"