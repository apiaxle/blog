---
layout: post
title: Signs you're veering from good API design
author: Phil Jackson
description: |

  Every developer sets out to make their REST API perfect when they
  start writing it, but, at least from this author's experience, it
  often veers as requirements pour in. The following is a small
  checklist of things that might indicate you're veering too far.

---

## Everything is a GET

This is a common mistake made with web apps. People have a tendency to
route everything, including non-safe operations, through a GET. Other
than things like logging and writing to a cache, GET, HEAD, OPTIONS
and TRACE shouldn't have any side effects on the data being requested,
if they do then there's probably another verb that describes the
operation more closely.

## All of your HTTP response codes are 200s

This won't be an issue for long - developers will let you know very
quickly if your errors are coming back with a 200 OK. When developing
a client the first thing this author does after receiving a response
is check the HTTP code, not search the body for an indication of a
problem. There's a wealth of HTTP codes which can account for pretty
much any issue you're trying to notify the user of.

On the topic of errors...

## Use of arbitrary error numbers

Using numbers to represent errors is a terrible idea (a carryover from
the C/PHP `errno` days?). Given that there is a rich platform for
returning data, publishers might as well take the extra step towards
helping out the client. This is the sort of thing I'm talking about:

    "error": {
      "code": 2301,
      "message": "That object cant be linked."
    }

The problems here are:

* Maintaining the codes from the API publisher's end will be a pain.
* This will mean developers will have to represent each of the codes
  with an enum-a-like or have their code littered with awful stuff
  like `if error.code is 2301 then ...`. As time passes the meaning of
  2301 will be lost.
* Message can't be relied upon as presumably when someone notices
  "cant" and wants to change it to "can't" there will be clients that
  break.

A better approach might be this:

    {
      meta: {
        version: 1,
        status_code: 403
      },
      results: {
        error: {
          type: "KeyError",
          time: "Wed, 01 Jul 2012 18:12:17 GMT",
          message: "'xxx' is not a valid key for 'acmeapi'"
        }
    }

Type can be relied upon as the canonical name for the error and the
message is a good human readable description. For good measure the
HTTP status is in the `meta` section of the response to enrich
debugging. Time is helpful for debugging.

[This is a very interesting idea](https://tools.ietf.org/html/draft-nottingham-http-problem-03)
for representing errors with resources.

## No MIME types

Don't ignore MIME types, even if you're only responding with one
representation of the data, you should return the appropriate
`content-type` header. Doing so means HTTP clients can act
appropriately towards the payload they're receiving, for example,
browsers often default to assuming the data is a `text/html`
representation. The popular
[request](https://github.com/mikeal/request) library will parse JSON
if it gets it, meaning if you don't send `content-type` two otherwise
identical responses will have clients treating them completely
differently.
