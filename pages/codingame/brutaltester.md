---
title: brutaltester
last_updated: May 17, 2021
keywords: algorithm
sidebar: mydoc_sidebar
comments: true
permalink: brutaltester.html
summary: brutaltester is a program that will allow you to run codes in your local pc using a refree. 

---

## [brutaltester](https://github.com/dreignier/cg-brutaltester)

> All rights reserved Codingame.com 

### Introduction

So this post is for me to read when I get to use this program again when I need to. It took me more than a day to set this up with many trouble shootings, and  I'm not sure if I would completely be able to use it even now.

'brutaltester' is basically a program that lets you learn your code on your local pc, but you have to have a refree saying who's doing better. 

you can download the program on their [github](https://github.com/dreignier/cg-brutaltester), link is [https://github.com/dreignier/cg-brutaltester](https://github.com/dreignier/cg-brutaltester).



### Instruction

>1. Install Java 1.8 (JDK)
>
>2. Install Maven.
>
>3. Run in command line `/mvn package` inside root directory of this repo.
>
>4. ./target/cg-brutaltester-0.0.1-SNAPSHOT.jar — is compiled brutaltester! You can rename it to cg-brutaltester.jar to make command line above work.
>
>5. ```cmd
>   java -jar cg-brutaltester.jar -r "java -jar cg-referee-ghost-in-the-cell.jar" -p1 "./myCode.exe" -p2 "php myCode.php" -t 2 -n 100 -l "./logs/" -s
>   ```
>
>by codingame.com
 


### Problems that I had

1. `/mvn package` wouldn't work. When you install maven, make sure is running with jdk not jre. If so, you can fix it by changing the environment variables. 
2. the jar files are created when you do `/mvn package`.
3. `java -jar cg-brutaltester.jar -r "java -jar cg-referee-ghost-in-the-cell.jar" -p1 "./myCode.exe" -p2 "php myCode.php" -t 2 -n 100 -l "./logs/" -s`  won't work. Basically what in "" would be able to executed by itself on terminal(cmd) too. For example, `php myCode.php` won't work if your haven't downloaded a complier for php. Let's say you have js file and you want to work execute it, you would do `node myCode.js`. Put this in "".
