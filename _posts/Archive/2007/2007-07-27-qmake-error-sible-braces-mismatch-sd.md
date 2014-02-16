---
title: 'qmake error:  sible braces mismatch %s:%d'
layout: post
comments: true
category: qt-boost
---
I've just received this error with qmake on windows. Googling for it showed no results so after i've solved the problem i decided to put it here.

The error was near a conditional block like:

{% highlight text linenos %}
unix{
}
win32{
  QMAKE_UIC+ = -L ../bin/plugin
}
{% endhighlight %}

...after searching for unclosed/missing braces for a while without any result and comparing svn versions i've finally found the problem. On the QMAKE_UIC linke instead of "+=" somehow ( probably kdevelop's qmake parser/generator ) got saved as "+ =" (notice the space). The problem would have been much easier to solve if the error message wasn't so misleading. 

{% highlight text linenos %}
#bad version
QMAKE_UIC + = -L ../bin/plugin
#good version
QMAKE_UIC += -L ../bin/plugin
{% endhighlight %}

So if you see this error look for operators that might have a space inserted in the middle. Hope this will save somebody's time.