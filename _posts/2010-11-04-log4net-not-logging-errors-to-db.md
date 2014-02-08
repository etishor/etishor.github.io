---
title: Log4Net not logging errors to db
author: Iulian Margarintescu
layout: post
permalink: /net/log4net-not-logging-errors-to-db/
views:
  - 672
dsq_thread_id:
  - 316198881
categories:
  - .NET
tags:
  - adonet appender
  - atomicity
  - configuration section
  - database
  - error
  - errors
  - log message
  - log4net
  - logging errors
  - post
  - transaction
  - Unit Of Work
  - use
  - windows services
  - Work
---
This is a short post about logging errors to db with log4net.

I&#8217;ve recently been bitten by a bug or better said miss configuration of log4net. I&#8217;m using log4net with database logging and file logging in a project and i was wandering why there are no error messages logged in the db when they are logged in the log files configured.

At one point it hit me: Transactions. Good ol\` transactions. Distributed ones.

In this project i use a mix of [NServiceBus][1] based windows services and WCF services. They all use a variation of the [Unit Of Work][2] pattern to manage the atomicity of the operations. Since we use MSMQ messaging and NHibernate sessions they all fall under a distributed transaction. By default log4net AdoNet appender also uses a transaction to insert the log message in the db. And this transaction was enlisting in the ambient distributed transaction. Now in case of an error the unit of work get&#8217;s aborted and the transactions get rolled back. This also includes log4net&#8217;s transaction.

The solution is simple after you figure out the problem. Just add <useTransactions value=&#8221;false&#8221;/> in the configuration section for the ADONetAppender. Ex:

<pre class="brush:xml">&lt;appender name="MySQLAppender" type="log4net.Appender.ADONetAppender"&gt;
    &lt;usetransactions value="false"/&gt;
    &lt;!-- rest of the configuration --&gt;
&lt;/appender&gt;</pre>

 [1]: http://www.nservicebus.com "NServiceBus"
 [2]: http://martinfowler.com/eaaCatalog/unitOfWork.html "Unit Of Work"