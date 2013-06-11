---
layout: post
title: Protect your data - keep it in your network
author: Phil Jackson
description: |

  With the recent uproar regarding the NSA monitoring various forms of
  our traffic, ApiAxle might be a large step in securing your data and
  user information.
---

<img src="/img/att.jpg"
     title="You're being watched"
     alt="Logo parody"
     style="float:right;" />
The recently exposed behaviour of the US government hardly needs to be
re-hashed here; at one point every single link on the front page of
Hacker News was pointing to content on the matter. It's a *massive*
scandal and will have repercussions on how we do business over the
internet from now on. There have been reports of people and companies
moving their data from US soil, developers have released a plethora of
tools to encrypt your data - from p2p traffic to your Gtalk
correspondence to your browser bookmarks. Encouragingly, this has been
taken very seriously indeed.

If you maintain an API which exposes sensitive data - including your
customer's names/email addresses and API keys - you should consider
where exactly that data is travelling to and how much control you have
over it. Do you actually control the user data? If you're using a
cloud-based API management solution, then probably not. You can't
possibly know whether or not an organisation/government is accessing
that information. Bringing your API into your network will help you
control that - it will be your lawyers who deal with data requests,
it'll be your sysops who keep an eye on the network and everything
accessing it.

At no point does ApiAxle call home. Not even the enterprise dashboard
offering that we have planned will be cloud-based. All of your data
remains just that, yours. Installing ApiAxle is very easy to do, you
can be up and running in 20 minutes for which you'll get the things
you would expect from an API proxy like rate limiting, authentication,
key and keyring management. Our API will give you advanced statistics
that let you see what's happening on your API in real-time or over the
span of a couple of years.

So have a peek at our
[product overview](http://apiaxle.com/products.html), install and
enjoy knowing your API's data is only going to people to who you
explicitly allow access.
