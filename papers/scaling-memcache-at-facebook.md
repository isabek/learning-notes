# [Scaling Memcache at Facebook](https://research.facebook.com/file/839620310074473/scaling-memcache-at-facebook.pdf)


- [Introduction](#introduction)
- [Overview](#overview)
- [In a Cluster: Latency and Load](#in-a-cluster-latency-and-load)
    - [Reducing Latency](#reducing-latency)
- [In a Region: Replication](#in-a-region-replication)
- [Across Regions: Consistency](#across-regions-consistency)
- [Single Server Improvements](#single-server-improvements)
- [Memcache Workload](#memcache-workload)
- [Conclusion](#conclusion)

## Introduction

This paper is mainly focused on `memcached` which is an open-source implementation of an in-memory hash table. It
provides low latency to a shared storage pool at a low cost. These characteristics of `memcached` enable Meta (formerly
Facebook) to build data-intensive applications. It is difficult to imagine that a feature that requires hundreds of DB
queries per page request goes on production without tuning it with the help of the cache mechanism. Meta's web pages
fetch dozens of key-value pairs from `memcached`.

## Overview

Meta's users mostly read the content instead of writing. This behavior of users load DB, so caching comes here to help
to avoid loads to the DB and other sources of the content. `Memcached` provides a simple set of commands: `set`, `get`,
and `delete` and an open-source version provides a single machine in-memory hash table. The aim of this paper is to show
how Meta took this simple solution and transformed it into the distributed key-value store which is able to handle
billions of requests per second.

> Meta uses `memcached` to refer to the source code or a running binary and `memcache` to describe the distributes system.

### Query cache

Meta relies on `memcache` to ease the read loads on its databases. It uses `demand-filled look-aside` caching strategy.

When as web-server needs data, it first tries to fetch it from `memcache` by providing a string key. If `memcache`
doesn't contain value for the requested key a web-server fetches data from DB and caches it to the `memcache`. For write
requests, a web server sends a query to the DB and invalidates the cache.

### Generic cache

Engineers use `memcache` to store pre-calculated results from sophisticated machine-learning algorithms which can then
be used by a variety of other applications.

## In a Cluster: Latency and Load

When you have thousands of servers in a cluster you need to spend your effort on the following problems: reducing the
latency of fetching cached data from `memcache` or reducing the load imposed due to cache miss.

### Reducing Latency

The latency of `memcache`'s response is a critical factor in the response time of a user's request. Sometimes loading
one of the Meta's popular pages results in an average of 521 distinct items fetched from `memcache`.

Meta maintains hundreds of `memcached` servers in a cluster in order to reduce load on databases and other services.
Items are distributed across the `memcached` servers through consistent hashing. Thus web servers have to routinely
communicate with many `memcached` servers to fulfill a user request. As a result, all web servers communicate with
every `memcached` server is called an all-to-all connection. Aforementioned connection pattern can lead to the following
problems: `TCP incast congestion` and allow a single server to become a bottleneck for many web servers. 

## In a Region: Replication

## Across Regions: Consistency

## Single Server Improvements

## Memcache Workload

## Conclusion