---
title: Testing Windows Live Writer
author: Iulian Margarintescu
layout: post
permalink: /news/testing-windows-live-writer/
views:
  - 412
dsq_thread_id:
  - 306768982
categories:
  - News
tags:
  - blog tool
  - code snippet
  - courier
  - gt 5
  - gt number
  - int
  - live writer
  - param
  - param name
  - public static void
  - summary
  - Testing
  - wordpress
---
It’s not that writing posts in WordPress is hard, but i just have a feeling that Live Writer might make me write more often <img src="http://www.erata.net/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley" /> . Writing these first lines seems to be a pleasant experience so far. And i would really like to publish some stuff about things that i have been working with and that i found to be quite interesting ... [NServiceBus][1] , [NHibernate][2] and other goodies ... will see how it goes.

Code snippet test

<div class="csharpcode">
  <pre><span class="lnum">   1:  </span><span class="rem">/// <summary></span></pre>
  
  <pre><span class="lnum">   2:  </span><span class="rem">/// Execute action <paramref name="numberOfTimes"/> times</span></pre>
  
  <pre><span class="lnum">   3:  </span><span class="rem">/// </summary></span></pre>
  
  <pre><span class="lnum">   4:  </span><span class="rem">/// <param name="numberOfTimes">Number of times to execute action</param></span></pre>
  
  <pre><span class="lnum">   5:  </span><span class="rem">/// <param name="action">Action to execute</param></span></pre>
  
  <pre><span class="lnum">   6:  </span><span class="kwrd">public</span> <span class="kwrd">static</span> <span class="kwrd">void</span> Times(<span class="kwrd">this</span> <span class="kwrd">int</span> numberOfTimes, Action<<span class="kwrd">int</span>> action)</pre>
  
  <pre><span class="lnum">   7:  </span>{</pre>
  
  <pre><span class="lnum">   8:  </span>      Check.NotNull(action, <span class="str">"action"</span>);</pre>
  
  <pre><span class="lnum">   9:  </span>      <span class="kwrd">for</span> (<span class="kwrd">int</span> i = 0; i < numberOfTimes; i++)</pre>
  
  <pre><span class="lnum">  10:  </span>          action(i);</pre>
  
  <pre><span class="lnum">  11:  </span>}</pre>
  
  <pre><span class="lnum">  12:  </span> </pre>
</div>

<div class="wlWriterHeaderFooter" style="margin:0px; padding:0px 0px 0px 0px;">
  <p>
    <br /> </div>

 [1]: http://www.nservicebus.com
 [2]: http://nhforge.org/Default.aspx