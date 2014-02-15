---
title: .NET Serialization Choices
author: Iulian Margarintescu
layout: post
permalink: /net/net-serialization-choices/
views:
  - 802
dsq_thread_id:
  - 306769024
categories:
  - .NET
tags:
  - AJAX
  - BinaryFormatter
  - cqrs
  - DataContractSerializer
  - Event
  - eventsourcing
  - google
  - Greg Young
  - internal RPC protocols
  - JavaScript
  - json
  - Json.NET
  - Newtonsoft Json.Net
  - nservicebus
  - Protocol Buffers
  - ProtocolBufers
  - ProtocolBufers.NET
  - Serialization
  - ServiceStack Json
  - SoapFormatter
  - xml
  - XML Serializer
---
### Introduction

Serialization is not a trivial problem in any language. In .NET there are quite some choices available for serializing/deserializing objects. Each available option has it's strengths and weaknesses.

I've started a project on git hub [SerializationTests][1] where i'm trying to determine what is supported by which library and draw a few conclusions that should be helpful when designing serializable objects and in general when dealing with serialization.

I've started this project because I've been bitten too many times by issues related to serialization, either trying to send a message with an System.Uri property in NServiceBus, or trying to deserialize an object from Json which had an ID public readonly field named different from the corresponding parameter in the constructor.  In the end i'm trying to put together a few tips that i hope will be useful in the to myself and others.

### Tested Implementations

The following serialization choices have been included in the SerializationTests project:

*   [BinaryFormatter][2]
*   [DataContractSerializer][3]
*   [NetDataContractSerializer][4]
*   [Newtonsoft Json.Net][5]
*   [NServiceBus BinarySerializer (Uses BinaryFormatter)][6]
*   [NServiceBus XMLSerialize][6]
*   [ProtocolBufers.NET Serializer][7]
*   [ServiceStack JsonSerializer][8]
*   [SoapFormatter][9]
*   [XmlSerializer][10]

**Update:** Added [Raven.Json Serializer - see post][11]

### Tested Messages

When designing an object that is meant to be used as a message, event or command there are a few approaches you can take:

*   **public readonly fields** and a constructor with appropriate parameters ( my favorite )
*   **public properties with private setters** and a constructor with appropriate parameters ( if you don't like public fields )
*   **public fields**, no constructor ( only generated default )
*   **public properties**, no constructor ( only generated default )
*   **interfaces with getters** and separate internal implementation (ex for events )
*   **interfaces with getters and setters** ( to help serialization )

I say i prefer the first choice since i find it the one that expresses my intentions the best. I want immutable data transfer objects. Also i have to mention that the first time i've seen this approach was in a [video by Greg Young][12] about event sourcing and CQRS architecture. To get the idea an event it's defined like this:

{% highlight c# linenos %}
public class PersonCreated
{
    public readonly Guid AggregateId;
    public readonly string Name;
    public readonly string Street;
    public readonly string StreetNumber;

    public PersonCreated(Guid id, string name, string street, string streetNumber)
    {
        this.AggregateId = id;
        this.Name = name;
        this.Street = street;
        this.StreetNumber = streetNumber;
    }
}
{% endhighlight %}

### Serializers Conclusions

**Disclaimer**: I have not used all this serializers in real projects and i'm by no means an expert in any of them. Before adopting one of them do you research and try to see if they have any other drawbacks that might not be acceptable in your projects. If i don't mention Cons for one, it only means that i have not been interested in it so much to research it deeper.

### [BinaryFormatter][2]

**Pro:** The BinaryFormatter included in the .NET Framework passes all tests except the DataContractOnly test which is expected since it relies on the presence of the [Serializable] Attribute.

**Cons**: Very Platform dependent. Assembly version dependent. It's complicated to handle different versions of the same class. Requires the [Serializable] attribute which you might not always be able to add.

### [DataContractSerializer][3] & [NetDataContractSerializer][4]

**Pro:** Passes all the tests, XML Based, used in WCF

**Cons:** Requires attributes on the class ( [DataContract] ) and all members ([DataMember])

### [Newtonsoft Json.Net][5]

One of the most common choices when doing serialization in an AJAX Call where the result is deserialized in JavaScript.

**Pro:**JSON Based, human readable, platform independent , fast, passes almost all the tests except the ones where the message has public readonly fields that have dirrerent names from the constructor parameters.

**Cons:** Without automatic testing, naming a constructor parameter different from the field can cause hard to notice bugs where certain fields are not deserialized. It should however be possible to write a deserializer based on the existing one which throws an exception if there are doubts when deserializing.

### [NServiceBus XMLSerialize][6]

Integrated in NServiceBus. Has caused me a lot of problems when it was silently ignoring some properties.

**Pro:** XML Based, integrated with NSB, leaving aside the problems i've had with it has performed quite well in a few projects. Even if i don't personally love it, it's actively maintained  by the NSB group and can be reliably used if you know it's limitations.

**Cons:** You might end up with properties being silently not serialized/deserialized

### [ProtocolBufers.NET Serializer][7]

To quote the authors:

> Protocol Buffers are a way of encoding structured data in an efficient yet extensible format. Google uses Protocol Buffers for almost all of its internal RPC protocols and file formats.

**Pro:** Fast, platform independent, compact, throws exceptions when unable to serialize/deserialive, supports versioning

**Cons:** Needs custom attributes on the class and members that need to be serialized.

Overall a good choice when size and performance really matter.

### [ServiceStack JsonSerializer][8]

**Pro:** it's said to be the fastest json serializer.

**Cons:** Fails a lot of tests - you have to make sure you write your objects in ways supported by the serializer

### [SoapFormatter][9]

**Pro:** Xml Based, Passes all the tests except the one without the Serializable attribute

**Cons:** The outputed xml is big

### [XmlSerializer][10]

**Pro:** widely known?

**Cons:** I must be doing something wrong since it fails a lot of tests.

## Conclusion

My first conclusion is that when approaching an architecture that relies on serialization like EventSourcing and CQRS you should carefully plan the way you write and persist the events in your system.

At the moment my choice is Json.NET with a Gzip filter. The main reason is that it has a minimal impact on how i must write events and is efficient in therms of speed and size yet is still human readable.

I'm hoping after i let this sink in a little i'll come back an update the conclusions.

Also i would be glad if others share their opinions & experiences related to serialization.

 [1]: https://github.com/etishor/SerializationTests "Serialization Tests"
 [2]: http://msdn.microsoft.com/en-us/library/system.runtime.serialization.formatters.binary.binaryformatter.aspx
 [3]: http://msdn.microsoft.com/en-us/library/system.runtime.serialization.datacontractserializer.aspx
 [4]: http://msdn.microsoft.com/en-us/library/system.runtime.serialization.netdatacontractserializer.aspx
 [5]: http://json.codeplex.com/
 [6]: https://github.com/NServiceBus/NServiceBus
 [7]: http://code.google.com/p/protobuf-net/
 [8]: https://github.com/ServiceStack/ServiceStack.Text
 [9]: http://msdn.microsoft.com/en-us/library/system.runtime.serialization.formatters.soap.soapformatter.aspx
 [10]: http://msdn.microsoft.com/en-us/library/system.xml.serialization.xmlserializer.aspx
 [11]: http://www.erata.net/net/net-serialization-choices-raven-json/ "Raven Json"
 [12]: http://www.viddler.com/explore/GregYoung/videos/8/ "CQRS Class"