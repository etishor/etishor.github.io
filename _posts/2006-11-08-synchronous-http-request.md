---
title: Qt4 Synchronous HTTP Request
author: Iulian Margarintescu
layout: post
permalink: /qt-boost/synchronous-http-request/
autometa:
  - qhttp request synchttp http qt synchronous asynchronous get post event loop signal slot wrapper
views:
  - 7498
head_meta:
  - name="keywords" content=""
ratings_users:
  - 0
ratings_score:
  - 0
ratings_average:
  - 0
dsq_thread_id:
  - 306768878
categories:
  - 'Qt & boost'
tags:
  - API
  - bsd
  - class
  - const
  - driven operations
  - driven programming
  - error
  - http
  - int
  - MIT
  - ping
  - Programming
  - protected
  - public
  - qstring
  - qt
  - reque
  - request
  - return
  - return error
  - solution
  - status
  - virtual
  - www.google.com
---
## Problem

Creating a wrapper over QHttp that can perform GET and POST requests in a synchronous way.

<!--more-->

## Solution

In Qt4 QHttp can perform HTTP requests, but the API only allows asynchronous requests. This means that you need to specify a set of slots to handle the signals that can be emitted while the request if performed. Now don't get me wrong, this is event driven programming and most of the times is the right way to go especially when writing a Qt based application.  The cases when event driven operations prove difficult to use are rare and usually can be programmed using events.

Recently I’ve developed a small application that had to do a number of requests in a specified order and show a message if an error was to occur. The algorithm was simple:

{% highlight c++ linenos %}
foreach ( Request r , requestList )
  if(! runRequest(r) ) return error;
{% endhighlight %}


For writing something like this I would like to not split the algorithm across a set of slots. So I would like to have a possibility to run a request and wait for it to finish.

## Implementation

We start by creating a *SyncHTTP* class that is derived from QHttp:

{% highlight c++ linenos %}
class SyncHTTP: public QHttp{
Q_OBJECT
/// id of current request
int requestID;
/// error status of current request
bool status;
/// event loop used to block until request finished
QEventLoop loop; 

public:
/// structors
SyncHTTP( QObject * parent = 0 )
:QHttp(parent),requestID(-1),status(false){}

SyncHTTP( const QString & hostName,quint16 port = 80, QObject * parent = 0 )
:QHttp(hostName,port,parent),requestID(-1),status(false){}

virtual ~SyncHTTP(){}
{% endhighlight %}

Now we need to implement the methods for *syncGet* and *syncPost* to mimic the *get* and *post* methods from QHttp:

{% highlight c++ linenos %}/// send GET request and wait until finished
bool syncGet ( const QString & path, QIODevice * to = 0 )
{
///connect the requestFinished signal to our finished slot
connect(this,SIGNAL(requestFinished(int,bool)),
SLOT(finished(int,bool)));
/// start the request and store the requestID
requestID = get(path, to );
/// block until the request is finished
loop.exec();
/// return the request status
return status;
}
/// send POST request and wait until finished
bool syncPost ( const QString & path,
QIODevice * data, QIODevice * to = 0 )
{
///connect the requestFinished signal to our finished slot
connect(this,SIGNAL(requestFinished(int,bool)),
SLOT(finished(int,bool)));
/// start the request and store the requestID
requestID = post(path, data , to );
/// block until the request is finished
loop.exec();
/// return the request status
return status;
}
bool syncPost ( const QString & path,
const QByteArray& data, QIODevice * to = 0 )
{
/// create io device from QByteArray
QBuffer buffer;
buffer.setData(data);
return syncPost(path,&buffer,to);
}
{% endhighlight %}


The *syncGet* and *syncPost* functions both use the same strategy. They connect the *requestFinished* signal from QHttp to a *finished* slot, they ask the QHttp object to perform the request. The QHttp's get/post method returns immediately with the request id that has been assigned to the current request. Now the nice part: After asking the QHttp object to perform the request we need to wait for it to finish, so we create an event loop and start it. The main event loop is now blocked and we are nor risking being called aging while executing the request. The blocking code is not using tricks like sleep or infinite loops so we don't end up using 100% CPU or wasting time sleeping. Now we just need to implement the *finished* slot to save the result and exit the loop:

{% highlight c++ linenos %}protected slots:
virtual void finished(int idx, bool err)
{
/// check to see if it's the request we made
if(idx!=requestID) return;
/// set status of the request
status = !err;
/// end the loop
loop.exit();
}
{% endhighlight %}

Now this slot first checks to see if it's being called for our request and returns if not, after that saves the error status returned by the QHttp object and exits the loop. The execution now will return in the *syncGet/syncPost* function that will return the status to the caller.

## Testing

Ok, let's write a small test application. Writing real automated test cases for this kind of class is possible but not trivial so will just write a small test app.

{% highlight c++ linenos %}
#include
#include
#include "synchttp.h"

int main(int argc, char *argv[])
{
QApplication a(argc,argv);
/// create object
SyncHTTP h("www.google.com");

///prepare output buffer
QBuffer getOutput;
h.syncGet( "/search?q=erata.net",&getOutput );
qDebug() << getOutput.data();

QByteArray data(QString("q=data").toLatin1());
QBuffer postOutput;

h.syncPost( "/search",data, &postOutput);

qDebug() << postOutput.data();

return 0;
}
{% endhighlight %}


Now in conclusion i would like to say that i recommend using this class only when you simply need to perform a request and know if it was successful or not. On any other case, where you need to do more complicated things learn to use Qt's event driven programming model.

Download <a title="Source File for SyncHTTP" rel="nofollow" href="http://www.erata.net/wp-content/uploads/File/synchttp.h">synchttp.h</a>

PS: this is my first programming HOWTO so be merciful <img src="http://www.erata.net/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley" /> 

**The code and ideas from this article can be used without any license restrictions. You can consider it under [BSD][1],[MIT][2],[BSL][3] or whatever suites you best. Providing my name or a link to this website would be nice but is not required.**

 [1]: http://opensource.org/licenses/bsd-license.php "BSD license"
 [2]: http://opensource.org/licenses/mit-license.php
 [3]: http://opensource.org/licenses/bsl1.0.html