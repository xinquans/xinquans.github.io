---
title: IDEA Spring Boot 2.0.5 内置Tomcat说明
date: 2018-09-27 上午
comments: true
toc: true
categories: "经验" 
copyright: true
---
  用IDEA新建SpringBoot项目后，启动时不是内置Tomcat，而是我本地某盘下的Tomcat：
  原因是因为我配置了CATELINA_HOME。去掉环境变量配置，就可以正常使用内置Tomcat了

>不积跬步，无以至千里
