---
title: 'KDevelop - KDevTrollProject Problem'
layout: post
permalink: /freebsd/kdevelop-kdevtrollproject-problem/
comments : true
dsq_thread_id: 27 http://www.erata.net/weblog/freebsd/2007/03/17/kdevelop-kdevtrollproject-problem/
category: FreeBSD
---
After updating kde to 3.5.6 and kdevelop to 3.4.0_1 ( around 15.03.2007 ) kdevelop failed to open qmake based projects. The error was "Error creating project management plugin KDevTrollProject".

## Update

On 23.03.2007 a fix has been committed to the kdevelop port so if you encounter this problem it's better to update the ports tree and recompile kdevelop.

## Finding why

It took me a few houers to find out that the problem was an undefined reference to a symbol in */usr/local/lib/libkdevqmakeparser.so*. The symbol was QMake::Lexer. Grepping to kdevelop sources i found that the libkdevqmakeparser is build in buildtools/lib/parsers/qmake. There i found that qmake_lex.cpp was not compiled and linked to the library.

## Quick Fix

Below are the command lines to build qmake_lex.cpp and relink libkdevqmakeparser.so

The fix_lex.sh file:

{% highlight bash %}
#!/bin/sh
if /bin/sh /usr/local/bin/libtool --silent --tag=CXX --mode=compile c++ -DHAVE_CONFIG_H  -I. -I. -I../../../.. -I../../../../lib/interfaces  -I../../../../lib/interfaces/extensions -I../../../../lib/util -I../../../../lib/widgets/propeditor  -I/usr/local/include -I/usr/X11R6/include  -I/usr/local/include  -D_THREAD_SAFE -pthread -DQT_THREAD_SUPPORT   -I/usr/local/include -I/usr/local/include  -I/usr/X11R6/include -D_GETOPT_H -D_THREAD_SAFE   -Wno-long-long -Wundef -Wall -W -Wpointer-arith -DNDEBUG -DNO_DEBUG -O2 -O2 -fno-strict-aliasing -pipe -Wno-non-virtual-dtor -fno-exceptions -fno-check-new -fno-common -DQT_CLEAN_NAMESPACE -DQT_NO_ASCII_CAST -DQT_NO_STL -DQT_NO_COMPAT -DQT_NO_TRANSLATION  -MT qmake_lex.lo -MD -MP -MF ".deps/qmake_lex.Tpo" -c -o qmake_lex.lo qmake_lex.cpp;  then mv -f ".deps/qmake_lex.Tpo" ".deps/qmake_lex.Plo"; else rm -f ".deps/qmake_lex.Tpo"; exit 1; fi
/bin/sh /usr/local/bin/libtool --silent --tag=CXX --mode=link c++   -Wno-long-long -Wundef -Wall -W -Wpointer-arith -DNDEBUG -DNO_DEBUG -O2 -O2 -fno-strict-aliasing -pipe -Wno-non-virtual-dtor -fno-exceptions -fno-check-new -fno-common -DQT_CLEAN_NAMESPACE -DQT_NO_ASCII_CAST -DQT_NO_STL -DQT_NO_COMPAT -DQT_NO_TRANSLATION    -o libkdevqmakeparser.la -rpath /usr/local/lib -no-undefined  -L/usr/local/lib -L/usr/X11R6/lib   -D_THREAD_SAFE -pthread  -L/usr/local/lib -lkio qmakeast.lo qmakedriver.lo  qmake_yacc.lo qmake_lex.lo qmakeastvisitor.lo  -Wl,-export-dynamic -L/usr/local/lib -L/usr/X11R6/lib -ljpeg  -L/usr/X11R6/lib
{% endhighlight %}

{% highlight bash %}
cd /usr/ports/devel/kdevelop
make extract patch configure
cd work/kdevelop-3.4.0/buildtools/lib/parsers/qmake
sh fix_lex.sh
cp .libs/libkdevqmakeparser.so.0 /usr/local/lib/
{% endhighlight %}

## Conclusion

I somehow have the impression that I'm the only one having this problem since i haven't seen any reports on the mailing lists. It may be some bad combination of autotools/libtools. If I'm the only one having this problem then it's not worth investing more time in a proper fix. If other users will report this problem then probably a patch to add the qmake_lex.cpp file to the Makefile with appropriate rules may be needed. My experience with autotools and handwritten Makefiles is close to zero (and i intend to keep it that way) but i guess the kde folks will have no problem fixing this once the problem i known.

If someone else experiences this problem please leave a comment or a post a message on the freebsd mailing list