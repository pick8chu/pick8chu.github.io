---
title: "spring"
keywords: homepage
sidebar: mydoc_sidebar
permalink: spring.html
---

# Spring

## dispatcher sevlet
- a service that will distribute the request into their controller.
- ex) when "/abc/cde" request came, it will pass the request to the service/controller that has the same name.

## DQM / DAO
- DQM doesn't have beans => low reusability, and high cost
- DAO has beans with its respective sql IDs with execute plans => high reusability, low cost

## Facade design pattern
- ~Service => ~ServiceImpl
