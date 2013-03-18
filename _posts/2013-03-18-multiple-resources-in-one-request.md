---
layout: post
title: Requesting multiple resources with one request
author: Phil Jackson
description: |
  It's a common problem when designing an API but there's seemingly no
  one way to return multiple resouces within a single request that
  fits in with peoples perception of an idealistic REST
  implementation. Here I discuss some options.
---

Often when writing an API someone will tell you that they need to pull
many arbitrary resources from your database. Instinctively you
probably want to tell them to simply request the objects
individually. That way keeps things simple in terms of caching and
server, and client logic. But, this is the real world and their
request might be perfectly legitimate. Some things to consider:

### Relationship of the data

When evaluating the request the first thing to consider is what is the
pattern of IDs? Are they *actually* arbitrary or are they exposing a
concept you could represent yourself with an extra resource? Someone
comes to you wanting a list of particular car models, for example,
"Fiesta, Sierra, Escort" they want a set of cars built by a particular
manufacturer. It might be time to make a `/vehicles/manufacturer/ford`
resource.

### Scalability

How quickly does the client need to resolve the individual resources?
How fast is your API? It's often helpful, caching-wise if you can
return a list of URIs and have the client resolve each
individually. The other end can take advantage of
[keep-alive](http://en.wikipedia.org/wiki/HTTP_persistent_connection)
to keep the http overhead to a minimum and you don't have to
potentially bust your HTTP cache by serving up lots of representations
of overlapping data.

### URL/URI structure

I bet this is the bit you were searching for when you came here, in
actual fact it's the might be the easiest bit to answer. A URI is
simply a unique identifier - there's not really a URI which is anymore
RESTful than another, though, of course some URIs will make more sense
than others. You have plenty of options for representing lists on a
url. I've seen any of the below suggested:

    HTTP://example.com/v1/vehicles/manufacturer/ford,lamborghini

    HTTP://example.com/v1/vehicles/manufacturer?id=ford&id=lamborghini
    HTTP://example.com/v1/vehicles/manufacturer?id=ford,lamborghini

Section 3.4 of [RFC 2396](http://www.ietf.org/rfc/rfc2396.txt) said
that:

    The query component is a string of information to be interpreted
    by the resource.

later, in [RFC 3986](http://www.ietf.org/rfc/rfc3986.txt) that became:

    The query component contains non-hierarchical data that, along
    with data in the path component (Section 3.3), serves to identify
    a resource within the scope of the URI's scheme and naming
    authority (if any).
    
So, any of the examples are fine, at least according to the latest
RFC. However, my *opinion* on the matter is that if you intend to
return a `200` and an empty list when there are no matching
manufacturers in your list then the query parameter method feels more
correct. If you intend to return a `404` and perhaps an error message
then the first feels more correct. Again, only an opinion.
