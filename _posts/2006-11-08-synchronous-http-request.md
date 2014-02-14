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
  - 'Qt &amp; boost'
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

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw2">foreach</span> <span class="br0">&#40;</span> Request r , requestList <span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">if</span><span class="br0">&#40;</span>! runRequest<span class="br0">&#40;</span>r<span class="br0">&#41;</span> <span class="br0">&#41;</span> <span class="kw1">return</span> error;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

For writing something like this I would like to not split the algorithm across a set of slots. So I would like to have a possibility to run a request and wait for it to finish.

## Implementation

We start by creating a *SyncHTTP* class that is derived from QHttp:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw2">class</span> SyncHTTP: <span class="kw2">public</span> <a href="http://doc.trolltech.com/4.2/QHttp.html"><span class="kw5">QHttp</span></a><span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw2">Q_OBJECT</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// id of current request</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="kw4">int</span> requestID;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// error status of current request</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw4">bool</span> status;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// event loop used to block until request finished</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <a href="http://doc.trolltech.com/4.2/QEventLoop.html"><span class="kw5">QEventLoop</span></a> loop;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw2">public</span>:
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// structors</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        SyncHTTP<span class="br0">&#40;</span> <a href="http://doc.trolltech.com/4.2/QObject.html"><span class="kw5">QObject</span></a> * parent = <span class="nu0"></span> <span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        :<a href="http://doc.trolltech.com/4.2/QHttp.html"><span class="kw5">QHttp</span></a><span class="br0">&#40;</span>parent<span class="br0">&#41;</span>,requestID<span class="br0">&#40;</span><span class="nu0">-1</span><span class="br0">&#41;</span>,status<span class="br0">&#40;</span><span class="kw2">false</span><span class="br0">&#41;</span><span class="br0">&#123;</span><span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        SyncHTTP<span class="br0">&#40;</span> <span class="kw4">const</span> <a href="http://doc.trolltech.com/4.2/QString.html"><span class="kw5">QString</span></a> & hostName,quint16 port = <span class="nu0">80</span>, <a href="http://doc.trolltech.com/4.2/QObject.html"><span class="kw5">QObject</span></a> * parent = <span class="nu0"></span> <span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        :<a href="http://doc.trolltech.com/4.2/QHttp.html"><span class="kw5">QHttp</span></a><span class="br0">&#40;</span>hostName,port,parent<span class="br0">&#41;</span>,requestID<span class="br0">&#40;</span><span class="nu0">-1</span><span class="br0">&#41;</span>,status<span class="br0">&#40;</span><span class="kw2">false</span><span class="br0">&#41;</span><span class="br0">&#123;</span><span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw2">virtual</span> ~SyncHTTP<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#123;</span><span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

Now we need to implement the methods for *syncGet* and *syncPost* to mimic the *get* and *post* methods from QHttp:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        <span class="co1">/// send GET request and wait until finished</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw4">bool</span> syncGet <span class="br0">&#40;</span> <span class="kw4">const</span> <a href="http://doc.trolltech.com/4.2/QString.html"><span class="kw5">QString</span></a> & path, <a href="http://doc.trolltech.com/4.2/QIODevice.html"><span class="kw5">QIODevice</span></a> * to = <span class="nu0"></span> <span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">///connect the requestFinished signal to our finished slot</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="kw2">connect</span><span class="br0">&#40;</span><span class="kw3">this</span>,<span class="kw4">SIGNAL</span><span class="br0">&#40;</span>requestFinished<span class="br0">&#40;</span><span class="kw4">int</span>,<span class="kw4">bool</span><span class="br0">&#41;</span><span class="br0">&#41;</span>,
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        SLOT<span class="br0">&#40;</span>finished<span class="br0">&#40;</span><span class="kw4">int</span>,<span class="kw4">bool</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// start the request and store the requestID</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        requestID = get<span class="br0">&#40;</span>path, to <span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// block until the request is finished</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        loop.<span class="me1">exec</span><span class="br0">&#40;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// return the request status</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">return</span> status;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// send POST request and wait until finished</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="kw4">bool</span> syncPost <span class="br0">&#40;</span> <span class="kw4">const</span> <a href="http://doc.trolltech.com/4.2/QString.html"><span class="kw5">QString</span></a> & path,
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <a href="http://doc.trolltech.com/4.2/QIODevice.html"><span class="kw5">QIODevice</span></a> * data, <a href="http://doc.trolltech.com/4.2/QIODevice.html"><span class="kw5">QIODevice</span></a> * to = <span class="nu0"></span> <span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">///connect the requestFinished signal to our finished slot</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw2">connect</span><span class="br0">&#40;</span><span class="kw3">this</span>,<span class="kw4">SIGNAL</span><span class="br0">&#40;</span>requestFinished<span class="br0">&#40;</span><span class="kw4">int</span>,<span class="kw4">bool</span><span class="br0">&#41;</span><span class="br0">&#41;</span>,
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        SLOT<span class="br0">&#40;</span>finished<span class="br0">&#40;</span><span class="kw4">int</span>,<span class="kw4">bool</span><span class="br0">&#41;</span><span class="br0">&#41;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// start the request and store the requestID</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        requestID = post<span class="br0">&#40;</span>path, data , to <span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// block until the request is finished</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        loop.<span class="me1">exec</span><span class="br0">&#40;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="co1">/// return the request status</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">return</span> status;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw4">bool</span> syncPost <span class="br0">&#40;</span> <span class="kw4">const</span> <a href="http://doc.trolltech.com/4.2/QString.html"><span class="kw5">QString</span></a> & path,
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw4">const</span> QByteArray& data, <a href="http://doc.trolltech.com/4.2/QIODevice.html"><span class="kw5">QIODevice</span></a> * to = <span class="nu0"></span> <span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// create io device from QByteArray</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <a href="http://doc.trolltech.com/4.2/QBuffer.html"><span class="kw5">QBuffer</span></a> buffer;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        buffer.<span class="me1">setData</span><span class="br0">&#40;</span>data<span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">return</span> syncPost<span class="br0">&#40;</span>path,&buffer,to<span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="br0">&#125;</span>
      </div>
    </li>
  </ol>
</div>

The *syncGet* and *syncPost* functions both use the same strategy. They connect the *requestFinished* signal from QHttp to a *finished* slot, they ask the QHttp object to perform the request. The QHttp's get/post method returns immediately with the request id that has been assigned to the current request. Now the nice part: After asking the QHttp object to perform the request we need to wait for it to finish, so we create an event loop and start it. The main event loop is now blocked and we are nor risking being called aging while executing the request. The blocking code is not using tricks like sleep or infinite loops so we don't end up using 100% CPU or wasting time sleeping. Now we just need to implement the *finished* slot to save the result and exit the loop:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        <span class="kw2">protected</span> <span class="kw2">slots</span>:
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw2">virtual</span> <span class="kw4">void</span> finished<span class="br0">&#40;</span><span class="kw4">int</span> idx, <span class="kw4">bool</span> err<span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// check to see if it's the request we made</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="kw1">if</span><span class="br0">&#40;</span>idx!=requestID<span class="br0">&#41;</span> <span class="kw1">return</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// set status of the request</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        status = !err;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// end the loop</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        loop.<span class="kw3">exit</span><span class="br0">&#40;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

Now this slot first checks to see if it's being called for our request and returns if not, after that saves the error status returned by the QHttp object and exits the loop. The execution now will return in the *syncGet/syncPost* function that will return the status to the caller.

## Testing

Ok, let's write a small test application. Writing real automated test cases for this kind of class is possible but not trivial so will just write a small test app.

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co2">#include</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co2">#include</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co2">#include "synchttp.h"</span>
      </div>
    </li>
  </ol>
</div>

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        <span class="kw4">int</span> main<span class="br0">&#40;</span><span class="kw4">int</span> argc, <span class="kw4">char</span> *argv<span class="br0">&#91;</span><span class="br0">&#93;</span><span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <a href="http://doc.trolltech.com/4.2/QApplication.html"><span class="kw5">QApplication</span></a> a<span class="br0">&#40;</span>argc,argv<span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">/// create object</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        SyncHTTP h<span class="br0">&#40;</span><span class="st0">"www.google.com"</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="co1">///prepare output buffer</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <a href="http://doc.trolltech.com/4.2/QBuffer.html"><span class="kw5">QBuffer</span></a> getOutput;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        h.<span class="me1">syncGet</span><span class="br0">&#40;</span> <span class="st0">"/search?q=erata.net"</span>,&getOutput <span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        qDebug<span class="br0">&#40;</span><span class="br0">&#41;</span> < < getOutput.<span class="me1">data</span><span class="br0">&#40;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <a href="http://doc.trolltech.com/4.2/QByteArray.html"><span class="kw5">QByteArray</span></a> data<span class="br0">&#40;</span><a href="http://doc.trolltech.com/4.2/QString.html"><span class="kw5">QString</span></a><span class="br0">&#40;</span><span class="st0">"q=data"</span><span class="br0">&#41;</span>.<span class="me1">toLatin1</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <a href="http://doc.trolltech.com/4.2/QBuffer.html"><span class="kw5">QBuffer</span></a> postOutput;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        h.<span class="me1">syncPost</span><span class="br0">&#40;</span> <span class="st0">"/search"</span>,data, &postOutput<span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        qDebug<span class="br0">&#40;</span><span class="br0">&#41;</span> < < postOutput.<span class="me1">data</span><span class="br0">&#40;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">return</span> <span class="nu0"></span>;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

Now in conclusion i would like to say that i recommend using this class only when you simply need to perform a request and know if it was successful or not. On any other case, where you need to do more complicated things learn to use Qt's event driven programming model.

Download <a title="Source File for SyncHTTP" rel="nofollow" href="http://www.erata.net/wp-content/uploads/File/synchttp.h">synchttp.h</a>

PS: this is my first programming HOWTO so be merciful <img src="http://www.erata.net/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley" /> 

**The code and ideas from this article can be used without any license restrictions. You can consider it under [BSD][1],[MIT][2],[BSL][3] or whatever suites you best. Providing my name or a link to this website would be nice but is not required.**

 [1]: http://opensource.org/licenses/bsd-license.php "BSD license"
 [2]: http://opensource.org/licenses/mit-license.php
 [3]: http://opensource.org/licenses/bsl1.0.html