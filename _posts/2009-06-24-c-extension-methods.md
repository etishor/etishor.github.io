---
title: 'C# Extension Methods'
author: Iulian Margarintescu
layout: post
permalink: /net/c-extension-methods/
views:
  - 480
dsq_thread_id:
  - 312530825
categories:
  - .NET
tags:
  - .NET
  - ASP.NET
  - c extension
  - c++
  - class
  - code
  - extension methods
  - html
  - 'null'
  - NullReferenceException
  - public static string
  - static class
  - static method
  - static methods
  - syntax sugar
  - TestClass
  - world test
---
After a few months I've enjoyed the newly ( or not ) added extension methods in 3.0 yesterday i discovered that one of the assumptions I've made about them was false. My assumption was that if the instance on witch you call an extension method is NULL you would get a NullReferenceException. Turns out it's not the case and you can call the extension method on a null reference.

Thinking about it later, it makes sense. In reality extension methods are nothing more than syntax sugar for calling static methods. And since too much sugar can hurt extension methods might do the same thing if not properly used. The code speaks pretty much for itself.

The class used as example:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">public</span> <span class="kw4">class</span> TestClass
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; <span class="co1">//existing method defined on the class</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp; &nbsp; <span class="kw1">public</span> <span class="kw4">string</span> Hello<span class="br0">&#40;</span><span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">return</span> <span class="st0">"Hello"</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

Static class defining extension method

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">public</span> <span class="kw1">static</span> <span class="kw4">class</span> ExtensionContainer
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; <span class="co1">//the extension method defined as static</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp; &nbsp; <span class="kw1">public</span> <span class="kw1">static</span> <span class="kw4">string</span> NewHello<span class="br0">&#40;</span><span class="kw1">this</span> TestClass instance<span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">return</span> <span class="st0">"World"</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

Test code:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw4">class</span> Program
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; <span class="kw1">static</span> <span class="kw1">void</span> Main<span class="br0">&#40;</span><span class="kw4">string</span><span class="br0">&#91;</span><span class="br0">&#93;</span> args<span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp; &nbsp; <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="co1">// declare and create a test instance</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; TestClass first = <a href="http://www.google.com/search?q=new+msdn.microsoft.com"><span class="kw3">new</span></a> TestClass<span class="br0">&#40;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; Console.<span class="me1">WriteLine</span><span class="br0">&#40;</span>first.<span class="me1">Hello</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span>; <span class="co1">//outputs: "Hello"</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="co1">//call the extension method as a normal method</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; Console.<span class="me1">WriteLine</span><span class="br0">&#40;</span>first.<span class="me1">NewHello</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span>; <span class="co1">// outputs: "World"</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="co1">// declare a null reference</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; TestClass second = <span class="kw1">null</span>;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">try</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span class="co1">//calling a method on a null reference throws</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Console.<span class="me1">WriteLine</span><span class="br0">&#40;</span>second.<span class="me1">Hello</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span>; <span class="co1">//will throw Null Reference exception</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">catch</span> <span class="br0">&#40;</span>NullReferenceException<span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Console.<span class="me1">WriteLine</span><span class="br0">&#40;</span><span class="st0">"NullReference calling Hello"</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">try</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; <span class="co1">//calling an extension method does not throw</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Console.<span class="me1">WriteLine</span><span class="br0">&#40;</span>second.<span class="me1">NewHello</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span>; <span class="co1">//will not throw</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="kw1">catch</span> <span class="br0">&#40;</span>NullReferenceException<span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Console.<span class="me1">WriteLine</span><span class="br0">&#40;</span><span class="st0">"NullReference calling NewHello"</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; <span class="co1">//the next two calls are exactly the same</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; Console.<span class="me1">WriteLine</span><span class="br0">&#40;</span>second.<span class="me1">NewHello</span><span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; &nbsp; &nbsp; Console.<span class="me1">WriteLine</span><span class="br0">&#40;</span>ExtensionContainer.<span class="me1">NewHello</span><span class="br0">&#40;</span>second<span class="br0">&#41;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp; &nbsp; &nbsp; &nbsp; Console.<span class="me1">ReadKey</span><span class="br0">&#40;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp; &nbsp; <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
  </ol>
</div>

Even now is still consider extension methods as a very valuable addition to the language, but you just need to understand exactly what they are. The wrong assumption about calling them on a null reference can affect some design choices you may take where in case of a null reference you want an exception to be thrown and no other method called.

Also using them when appropriate the ability to call extension methods on null references might help you write more readable code by moving the null check in the extension method. This can be particularly useful on writing extension methods that are supposed to be used in Views in ASP.NET MVC where moving the null check in the method leaves the the view's html code easier to read an understand.

One conclusion that I've drown from this is that in an extension method you should either check for null and throw NullReferenceException if null is not a valid value or handle null appropriately.

PS: Is it visible that the last 6+ months I've been working with C# and not C++ ? <img src="http://www.erata.net/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley" /> 

[<img src="http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http://www.erata.net/net/c-extension-methods/" border="0" alt="kick it on DotNetKicks.com" />][1]

 [1]: http://www.dotnetkicks.com/kick/?url=http://www.erata.net/net/c-extension-methods/