---
title: redis moniting
date: 2018-04-13 10:53:58
tags:
---

[Understanding the Top 5 Redis Performance Metrics](https://www.datadoghq.com/pdf/Understanding-the-Top-5-Redis-Performance-Metrics.pdf)

1. memory Used: *used_memory*
* moniting available memory: 1 - *used_memory_human/used_memory* x 100%
* moniting physical memory: *used_memory/total available memory* x100%
    If used memory more than total available memory may cause memory swap.
    Memory swap to disk may cause up to 5 orders of magnitude slower.
* using periodiacal snapshot: retain enough memory to prevent swaping happens
    * try 32-bin Redis
    * use hashes when possible
    * expiration key should expire
    * *config set maxmemory <value>* in **redis-cli**
    * set *maxmemory-policy* as *volatile-ttl* or *allkeys-lru* in redis.conf for when maxmemory limit is reached, *noneviction*
2. number of commands processed: *total_commands_processed*
    setup a script that preiodically logs the *total_commands_processed* metric and measure latency.use this log of historical command volume to determine if your total number of commands processed increased or decreased when you observed slower response times.
    * use multi-argument later commands are waiting in	queue for the	large volume of	earlier	commands to	complete
    * pipeline commands: save network latency cost.(networklatency is significantly larger than your instance's)
    * avoid slow commands for large sets: time-complexity related command
3. Latency
    Latency measures: **./redis-cli --latency -h 'host' -p 'port'**
    * Tracking Redis performance: 1Gb/s network with latency greater than 200μs likely point to a problem.
    * 