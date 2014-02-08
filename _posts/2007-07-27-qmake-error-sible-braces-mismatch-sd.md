---
title: 'qmake error:  sible braces mismatch %s:%d'
author: Iulian Margarintescu
layout: post
permalink: /qt-boost/qmake-error-sible-braces-mismatch-sd/
autometa:
  - qmake solved received showed decided error sible braces mismatch qt project scope
views:
  - 1103
head_meta:
  - name="keywords" content=""
ratings_users:
  - 0
ratings_score:
  - 0
ratings_average:
  - 0
dsq_thread_id:
  - 315344963
categories:
  - 'Qt &amp; boost'
tags:
  - block
  - braces
  - error
  - Googling
  - kdevelop
  - middle hope
  - mismatch
  - parser generator
  - qmake
  - result
  - uic
  - unix
  - windows
---
I&#8217;ve just received this error with qmake on windows. Googling for it showed no results so after i&#8217;ve solved the problem i decided to put it here.  
<!--more-->

The error was near a conditional block like:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        unix{
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        }
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        win32{
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        &nbsp; QMAKE_UIC+ = -L ../bin/plugin
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        }
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

&#8230;after searching for unclosed/missing braces for a while without any result and comparing svn versions i&#8217;ve finally found the problem. On the QMAKE_UIC linke instead of &#8220;+=&#8221; somehow ( probably kdevelop&#8217;s qmake parser/generator ) got saved as &#8220;+ =&#8221; (notice the space). The problem would have been much easier to solve if the error message wasn&#8217;t so misleading. 

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        #bad version
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        QMAKE_UIC + = -L ../bin/plugin
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        #good version
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        QMAKE_UIC += -L ../bin/plugin
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
  </ol>
</div>

So if you see this error look for operators that might have a space inserted in the middle. Hope this will save somebody&#8217;s time.