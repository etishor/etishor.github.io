---
title: '[Update]Flash7 with native FreeBSD browsers'
author: Iulian Margarintescu
layout: post
permalink: /freebsd/updateflash7-with-native-freebsd-browsers/
autometa:
  - opera native eddit flash7 port firefox linux update
views:
  - 2336
head_meta:
  - name="keywords" content=""
ratings_users:
  - 0
ratings_score:
  - 0
ratings_average:
  - 0
dsq_thread_id:
  - 306768903
categories:
  - FreeBSD
tags:
  - bsd
  - emulation layer
  - firefox
  - flash
  - flash player 7
  - flash7
  - flash9
  - FreeBSD
  - install
  - install flash player 7
  - Linux
  - linux emulation
  - port
  - ports
  - ret
  - Step
  - switch branch
  - unix
  - update
---
Finally the linux-flash7 plugin works with native firefox. If it&#8217;s not working for you this may help.

<!--more-->

## Update

With the nspluginwrapper port this [  
post][1] may be more usefull. The if you still have problems you may try the tips below.

## Step By Step HOWTO

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        1. Use cvsup or portsnap to update your ports tree
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        2. install www/linuxpluginwrapper
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        3. copy libflashplayer.so and flashplayer.xpt from distfiles/flashplugin/7.0r68/install_flash_player_7_linux.tar.gz to /usr/local/lib/browser_plugins
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        4. Fireup firefox or opera and test
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

If in the future the dependency on the linux emulation layer is removed from linux-flash7 port it will be ok to install the port as you would normally do, but until then i prefer to manually unpack the two files instead of installing the hole linux-base port(s) witch is not needed anyway for the plugin to work.  
  
If it works congratulations, if firefox crashes and opera displays just an empty box &#8230; read on.

## Fixing Plugin Crash

Here there are two things to try. (the first one is recommended)

1.  edit /etc/X11/xorg.conf and **disable the Composite extension** and **set the ColorDepth to 24**
2.  edit /usr/local/bin/firefox and add **export XLIB\_SKIP\_ARGB_VISUALS=1** in the beginning of the file

I&#8217;ve noticed that opera still has some trouble working with the plugin and as a fix I&#8217;ve done a small change to the linuxpluginwrapper port. In compat\_linux/linux\_ioctl.c I&#8217;ve changed the default switch branch from:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">default</span>:
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; errno = ENOSYS;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; ret &nbsp; = <span class="nu0">-1</span>;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp; &nbsp; dprintf<span class="br0">&#40;</span><span class="st0">"ioctl(fd=%d, request=%08X(on Linux), &#8230;) = %d / ERRNO = %d"</span>, d, l_request, ret, errno<span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; <span class="kw2">break</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

to

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">default</span>:
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp;ret= _ioctl<span class="br0">&#40;</span>d,l_request,va_arg<span class="br0">&#40;</span>ap,<span class="kw4">int</span>*<span class="br0">&#41;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp;<span class="kw2">break</span>;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

After this change the plugin works fine but sometimes you need to reload the page for opera to display the flash content.

## References

Information on fixing the crash was gathered from:

*   <https://launchpad.net/ubuntu/+source/flashplugin-nonfree/+bug/62988>
*   <http://www.kaourantin.net/#116224299860645174>

## To be continued

Now that I&#8217;ve learned this i really have a good feeling about linux-flash9 and native browsers. I haven&#8217;t tested yet but i guess the fix for the crashes works also for linux-flash9 with linu-firefox and linux-opera.

 [1]: http://unix.derkeiler.com/Mailing-Lists/FreeBSD/questions/2007-07/msg01919.htm