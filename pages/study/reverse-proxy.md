---
title: Reverse proxy
last_updated: Jun 10, 2026
keywords: study
sidebar: mydoc_sidebar
comments: true
permalink: reverse-proxy.html

---

## Reverse proxy
#### Check [this video](https://www.youtube.com/watch?v=-R5ak7-LiVY)
1. Desc: As proxy sits inbetween your client and the server and protect client's info, reverse proxy is to sit in between client and server, but protect the server's info.
2. Benefits - Reverse proxy help the load out from the server by doing some works for it:
    1. SSL: SSL is big drawback; reverse proxy can take care of that and do reverse proxy to server interaction with HTTP. (nginx)
    2. Caching: CDN
    3. Compression: smaller payload -> faster
    4. Security: as it doesn't show server's info
3. Kinds:
    1. CDN: for caching mainly
    2. **Load balancer**: with scaling out, we need a server that distribute the traffic to servers properly and maintain the crushes of server. It does health check and control the failure.
        1. main techtic: round robin, least connection, weighted, IP hashing, layer 7(sending depends on API endpoint) etc.
    3. **API Gateway**: when MSA, deciding which API goes to which server - similar to layer 7. With MSA, every services need the authentication, and all others that one server has to go through. API gateway is to manage that
        1. benefits:
            1. common logic at one place - auth, understanding the API, traffic limit, etc.
            2. API versioning
