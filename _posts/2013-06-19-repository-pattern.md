---
title: The Repository Pattern
layout: post
dsq_thread_id:
  - 1412443637
category : Programming
tags:
  - nhibernate
  - patterns
  - practices
  - RavenDb
  - repository
---
In the context of a question asked on the [RavenDb mailing list][1] I've decided to share my thoughts on the ***Repository Pattern***.

It is a common mistake to consider the Repository pattern a generic "good practice", without actually considering what it is good about it.

According to [Martin Fowler's P of EAA][2] a Repository

>  "Mediates between the domain and data mapping layers using a collection-like interface for accessing domain objects."

This makes a lot of sense, and even if you are dealing with an [anemic domain model][3], you don't want to have data mapping code mixed in with your domain object code. The key here is ***"data mapping code"***. You don't want code that deals with the internal representation of your data storage (whatever your actual storage is) mixed with code that performs business operations on your objects.

Back in the day, when we used to write SQL code in our apps, it was essential that you implemented a layer that encapsulated the SQL queries and SQL mapping code from the rest of the application. With the technology choices available today, we rarely need to write SQL code or SQL mapping code. Today we have [NHibernate][4], [Entity Framework][5], Linq2SQL, [RavenDb][6] etc.

Consider NHibernate - the session you open acts like an in-memory collection of domain objects, where you can query, fetch and add domain objects without needing to do any SQL specific stuff. The data-mapping and SQL stuff is encapsulated in your NHibernate mappings code.** The NHibernate session IS your repository**. It also acts as a Unit of Work, but that is a different pattern.

The same thing happens with RavenDb's client. It provides a "session" which encapsulates all the persistence details like http calls, (de)serialization, caching, concurrency, a lot of safety checks, while acting like an in-memory collection of domain objects. When you do session.Load() or session.Query(), you do this in a very similar way to what you would do when dealing with an in-memory collection of domain objects.** The RavenDB's session IS your repository**.

Do HNibernate or RavenDb sessions hide ALL the details of the persistence engine? No, but you don't want them to be hidden from you. Except for the most trivial applications, you can't ignore the way your data is persisted. You are going to need to handle relationships between the objects in your persistent store, you are going to need to handle concurrency problems and you are going to have to write efficient queries. All these concerns are application specific and no abstraction is going to be good enough to handle all your application's use cases. But the concept of session from NHibernate or Raven does provide an abstraction over the persistence details, while exposing to the developer ways in which efficient operations can be performed.

There is also a very strong resemblance between the session from RavenDb and the session from NHibernate, but that is probably because [Ayende][7] is (was? <img src="http://www.erata.net/wp-includes/images/smilies/icon_smile.gif" alt=":)" class="wp-smiley" /> ) one of the most active and knowledgeable developers and "evangelist" for NHibernate, and also because the session is a good model for a unit of work. Ayende also has some good posts about the [Repository pattern][8].

One question that always pops up in the context of the Repository pattern is Unit Testing. How do you mock the ISession from RavenDb or NHibernate. Well ... you don't. It's very hard to mock a non-trivial repository. Instead you "use the real thing", just in an in-memory form. RavenDb's echo-system provides an embedded, in-memory store that is perfect for running unit tests against the real store. NHibernate provides a SQLLite back-end which, while not perfect, can be used for fast tests against a real sql implementation.

In conclusion, before you start defining the IRepository which i know sounds like a cool "good practice", stop for a minute and check if you are not already using an abstraction over your persistence.

 [1]: https://groups.google.com/forum/#!topic/ravendb/Gfb6XTOWF0Q
 [2]: http://martinfowler.com/eaaCatalog/repository.html
 [3]: http://www.martinfowler.com/bliki/AnemicDomainModel.html
 [4]: http://nhforge.org/ "NHibernate"
 [5]: http://msdn.microsoft.com/en-us/data/ef.aspx "Entity Framework"
 [6]: http://ravendb.net/ "RavenDb"
 [7]: http://ayende.com/blog "Ayende's blog"
 [8]: http://ayende.com/blog/search?q=repository "Ayende's Repository posts"