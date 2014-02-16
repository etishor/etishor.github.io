---
title: 'Flash7 with native FreeBSD browsers'
layout: post
comments: true
dsq_thread_id: 28 http://www.erata.net/weblog/freebsd/2007/03/18/updateflash7-with-native-freebsd-browsers/
category: FreeBSD
---
Finally the linux-flash7 plugin works with native firefox. If it's not working for you this may help.


## Update

With the nspluginwrapper port this [  
post][1] may be more usefull. The if you still have problems you may try the tips below.

## Step By Step HOWTO

1. Use cvsup or portsnap to update your ports tree
2. install www/linuxpluginwrapper
3. copy libflashplayer.so and flashplayer.xpt from distfiles/flashplugin/7.0r68/install_flash_player_7_linux.tar.gz to /usr/local/lib/browser_plugins
4. Fireup firefox or opera and test

If in the future the dependency on the linux emulation layer is removed from linux-flash7 port it will be ok to install the port as you would normally do, but until then i prefer to manually unpack the two files instead of installing the hole linux-base port(s) witch is not needed anyway for the plugin to work.  
  
If it works congratulations, if firefox crashes and opera displays just an empty box ... read on.

## Fixing Plugin Crash

Here there are two things to try. (the first one is recommended)

1.  edit /etc/X11/xorg.conf and **disable the Composite extension** and **set the ColorDepth to 24**
2.  edit /usr/local/bin/firefox and add **export XLIB\_SKIP\_ARGB_VISUALS=1** in the beginning of the file

I've noticed that opera still has some trouble working with the plugin and as a fix I've done a small change to the linuxpluginwrapper port. In compat\_linux/linux\_ioctl.c I've changed the default switch branch from:

{% highlight c linenos %}
default:
    errno = ENOSYS;
    ret   = -1;
    dprintf("ioctl(fd=%d, request=%08X(on Linux), ...) = %d / ERRNO = %d", d, l_request, ret, errno);
    break;
{% endhighlight %}

to

{% highlight c linenos %}
default:
     ret= _ioctl(d,l_request,va_arg(ap,int*)); 
     break; 
{% endhighlight %}

After this change the plugin works fine but sometimes you need to reload the page for opera to display the flash content.

## References

Information on fixing the crash was gathered from:

*   <https://launchpad.net/ubuntu/+source/flashplugin-nonfree/+bug/62988>
*   <http://www.kaourantin.net/#116224299860645174>

## To be continued

Now that I've learned this i really have a good feeling about linux-flash9 and native browsers. I haven't tested yet but i guess the fix for the crashes works also for linux-flash9 with linu-firefox and linux-opera.

 [1]: http://unix.derkeiler.com/Mailing-Lists/FreeBSD/questions/2007-07/msg01919.htm