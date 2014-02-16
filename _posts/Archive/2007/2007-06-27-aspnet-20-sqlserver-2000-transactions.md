---
title: ASP.NET 2.0 and SQLServer 2000 Transactions
layout: post
comments: true
category: NET
---
The new ADO.NET 2.0 and it's typed datasets and table adapters are very useful tools for creating a DAL in you application. Still when working with SQLServer 2000 implementing transactions is a bit tricky. 

You can use TransactionScope but is seems to be designed for SQLServer 2005 as it will promote all transactions to distributed on SQLServer 2000 witch is a performance problem but also a bigger problem if your hosting provider does not provide distributed transactions. I've found a few solutions on the net some using reflection some using partial classes to add a method to the table adapter. Since i don't like using reflection unless i absolutely have to I've come up with a solution using partial classes but trying to minimize the code you have to write for each table adapter and also trying to make is less error prone.

The new ADO.NET 2.0 and it's typed datasets and table adapters are very useful tools for creating a DAL in you application. Still when working with SQLServer 2000 implementing transactions is a bit tricky. 

First we define an interface witch the table adapters will implement:

{% highlight c# linenos %}
public interface ITransactionable
{
  SqlConnection SqlConnection {get;set;}
  SqlCommand[] Commands {get;}
}
{% endhighlight %}

Now define a helper class that will manage all transaction operations.

{% highlight c# linenos %}
public class TransactionHelper
{
  public static SqlTransaction BeginTransaction(ITransactionable adapter)
  {
    return BeginTransaction(adapter,IsolationLevel.ReadUncommitted);
  }

  public static SqlTransaction BeginTransaction(ITransactionable adapter, IsolationLevel lvl)
  {
    SqlConnection con = adapter.SqlConnection;
    if (con.State == ConnectionState.Closed)
    con.Open();
    SqlTransaction trans = con.BeginTransaction(lvl);
    SetTransaction(adapter, trans);
    return trans;
  }
  
  public static void SetTransaction(ITransactionable adapter, SqlTransaction trans)
  {
    foreach (SqlCommand com in adapter.Commands)
    com.Transaction = trans;
    adapter.SqlConnection = trans.Connection;
  }
}
{% endhighlight %}

Now for each table adapter that we need to use transactions:

{% highlight c# linenos %}
public partial class DemoTableAdapter : ITransactionable
{
  public SqlConnection SqlConnection { get { return Connection; } set { Connection = value; } }
  public SqlCommand[] Commands { get { return CommandCollection; } }
}
{% endhighlight %}

As you can see the code needed for each table adapter is minimal and only requires copy-pasting and changing the name of the class. If c# would support c-style macros this could be reduced to a single line of code. To use the TransactionHelper class:

{% highlight c# linenos %}
....
SqlTransaction transaction=null;
try{
  using(DemoTableAdapter adapter = new DemoTableAdapter())
  {
    transaction = TransactionHelper.BeginTransaction(adapter);
  .... operations with adapter ....
  }
  using(OtherTableAdapter adapter = new OtherTableAdapter ())
  {
    TransactionHelper.SetTransaction(adapter,transaction);
  .... operations with adapter ....
  }
  transaction.Commit();
}
catch(Exception x)
{
  transaction.Rollback();
  ...handle error....
}
finally 
{
  transaction.Dispose();
}
{% endhighlight %}

Hope it helps.