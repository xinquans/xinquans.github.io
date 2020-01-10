---
title: Myeclipse修改设置Default VM Arguments
date: 2018-10-11 下午
comments: true
toc: true
categories: "经验" 
copyright: true
---
#### 第一步：
打开Myeclipse，选择Windows->Preferences
#### 第二步：
选择Java->Installed JRES，选择相应JDK，点击Edit
#### 第三步：
Default VM Arguments输入：
>-Xms512m -Xmx1024m -XX:PermSize=512m -XX:MaxPermSize=1024m

点击finish。



>不积跬步，无以至千里