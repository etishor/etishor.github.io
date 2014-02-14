---
title: ASP.NET 2.0 and SQLServer 2000 Transactions
author: Iulian Margarintescu
layout: post
permalink: /net/aspnet-20-sqlserver-2000-transactions/
autometa:
  - datasets tools typed adapters table application creating ado.net sqlserver transaction distributed reflection
views:
  - 1450
head_meta:
  - name="keywords" content=""
ratings_users:
  - 0
ratings_score:
  - 0
ratings_average:
  - 0
dsq_thread_id:
  - 328628114
categories:
  - .NET
tags:
  - ADO.NET
  - ASP.NET
  - BeginTransaction
  - class
  - datasets
  - error
  - performance problem
  - public
  - public interface
  - public static void
  - return
  - solution
  - sql
  - SqlCommand
  - SqlConnection
  - sqlserver
  - sqlserver 2000
  - sqlserver 2005
  - transaction
  - transactions
  - type
---
The new ADO.NET 2.0 and it's typed datasets and table adapters are very useful tools for creating a DAL in you application. Still when working with SQLServer 2000 implementing transactions is a bit tricky. <!--more--> You can use TransactionScope but is seems to be designed for SQLServer 2005 as it will promote all transactions to distributed on SQLServer 2000 witch is a performance problem but also a bigger problem if your hosting provider does not provide distributed transactions. I've found a few solutions on the net some using reflection some using partial classes to add a method to the table adapter. Since i don't like using reflection unless i absolutely have to I've come up with a solution using partial classes but trying to minimize the code you have to write for each table adapter and also trying to make is less error prone.

  
First we define an interface witch the table adapters will implement:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">public</span> <span class="kw4">interface</span> ITransactionable
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        SqlConnection SqlConnection <span class="br0">&#123;</span>get;set;<span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        SqlCommand<span class="br0">&#91;</span><span class="br0">&#93;</span> Commands <span class="br0">&#123;</span>get;<span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
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

Now define a helper class that will manage all transaction operations.

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">public</span> <span class="kw4">class</span> TransactionHelper
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">public</span> <span class="kw1">static</span> SqlTransaction BeginTransaction<span class="br0">&#40;</span>ITransactionable adapter<span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">return</span> BeginTransaction<span class="br0">&#40;</span>adapter,IsolationLevel.<span class="me1">ReadUncommitted</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">public</span> <span class="kw1">static</span> SqlTransaction BeginTransaction<span class="br0">&#40;</span>ITransactionable adapter, IsolationLevel lvl<span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        SqlConnection con = adapter.<span class="me1">SqlConnection</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">if</span> <span class="br0">&#40;</span>con.<span class="me1">State</span> == ConnectionState.<span class="me1">Closed</span><span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        con.<span class="me1">Open</span><span class="br0">&#40;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        SqlTransaction trans = con.<span class="me1">BeginTransaction</span><span class="br0">&#40;</span>lvl<span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        SetTransaction<span class="br0">&#40;</span>adapter, trans<span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="kw1">return</span> trans;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">public</span> <span class="kw1">static</span> <span class="kw1">void</span> SetTransaction<span class="br0">&#40;</span>ITransactionable adapter, SqlTransaction trans<span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">foreach</span> <span class="br0">&#40;</span>SqlCommand com <span class="kw1">in</span> adapter.<span class="me1">Commands</span><span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        com.<span class="me1">Transaction</span> = trans;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        adapter.<span class="me1">SqlConnection</span> = trans.<span class="me1">Connection</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
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

Now for each table adapter that we need to use transactions:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">public</span> partial <span class="kw4">class</span> DemoTableAdapter : ITransactionable
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">public</span> SqlConnection SqlConnection <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        get <span class="br0">&#123;</span> <span class="kw1">return</span> Connection; <span class="br0">&#125;</span> set <span class="br0">&#123;</span> Connection = value; <span class="br0">&#125;</span> <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">public</span> SqlCommand<span class="br0">&#91;</span><span class="br0">&#93;</span> Commands <span class="br0">&#123;</span> get <span class="br0">&#123;</span> <span class="kw1">return</span> CommandCollection; <span class="br0">&#125;</span> <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
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

As you can see the code needed for each table adapter is minimal and only requires copy-pasting and changing the name of the class. If c# would support c-style macros this could be reduced to a single line of code. To use the TransactionHelper class:

<div class="dean_ch" style="white-space: wrap;">
  <ol>
    <li class="li1">
      <div class="de1">
        &nbsp;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        ....
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="me1">SqlTransaction</span> transaction=<span class="kw1">null</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">try</span><span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="kw1">using</span><span class="br0">&#40;</span>DemoTableAdapter adapter = <a href="http://www.google.com/search?q=new+msdn.microsoft.com"><span class="kw3">new</span></a> DemoTableAdapter<span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        transaction = TransactionHelper.<span class="me1">BeginTransaction</span><span class="br0">&#40;</span>adapter<span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        .... <span class="me1">operations</span> with adapter ....
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        <span class="kw1">using</span><span class="br0">&#40;</span>OtherTableAdapter adapter = <a href="http://www.google.com/search?q=new+msdn.microsoft.com"><span class="kw3">new</span></a> OtherTableAdapter <span class="br0">&#40;</span><span class="br0">&#41;</span><span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        TransactionHelper.<span class="me1">SetTransaction</span><span class="br0">&#40;</span>adapter,transaction<span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        .... <span class="me1">operations</span> with adapter ....
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        transaction.<span class="me1">Commit</span><span class="br0">&#40;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">catch</span><span class="br0">&#40;</span>Exception x<span class="br0">&#41;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        transaction.<span class="me1">Rollback</span><span class="br0">&#40;</span><span class="br0">&#41;</span>;
      </div>
    </li>
    
    <li class="li2">
      <div class="de2">
        ...<span class="me1">handle</span> error....
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="br0">&#125;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        <span class="kw1">finally</span> <span class="br0">&#123;</span>
      </div>
    </li>
    
    <li class="li1">
      <div class="de1">
        transaction.<span class="me1">Dispose</span><span class="br0">&#40;</span><span class="br0">&#41;</span>;
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

Hope it helps.