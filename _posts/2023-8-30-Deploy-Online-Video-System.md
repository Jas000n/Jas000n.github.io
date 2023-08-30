---
title: Deploy Online Video System
author: Jason
date: 2023-08-30
category: Jekyll
layout: post

cover: /assets/pics/Deploy_Online_Video_System/tech_stack.png
---



## 1. Download Necessary File

```shell
$ git clone https://github.com/Jas000n/online_video_sys.git
```

## 2. Setup Database

```shell
# DML & DDL all stored in ./online_video_sys/sql, execute all of them in MySQL

# In each SpringBoot service, configure the address and password for MySQL in resources/application.properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/cilicili?useUnicode=true&characterEncoding=utf-8&serverTimezone=UTC
spring.datasource.username=YOUR_USERNAME
spring.datasource.password=YOUR_PASSWORD
# Register Alibaba Cloud service, get the access_key & access_id; fill in the blank in resources/application.properties, for Services: VOD(video on demand), OSS(object storage service), SMS(short message service)

#Start Redis
$ redis-server
```

## 3. Start Nacos

```shell 
$ cd /YOUR_DIRECTORY/online_video_sys/Nacos/bin
$ sh startup.sh -m standalone
```

## 4. Start Application

Start all SpringBoot Service

Start both front end service, cili_front(for user to browse, watch and purchase) & cili_Back(for administrator to manage the website and view statistics)

It should look like this:

![demo](/assets/pics/Deploy_Online_Video_System/demo.gif)