---
title: '.NET Serialization Choices - Raven.Json'
layout: post
comments: true
category: NET
---
This post is an addition to [.NET Serialization Choices][1] post.

I've added to the [SerializationTests ][2] Project the new serialization implementation in Raven.Json.

The Raven.Json Serializer is an extension to Newtonsoft Json.NET serializer and as expected the results are similar. Also the Pros & Cons are similar:

### [Raven.Json][3]

Optimized DOM for Newtonsoft Json.NET

**Pro:** JSON Based, human readable, platform independent , fast, passes almost all the tests except the ones where the message has public readonly fields that have dirrerent names from the constructor parameters.

**Cons:** Without automatic testing, naming a constructor parameter different from the field can cause hard to notice bugs where certain fields are not deserialized. It should however be possible to write a deserializer based on the existing one which throws an exception if there are doubts when deserializing.

 [1]: http://www.erata.net/net/net-serialization-choices/ ".NET Serialization Choices"
 [2]: https://github.com/etishor/SerializationTests "Serialization Tests"
 [3]: https://github.com/ravendb/Raven.Json "Raven.Json"