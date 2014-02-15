---
title: 'KDevelop - KDevTrollProject Problem'
author: Iulian Margarintescu
layout: post
permalink: /freebsd/kdevelop-kdevtrollproject-problem/
comments : true
dsq_thread_id: 27 http://www.erata.net/weblog/freebsd/2007/03/17/kdevelop-kdevtrollproject-problem/
categories:
  - FreeBSD
tags:
  - bsd
  - cpp
  - error
  - FreeBSD
  - freebsd mailing
  - houers
  - kde
  - kdevelop
  - lex
  - mailing
  - parsers
  - ping
  - ports
  - relink
  - symbol
  - undefined reference
---
After updating kde to 3.5.6 and kdevelop to 3.4.0_1 ( around 15.03.2007 ) kdevelop failed to open qmake based projects. The error was "Error creating project management plugin KDevTrollProject".

## Update

On 23.03.2007 a fix has been committed to the kdevelop port so if you encounter this problem it's better to update the ports tree and recompile kdevelop.

## Finding why

It took me a few houers to find out that the problem was an undefined reference to a symbol in */usr/local/lib/libkdevqmakeparser.so*. The symbol was QMake::Lexer. Grepping to kdevelop sources i found that the libkdevqmakeparser is build in buildtools/lib/parsers/qmake. There i found that qmake_lex.cpp was not compiled and linked to the library.

## Quick Fix

Below are the command lines to build qmake_lex.cpp and relink libkdevqmakeparser.so

{% highlight bash %}
#cd /usr/ports/devel/kdevelop
#make extract patch configure
#cd work/kdevelop-3.4.0/buildtools/lib/parsers/qmake
#fetch http://www.erata.net/wp-content/uploads/File/fix_lex.sh
#cat fix_lex.sh ( just to make sure i haven't hidden anything in that file :) )
#sh fix_lex.sh
#cp .libs/libkdevqmakeparser.so.0 /usr/local/lib/
{% endhighlight %}

## Conclusion

I somehow have the impression that I'm the only one having this problem since i haven't seen any reports on the mailing lists. It may be some bad combination of autotools/libtools. If I'm the only one having this problem then it's not worth investing more time in a proper fix. If other users will report this problem then probably a patch to add the qmake_lex.cpp file to the Makefile with appropriate rules may be needed. My experience with autotools and handwritten Makefiles is close to zero (and i intend to keep it that way) but i guess the kde folks will have no problem fixing this once the problem i known.

If someone else experiences this problem please leave a comment or a post a message on the freebsd mailing list