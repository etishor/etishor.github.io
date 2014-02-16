---
title: NServiceBus with NHibernate and MySQL
layout: post
comments: true
dsq_thread_id: 125 http://www.erata.net/net/nservicebus-with-nhibernate-and-mysql/
category: NET
---
In the last project I have been working i finally got a chance to design and implement a solution based on [NServiceBus][1] and [NHibernate][2], two tools I’ve been watching for a while but never got a chance to play with in more than sample applications. For some external reasons I've been forced to use [MySQL][3] as a database server in this project.

So basically I’m using [NServiceBus][1] to provide reliable communication between the involved components and NHibernate to do the persistence of the domain objects used by the components. Up to this point the whole design of the solution looks good, with minimal effort i have reliable, fault tolerant services that are ready to do their job.

Now i start implementing the details and get to the point where MySQL comes into play. I must say, it has surprised me... both ways.

The good thing is that after careful tuning, where my previous UNIX experience had a very important role, the database is able to handle the amounts of data that i plan to throw at it. Also it surprised me that some pretty complex queries run a lot faster than expected.

Then the bad things started to show up. The hardest to debug was that updating an indexed column from multiple parallel transactions causes deadlocks witch cause transactions to be aborted. Of course this only happens at high loads. It was not hard to avoid this after i found out what the problem was ... but still after this i had a feeling of working with something that might not be as reliable as expected.

After that the [MySQL .NET connector][4] dropped the bomb on me: [Distributed Transactions are not supported][5]. Ok, they are not supported but why the hell does the connector throws an exception when used in a distributed transaction? I can understand that i can’t rely on the MySQL transaction being enlisted in the Distributed Transaction (DT) and that i have to handle that myself but not being able to use the connector AT ALL under a DT was unexpected. At this point i see only one solution: [grab the source][6] for the connector and modify the part that checks if a DT is present and just ignore it. Turns out this was very easy to do. If anyone is interested in this change in the connector i can provide more details.

Now i need to find a way of having a [NHibernate][7] ISession and a ITransaction per [NServiceBus][1] message handler.

The first approach was something similar to what [Andreas Öhlund describes in this blog post][8]. The only problem is that IMessageModule implementations in NServiceBus 2.0 are singletons and that was a problem because i need to store the ITransaction to commit or rollback at the end of the message handler. If in the next version of the NServiceBus there will be a way to have some message handler “wrapper” that it could work.

My solution was to use a base abstract class for the massage handlers. So instead of just implementing IMessageHandler<T> now i derive from this base class. The code below should speak for itself:

{% highlight c# linenos %}
/// <summary>
/// Base class for message handlers.
/// Manages the unit of work required for handling the message.
/// </summary>
public abstract class MessageHandler<T> : IMessageHandler<T>
          where T : IMessage
{
    /// <summary>
    /// The injected unit of work implementation.
    /// </summary>
    private readonly IUnitOfWork unitOfWork;

    public MessageHandler(IUnitOfWork unitOfWork)
    {
        this.unitOfWork = unitOfWork;
    }

    /// <summary>
    /// Concrete handlers must implement this method.
    /// </summary>
    public abstract void HandleMessage(T message);

    public void Handle(T message)
    {
        try
        {
            HandleMessage(message);
            unitOfWork.Complete();
        }
        catch
        {
            unitOfWork.Abort();
            throw;
        }
        finally
        {
            unitOfWork.Dispose();
        }
    }
}
{% endhighlight %}

The implementation for the IUnitOfWork is in this case very simple, providing only the creation of the session and the transaction and the required operations. Since the unit of work is created per handler and the handlers don’t use other threads to do the work I don’t need to worry about making it thread safe.

{% highlight c# linenos %}
public class MessageUnitOfWork: IUnitOfWork
{
    private readonly ISessionFactory factory;
    private readonly ITransaction transaction;
    private readonly ISession session;

    public MessageUnitOfWork(ISessionFactory factory)
    {
        this.factory = factory;
        session = factory.OpenSession();
        CurrentSessionContext.Bind(session);
        transaction = session.BeginTransaction();
    }

    public void Complete()
    {
        transaction.Commit();
    }

    public void Abort()
    {
        transaction.Rollback();
    }

    public void Dispose()
    {
        transaction.Dispose();
        CurrentSessionContext.Unbind(factory);
        session.Dispose();
        GC.SuppressFinalize(this);
    }
}
{% endhighlight %}

There is still one small problem. If the handler finishes it’s work without and exception and the mysql transaction is committed BUT an exception is thrown by the bus when committing the distributed transaction the MySQL transaction is not rolled back. But i realized that this only means that the same message might be sent again to the handler and that the handlers in general should handle this logical case since whoever sent the message is free to send it multiple times.

Since I’ve got this solution working it has handled a few millions of messages and there have been crashes and transaction that got rolled back occasionally but after all the system is designed to be fault tolerant and it has proven it is. Also in all the cases the database remained in a consistent state, witch in the beginning i was not sure it will.

In the end i would like to thank the [NServiceBus][9] team ( mainly [Udi Dahan][10] and [Andreas Öhlund][11] ) witch was very responsive and helpful on the support mailing list. I can only hope to find the time to contribute a few ideas to the next release of NSB. Also i would like to thank the [NHibernate][7] team for the great product they created ( can’t wait for the 3.0 ).

 [1]: http://nservicebus.com/ "NServiceBus"
 [2]: http://nhforge.org/ "NHibernate"
 [3]: http://www.mysql.com/
 [4]: http://dev.mysql.com/downloads/connector/net/ "MySQL .NET connector"
 [5]: http://bugs.mysql.com/bug.php?id=37283 "Distributed Transactions bug"
 [6]: http://bazaar.launchpad.net/~mysql-clr-team/connectornet/6.2/files
 [7]: http://nhforge.org
 [8]: http://andreasohlund.blogspot.com/2010/02/nhibernate-session-management-in.html "Andreas Öhlund - Nhibernate Session Management"
 [9]: http://nservicebus.com/Community.aspx "NServiceBus Community"
 [10]: http://www.udidahan.com/ "Udi Dahan"
 [11]: http://andreasohlund.blogspot.com "Andreas Öhlund blog"