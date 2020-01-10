---
title: 动态加载JS和CSS文件
date: 2018-09-29 下午 09-30 上午更新
comments: true
toc: true
categories: "经验" 
copyright: true
---

#### 动态加载JS和CSS，可以带时间戳，js可以回调

```javascript
/**
    * 动态加载JS
    * @param {string} url 脚本地址
    * @param {boolean} stamp 是否包含时间戳
    * @param {function} callback  回调函数
    */
function importJS(url, stamp, callback) {
    var body = document.getElementsByTagName('body')[0];
    var script = document.createElement('script');
    script.type = 'text/javascript';
    if (stamp) {
        script.src = url + '?v=' + Date.now();
    } else {
        script.src = url;
    }
    if (typeof (callback) == 'function') {
        script.onload = script.onreadystatechange = function () {
            if (!this.readyState || this.readyState === "loaded" || this.readyState === "complete") {
                callback();
                script.onload = script.onreadystatechange = null;
            }
        };
    }
    body.appendChild(script);
}

/**
     * 动态加载CSS
     * @param {string} url 样式地址
     * @param {boolean} stamp 是否包含时间戳
     */
function importCSS(url, stamp) {
    var head = document.getElementsByTagName('head')[0];
    var link = document.createElement('link');
    link.type = 'text/css';
    link.rel = 'stylesheet';
    if (stamp) {
        link.href = url + '?v=' + Date.now();
    } else {
        link.href = url;
    }
    head.appendChild(link);
}
```
#### 使用方法：
```javascript
</body>
<script type="text/javascript" src="/Mobile/resources/js/common.js"></script>
<script>
    importCSS('/Mobile/resources/css/mui.min.css', false);
    importCSS('/Mobile/resources/css/common.css', false);
    importJS('/Mobile/resources/js/mui.min.js', false);
    //some functions
</script>
</html>
```
>不积跬步，无以至千里