---
layout: post
title: Beautiful APIs and dogfood
author: Phil Jackson
description: |

  The secular trend towards APIs is seemingly unbounded. Out of the
  excitement will comes established companies wanting to get their
  slice of the pie and experimenting with presenting their data though
  a programmable interface. How do you go about designing one
  considering whilst considering current architecture?

---

If you were building a large system now it might seem contrary to
build a service which relies on a data source without an abstraction
in the form of an API. How, if an application has been servicing
clients for years and a decision is made to release an API do you
design it? What do you base requirements on? Your current interface?

No. Well, probably not. One thing you can be pretty certain of is that
the way developers want to interact with your API won't be the same
way *users* interact with your current interface. In fact, why would
you want to design such restrictions into your interface? You should
be out to enable and encourage innovation with your data, you need to
keep the interface generic, free of edge-cases and free of
particularly domain specific logic. An oft-recommended technique for
designing your interface is to "dogfood" it, that is, to become
self-reliant on the same technology you present to others - any
problems your clients experience you also suffer, any optimisations
will benefit you equally. This sounds well and good until you consider
the fact that your particular use-case isn't necessarily the best use
of your data.

Take Google Maps, reasonably early on the service was reverse
engineered and people used the maps tiles for some great
mashups. Rather than lock down their system further, Google liked what
they saw, recognised the potential value, and subsequently became more
open by releasing a public interface. People were using the API in
ways Google couldn't have imagined when the original idea was
conceived, despite the service being so innovative in itself. The
strategy centred on remaining in control of the data but to see how
people could innovate beyond what Google themselves had already
achieved. It paid off too, they have a massive, profit generating,
customer base consuming and presenting the data.

Going back to the original point, building an API with an existing
application. The first thing to do is go out and talk to your existing
customers. Ask them how an API might complement your current offering
and how it might help them grow their business. Describe to them a
scenario which might 
