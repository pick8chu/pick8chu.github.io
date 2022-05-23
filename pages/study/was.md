---
title: "WAS"
keywords: homepage
sidebar: mydoc_sidebar
permalink: was.html
---

## WAS

[Check here 1](https://gmlwjd9405.github.io/2018/10/27/webserver-vs-was.html)
[Check here 2](https://goldsony.tistory.com/37)

So WAS is basically the same with Server(so called), such as tomcat. 
This webpage which doesn't have any dynamic changes is considered as a WEB server.

WAS enables the WEB server to have WEB container. 
WEB container has controllers that recieve request(such as API) and response with data.

Therefore, with WAS, clients can request data and receive them. 

### Conclusion

- WAS: Regular website where client requests and receive.
- WEB: Static website, like this website. none of this is from database, it's just innated in HTML.

### UPDATE :: 21-12-08

Every request from clients has to go through WEB server, and from there, 
if the request only has static things, then return from WEB server. 
Therefore it won't go to WAS server. 

However, if the request has dynamic things, then it would reach to the WAS server
going through WEB server. 

Firewalls are often covering WEB server, which means oftentimes WEB server and WAS server doesn't have one in between.

{% include image.html file="web.png" %}
