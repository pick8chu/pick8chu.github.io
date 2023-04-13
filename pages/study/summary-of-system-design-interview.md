---
title: Summary of System Design interview
last_updated: Apr 13, 2023
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: summary-of-system-design-interview.html
---

## #. intro

This is a summary for the book, `System Design Interview`. <br/>
I am only writing this to not forget and read it whenever I need to remind myself. <br/>

## 1. SCALE FROM ZERO TO MILLIONS OF USERS

### What databases to use
- structured Data: RDBMS(SQL) (join is easier/cheaper)
- Non-structured Data: None-RDBMS(noSQL)
	- low latency - faster to retreive
	- easy to scale out (add servers) - however RDBMS could also use sharding
	
- Sharding: separating DB into several servers
	- Celebrity problem: one of servers could hit too often
	- Join and de-normalization: when it's too separated, join cost could be big.


### load balancing
- Pro:
	1. Can handle big amount of traffic
	2. better security - only load balancer can reach the server
	
### Database replication
- Master - Slaves : write to master and read from one of slaves. Master's data will be copied to slaves.
	- Master offline -> one of slaves became the master
- Consistence hashing for reading slaves?
- Write would have big traffic ...

### Cache
- things to think about:
	1. consistency
	2. Expiration policy
	3. Eviction policy : what to delete when it's full
	4. Single point of failure : when cache server is down, all the traffic would go to the server -> maybe recommended to use `rate limiter`
	
### CDN ★
- Geographical server. Content delivery network. Storing static datas(Images, videos, CSS, JS files ..)
- It will reduce the latency by big time locally.

### Stateless vs Stateful
- Stateless(JWT, session) all the way : 
	1. scalability
	2. robustness(HA)

- For stateless: Message queue can be used for the communication.

## 2. BACK-OF-THE-ENVELOPE ESTIMATION

### things to think about first
- read count per sec : QPS(query per sec)
- peak QPS : 2 * QPS
- write count per sec
- data type/size per request : storage requirement
- daily active user?
- cache? 
- number of servers????

## 3. Interview process

1. Understand requirement and establish design scope limit
	- What is the most important function of the system?
	- QPS, writes per sec, which storage, storage size, cache, server side render/client side render

2. Propose high level design and be pursuasive

3. Design and deep dive

4. wrap up

### things to remember
1. Always ask for clarification
2. Possibly suggest multiple approaches
3. Mind the time, so don't go too deep unless it's neccesary

## 4. Rate Limiter (CODE: 429)
Rate Limiter is to limit the request per IP (somesort). 
- prevent D-DoS (distributed denial of service)

### Where?
- can be located in-between any layers: but generally client <-> API server

### How? (algorithm)
1. ~~Token bucket~~
2. ~~Leaking bucket~~
3. Fixed window counter: certain coounters per fixed timeline
4. Sliding window log: FIFO - sliding time frame with log data
5. ⭐Sliding window counter
- use headers: X-Ratelimit-Remaining/Limit/Retry-After

### Problems in a distributed system
- Race condition: write-read consistency :: lock
	- Lua script/sorted set data
- synchronization issue in different servers: 
	- using centralized data stores such as Redis

### Performance Optimization
- multi-data center setup is crucial
- eventual consistency model: consistency guranteed after *N* time

{% include image.html file="rate-limiter.png" %}
- if the data is stored in cache, it won't go through rate limiter.


## 5. CONSISTENT HASHING
Important concept for traffic distribution, especially when scale out. Could be applied to `Load balancer`, also `sharded DB`
- hashvalue? [0-9a-zA-Z] : 62 possible chracter per block.

### Hash ring:
- with more virtual servers/nodes, distribution is almost even.
- When 1 server down, only *k/n* hash keyes need to be re-allocated. (k = # of hash key, n = # of server/node)

