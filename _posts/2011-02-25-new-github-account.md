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
I’ve opened a [Github account][1] where i keep some stuff I&#8217;ve been playing with recently. At the moment is mostly related to doing DDD with CQRS and Event Sourcing. 

I’ve created a [sample project][2] to demonstrate the concepts. Here is a short description of the sample:

Sample project demonstrating CQRS & Event Sourcing   
================================================= 

The sample uses the following projects from Jonathan Oliver (<https://github.com/joliver)>

* EventStore   
* NanoServiceBus   
* CommonDomain   
* StorageAccess 

For more information on CQRS and Event Sourcing <http://cqrsinfo.com/>

A short description of the projects in the sample: 

a. Write Side 

&#160;&#160;&#160; 1. Sample.DomainModel   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; the domain model for the sample   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; this model is persisted using event sourcing and does not need to handle reads ( queries )   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; since they are done on the read side.   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; the resulting event stream is the "source of truth" data. 

&#160;&#160;&#160; 2. Sample.AppService   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; the handlers for the commands our domain knows to execute 

&#160;&#160;&#160; 3. Sample.AppServiceHost   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; infrastructure for wiring up the command handlers to NanoServiceBus   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; this is the actual instance of the service that needs to be running for the write side to process   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; commands. 

b. Read Side 

&#160;&#160;&#160; 1. Sample.ReadModel   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; the read model on witch queries are executed   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; this model should be mapped as close as possible to the views   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; this model can be regenerated from the event stream 

&#160;&#160;&#160; 2. Sample.Denormalizer   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; the event handlers for the domain events that are published by the DomainModel on the Write Side   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; this handlers keep the read model in sync with the event stream   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; a better name for it is welcome 

&#160;&#160;&#160; 3. Sample.DenormalizerHost   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; infrastructure for wiring up the event handlers to NanoServiceBus   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; this process needs to run for the read side to be updated. 

c. Infrastructure   
&#160;&#160;&#160; 1. Sample.Messages   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; definitions for commands and events that are handled or published by the Domain Model   
&#160;&#160;&#160; 2. Sample.Client.Web   
&#160;&#160;&#160;&#160;&#160;&#160;&#160; &#8211; ASP.NET MVC3 application that demonstrates how to integrate the Write Side & the Read Side in an application. 

TODO   
==== 

- Add more complex objects to the domain   
- Review transaction management on integration points   
- use ConfOrm for mapping the read model

I’d be glad if other shared their comments on the code there.

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:C16BAC14-9A3D-4c50-9394-FBFEF7A93539:64aaa9f9-997c-4281-8427-c10bb319473a" class="wlWriterSmartContent">
  <a href="http://www.dotnetkicks.com/kick/?url=http://www.erata.net/net/new-github-account/"><img src="http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http://www.erata.net/net/new-github-account/" border="0" alt="kick it on DotNetKicks.com" /></a>
</div>

<div class="wlWriterHeaderFooter" style="margin:0px; padding:0px 0px 0px 0px;">
  <p>
    <br /> </div>

 [1]: https://github.com/etishor
 [2]: https://github.com/etishor/CQRSEventSourcingSample