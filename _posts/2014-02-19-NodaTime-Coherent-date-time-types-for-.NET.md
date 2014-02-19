---
layout : post
title : NodaTime - Coherent date time types for .NET
comments : true
category : net
tags : [noda-time,date-time,jon-skeet]
---
For the last two weeks I've been working on an internal project. Being a low risk project, I had the option of introducing new technology that was harder to introduce in other projects. I'm hoping to write a few posts about my experiences with [NancyFx][3], [EventStore][4] & [NodaTime][1], to name a few of the things I'm using. 

One library I've been looking for a chance to try is the [NodaTime][1] library, written by the famous [Jon Skeet][2].

The experience of using NodaTime in a few words: 

* extremely low learning curve
* perfect API
* everything is where you expected it to be and named as you expect it to be named 

I can't say that I've experienced major issues with the native DateTime form .NET. But I have often felt that it is not a perfect match for representing a concept or not being clear enough in a public APIs. When trying to represent a particular day, or a time in a timezone or UTC the DateTime abstraction did not capture the full intent.

So far the most useful types for me from NodaTime have been: **Instant** & **Duration**. 

**Instant** represents a specific point on a clear and unambiguous time axis. This is a common representation also found in other languages - like the time_t type in C - representing the number of ticks from the Unix epoch (January 1st 1970 00:00 UTC). 

> The "zero point" used everywhere in NodaTime is the Unix epoch: midnight at the start of January 1st 1970, UTC. (UTC itself did not exist in 1970, but that's another matter.) 

**Duration** represents an exact number of ticks on the same time axis. This is similar to the **TimeSpan** type from .net.

I have also used a **LocalDate** type to represent a birth date.

As I've said I'm using Nancy to expose an HTTP API and EventStore for persistence. For moving data across this boundaries I'm using the awesome and now almost standard [Json.NET][5] library. My first worry was that I'm going to have to convert the types from NodaTime to and from a format supported by API and events consumers. 

Almost as expected, I've found that NodaTime already provides a NuGet package for this. Flawless Json.NET serialisation is available by installing the NodaTime.Serialization.JsonNet package. 

## Conclusion

If you value the expressiveness and correctness of your code, I encourage you to give NodaTime a try. It risks becoming an addiction, but it is definitely worth it.

As a starting point continue reading the excellent [User Guide for NodaTime][6].

In the end I'd like to thank Jon Skeet and the rest of the [authors][7] that made this awesome library available for the rest of us.

[1]: http://nodatime.org
[2]: http://stackoverflow.com/users/22656/jon-skeet
[3]: http://nancyfx.org
[4]: http://geteventstore.com
[5]: http://james.newtonking.com/json
[6]: http://nodatime.org/unstable/userguide/index.html
[7]: http://noda-time.googlecode.com/hg/AUTHORS.txt