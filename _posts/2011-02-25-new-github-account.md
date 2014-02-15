---
title: New Github account
author: Iulian Margarintescu
layout: post
permalink: /net/new-github-account/
views:
  - 592
dsq_thread_id:
  - 307927148
categories:
  - .NET
tags:
  - ASP.NET
  - cqrs
  - ddd
  - domain model
  - Event
  - event sourcing
  - git
  - Github
  - jonathan oliver
  - model
  - sample
  - short description
  - Sourcing
  - stream
  - witch
---
I’ve opened a [Github account][1] where i keep some stuff I've been playing with recently. At the moment is mostly related to doing DDD with CQRS and Event Sourcing. 

I’ve created a [sample project][2] to demonstrate the concepts. Here is a short description of the sample:

### Sample project demonstrating CQRS & Event Sourcing   

The sample uses the following projects from [Jonathan Oliver][3]

* EventStore   
* NanoServiceBus   
* CommonDomain   
* StorageAccess 

For more information on CQRS and Event Sourcing <http://cqrsinfo.com/>

A short description of the projects in the sample: 

a. Write Side 

    1. Sample.DomainModel   
        - the domain model for the sample   
        - this model is persisted using event sourcing and does not need to handle reads ( queries )   
        since they are done on the read side.   
        - the resulting event stream is the "source of truth" data. 

    2. Sample.AppService   
        - the handlers for the commands our domain knows to execute 

    3. Sample.AppServiceHost   
        - infrastructure for wiring up the command handlers to NanoServiceBus   
        - this is the actual instance of the service that needs to be running for the write side to process   
        commands. 

b. Read Side 

    1. Sample.ReadModel   
        - the read model on witch queries are executed   
        - this model should be mapped as close as possible to the views   
        - this model can be regenerated from the event stream 

    2. Sample.Denormalizer   
        - the event handlers for the domain events that are published by the DomainModel on the Write Side   
        - this handlers keep the read model in sync with the event stream   
        - a better name for it is welcome 

    3. Sample.DenormalizerHost   
        - infrastructure for wiring up the event handlers to NanoServiceBus   
        - this process needs to run for the read side to be updated. 

c. Infrastructure

    1. Sample.Messages   
        - definitions for commands and events that are handled or published by the Domain Model   
    2. Sample.Client.Web   
        - ASP.NET MVC3 application that demonstrates how to integrate the Write Side & the Read Side in an application. 

### TODO   

- Add more complex objects to the domain   
- Review transaction management on integration points   
- use ConfOrm for mapping the read model

I’d be glad if other shared their comments on the code there.

 [1]: https://github.com/etishor
 [2]: https://github.com/etishor/CQRSEventSourcingSample
 [3]: https://github.com/joliver