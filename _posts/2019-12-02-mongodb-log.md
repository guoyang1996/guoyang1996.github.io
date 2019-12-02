---
layout:  post
title:  MongoDB Ubuntu18.04版本允许远程访问
date:  2019-12-02 14:20:00
tags: [MongoDB]
typora-root-url: .
typora-copy-images-to: ..\assets\clipimg
---

官网的教程中 默认启动命令为

`mongod --dbpath /var/lib/mongo --logpath /var/log/mongodb/mongod.log --fork`

要想远程访问，在启动时需要加上ip

`mongod --dbpath /var/lib/mongo --logpath /var/log/mongodb/mongod.log --bind_ip 0.0.0.0 --fork`

同时确认iptables端口是放开的

