comments: true
date: 2013-01-11 13:16:55+00:00
layout: post
slug: chrome-plugin-content-js-original-js-access
title: Chrome插件Content js对原网页空间中的变量和函数的访问
wordpress_id: 263
categories:
- Web与互联网
- JavaScript
tags:
- chrome
- js

---

## 引言


昨天在写人人的插件的时候遇到的一个问题，即利用chrome的命令行可以访问的变量（如人人的XN模块），在插件编写时却提示变量未被定义。经过查询 [How can I mimic Greasemonkey/Firefox's unsafeWindow functionality in Chrome?](http://stackoverflow.com/questions/1622145/how-can-i-mimic-greasemonkey-firefoxs-unsafewindow-functionality-in-chrome) 这个SoF的帖子，发现是两个环境分离的原因，根据帖子找到了三种解决方式。

<!-- more -->
## 方法

	
1 利用DOM将JS代码插入原网页的Head
``` javascript
setTimeout(function() {
var script = document.createElement('script');
script.type = 'text/javascript';
script.innerHTML = /*Injected JS here*/;
document.head.appendChild(script);
}, 1000);
```
2 利用location.href来插入代码
``` javascript
setTimeout(function() {
location.href = "javascript:document.body.setAttribute('data-fp', fp);";
}, 1000);
```
3 其实就是利用标准的XSS攻击的方法，利用onload什么的一些事件来执行js代码。




不管怎么说了，这些方法都是变相的把代码插入到原来的网页中。如果你的js代码较长且有空格，可以用这个网站在线压缩一下js代码 [在线JS/CSS压缩](http://tools.dedecms.com/jscsscompress.html) 。压缩后的代码可以良好的放入两个单引号的字符串里。



