---
layout: post
title: Building a repl to design an API
author: Phil Jackson
description: |

  ApiAxle need to provide a way for the API owner to provision APIs,
  keyrings and keys. Early on I wrote some scripts which allowed users
  to create and manipulate the objects but they started to become
  overly complicated - they needed to know what types fields were,
  some fields were dependants of others and the options/arguments
  became unwieldy. On top of all this I realised I was duplicating
  work with Axle's own REST API. I decided to take a different
  approach.

---

