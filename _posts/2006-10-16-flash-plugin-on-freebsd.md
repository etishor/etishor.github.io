---
title: Flash Plugin on FreeBSD
author: Iulian Margarintescu
layout: post
permalink: /freebsd/flash-plugin-on-freebsd/
autometa:
  - plugin flash freebsd tutorial firefox linux unix adobe macromedia
views:
  - 7200
head_meta:
  - name="keywords" content=""
dsq_thread_id:
  - 306768832
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

<!--more-->

## Update

There is a new [article][1] on enabling the linux-flash7 plugin with native browsers.

With the release of FreeBSD 6.2 and the update on linuxpluginwrapper this tutorial has become obsolete. It&#8217;s only recommended if you need to stick with an older version of freebsd and/or flashplugin.

## Solution

First thing to do is get [this][2] patch for /usr/src/libexec/rtld-elf/rtld.c, copy it in /usr/src and do the following: (you must have the source tree in /usr/src and the version of the sources must be the same with the version of the installed world, if they are different or you are not sure it&#8217;s probably best to rebuild your world ( FreeBSD world that is <img src="http://www.erata.net/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley" /> ) and kernel )

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        root#cd /usr/src
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        root#patch -p0 < rtld_dlsym_hack.diff
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        root#cd libexec/rtld-elf/
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        root#make && make install
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

After successfully installing the patch you need to fetch and unpack the linuxflashplayer7:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        root#cd /usr/ports/www/linux-flashplugin7/
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        root#make fetch
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        root#cd /usr/local/lib/firefox/plugins/
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        root#tar zxvf /usr/ports/distfiles/flashplugin/7.0r68/install_flash_player_7_linux.tar.gz
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        root#cp install_flash_player_7_linux/libflashplayer.so libflashplayer.so
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        root#cp install_flash_player_7_linux/flashplayer.xpt flashplayer.xpt
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

Now make sure you have the plugin files ( libflashplayer.so and flashplayer.xpt ) in /usr/local/lib/firefox/plugins.You now need to install the linuxpluginwrapper:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        root#cd /usr/ports/www/linuxpluginwrapper/ && make install clean
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

Finally you have to change /etc/libmap.conf:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        [/usr/local/lib/firefox/plugins/libflashplayer.so]
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        libpthread.so.0 &nbsp; &nbsp;libpthread.so.2
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        libdl.so.2 &nbsp; &nbsp; &nbsp; &nbsp; pluginwrapper/flash7.so
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        libz.so.1 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;libz.so.3
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        libm.so.6 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;libm.so.4
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        libc.so.6 &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;pluginwrapper/flash7.so
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

Now open your browser and go to about:plugins. Flash 7 plugins is active.

(this is my first tutorial post so be gentle with the comments <img src="http://www.erata.net/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley" /> )

 [1]: http://www.erata.net/weblog/freebsd/2007/03/18/updateflash7-with-native-freebsd-browsers/
 [2]: /wp-content/uploads/File/rtld_dlsym_hack.diff?phpMyAdmin=ZVVy4h18NWWYGtjd86mc4kQ%2COS6 "rtld dlsym hack"