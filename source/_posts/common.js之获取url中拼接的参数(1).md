---
title: common.js之获取url中拼接的参数（1）
date: 2018-09-26 下午
comments: true
toc: true
categories: "javascript" 
tags: "前端,js,经验"
copyright: true
---
>公共方法，直接上代码：
```javascript
/*
* e.g.
* var id = GetUrlParam('id')
*/
function getUrlParam(name) {
    var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)");
    var r = window.location.search.substr(1).match(reg);
    if (r != null) return unescape(r[2]);
    return null;
}
```
>不积跬步，无以至千里
