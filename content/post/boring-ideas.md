---
title: "Boring ideas in Computer Science: Caching"
date: 2020-04-26T16:16:40-07:00
draft: true
---

_"There are only two hard things in Computer Science: cache invalidation and naming things"_
_- Phil Karlton_

In Computer Science, caching is a well known idea. Almost all modern systems, use one form of caching or the other. It is usually by using some form of cache that computer systems manage to speed up computation. As such, a wide range of caches are deployed in various parts of the computer system, including caches in the microprocesor itself. In this article, though, I will focus on caching in web applications.

Web applications are composed of complex works of software. At a very high level, a web application would consist of of some form of server side persistance along with a server side web programs handling requests from the client. Depending on how the application is designed, such server side web program could in turn be calling other web applications behind the scences, or running asynchrnous operations. Programmers often use caches in any parts < //TODO rename > of this system. While caching makes things faster by saving computation, invalid caches result in hard to reproduce bugs.

//check bookmarks for examples
// Add some code examples of cache demonstrating what happens if you cache hapazardly
// Introduce TTL
// introduce write through/write behind/read through etc
// tip: make it visible ie observable system
//
// conculsion: dont' re-invent the wheel. 
//follow the strcuture has here https://queue.acm.org/detail.cfm?id=2088916
References
1. TODO: Microprocessor caching
2. Web applications/microservices/SoA and etc
