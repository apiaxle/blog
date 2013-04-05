---
layout: post
title: Storing Time Series Statistics in Redis 
author: Stuart Quin
description: |
  The following is the tale describing the creation of a flexible
  strategy for storing time series statistical data in Redis.
---

This was developed as part of a recent rethink of how the popular API
proxy [ApiAxle](http://apiaxle.com) handles it's statistical data so
it's quite specific to the associated domain. This is certainly not
the only way to tackle this problem and may not be the best, simply
the one that worked for this developer on this project.

## Requirements

* Super fast inserts.
* Query by arbitrary time range.
* Support for both near real-time (per second) and historical data.
* Reasonable DB space usage.

## Storage

After looking at various solutions including using
[Sorted Sets](http://redis.io/topics/data-types#sorted-sets) we
decided we could get the best performance/space setup by breaking
storing each API hit in a range of hashmaps representing different
granularities of time (e.g. minutes, seconds...).  Each hashmap would
hold a suitable number of values to provide useful data at that
granularity.

    granularities =
      seconds: # kept for 1 hour
        size:   3600
        ttl:    7200
        factor: 1

      minutes: # Available for 24 hours
        size:   1440   # Minutes in 24 hours
        ttl:    172800 # Seconds in 48 hours
        factor: 60     # Number of seconds that make up this granularity

This structure is easily extensible and customisable to suit the
projects needs.

Each key is then assigned a TTL (using
[Redis EXPIREAT](http://redis.io/commands/expireat)) of twice the
duration of storage (this is doubled to accommodate rollover between
one day/hour/minute and the next.)

Each key then includes the timestamp at which the hit occurred rounded
down to the nearest whole number when divided by the granularity
required.

    # Round a timestamp (in seconds) for a give granularity
    getRoundedTimestamp: ( timestamp, granularity ) ->
      factor = granularity.size * granularity.factor
      return Math.floor( timestamp / factor ) * factor

### Example

A hit occurring at 1364833411 would create the following keys:

* seconds: `<API_ID>:stats:seconds:1364832000`
* minutes: `<API_ID>:stats:minutes:1364774400`
* hours: `<API_ID>:stats:hours:1364428800`

Each key then contains a mapping from timestamp:hits. In this case the
timestamp is rounded down divided by the amount of seconds per
granularity (60 for minutes).  The values are updated using the atomic
[HINCRBY](http://redis.io/commands/hincrby) operation.

## Retrieval

The statistics data is made available using the
[ApiAxle API](http://apiaxle.com/api.html#toc34) with the consumer
specifying the required granularity and time range as query
parameters. The from and to timestamps are rounded to the required
granularity, reusing the logic for saving and a simple `while` loop
iterates the range, incrementing by the number of seconds per unit.

### Example

    i = from
    while from <= to
      rounded_ts = getRoundedTimestamp( getTime(), granularities["seconds"] )
      redis_key  = "<API_ID>:stats:seconds:rounded_ts"
      results[i] = hget( redis_key, i )
      i += granularities["seconds"].factor

The full
[source code](https://github.com/apiaxle/apiaxle/blob/develop/base/app/model/redis/stats.coffee)
for this implementation is available in ApiAxle's github repo. This
version is specific to ApiAxle but if any one is particularly
interested [get in touch with me](mailto:stuart.quin@gmail.com), I'd
be happy to help create a more generic JS/Coffeescript library for
this.
