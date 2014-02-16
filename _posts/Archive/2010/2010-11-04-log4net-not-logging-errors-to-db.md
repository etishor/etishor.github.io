---
title: Log4Net not logging errors to db
layout: post
comments: true
category: NET
---
This is a short post about logging errors to db with log4net.

I've recently been bitten by a bug or better said miss configuration of log4net. I'm using log4net with database logging and file logging in a project and i was wandering why there are no error messages logged in the db when they are logged in the log files configured.

At one point it hit me: Transactions. Good ol` transactions. Distributed ones.

In this project i use a mix of [NServiceBus][1] based windows services and WCF services. They all use a variation of the [Unit Of Work][2] pattern to manage the atomicity of the operations. Since we use MSMQ messaging and NHibernate sessions they all fall under a distributed transaction. By default log4net AdoNet appender also uses a transaction to insert the log message in the db. And this transaction was enlisting in the ambient distributed transaction. Now in case of an error the unit of work get's aborted and the transactions get rolled back. This also includes log4net's transaction.

The solution is simple after you figure out the problem. Just add <useTransactions value="false"/> in the configuration section for the ADONetAppender. Ex:

{% highlight xml linenos %}
<appender name="MySQLAppender" type="log4net.Appender.ADONetAppender">
    <usetransactions value="false"/>
    <!-- rest of the configuration -->
</appender>
{% endhighlight %}

 [1]: http://www.nservicebus.com "NServiceBus"
 [2]: http://martinfowler.com/eaaCatalog/unitOfWork.html "Unit Of Work"