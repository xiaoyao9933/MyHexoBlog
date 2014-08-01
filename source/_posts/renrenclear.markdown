comments: true
date: 2013-01-11 12:48:32+00:00
layout: post
slug: renrenclear
title: 人人网清空器 1.1 发布了～
wordpress_id: 256
categories:
- Web与互联网
- JavaScript

tags:
- chrome
- 人人
- 插件
- 清空

---

人人网清空器，该插件可生成一个“清空”按钮在人人导航栏，点击后可以在激活人人清空器的主界面，目前版本可以清空“状态”和“分享”，所有被清空的数据将无法恢复，有需要的朋友们来试试吧，只要你不点最后的确定，就是安全无害的～

chrome store[地址](https://chrome.google.com/webstore/detail/%E4%BA%BA%E4%BA%BA%E6%B8%85%E7%A9%BA%E5%99%A8/kngpjlcohbhbbdkbkfjgjbamhmbemkjb) (可能会撞墙)

<!-- more -->

[![](/images/renrenclear/Screenshot-from-2013-01-11-171344.png)](/images/renrenclear/Screenshot-from-2013-01-11-171344.png)

虽然这个应用的应用场景还是很奇怪的，我们可以YY几个

比如你忽然觉得你以前说的话都好naive啊，你怕影响就职面试，HR搜你的人人信息，你怕有人拆你家水表等等。

## 吐嘈篇：

1. 原来发布chrome 开发者还要一次性5美刀。

2. 第一次写chrome插件，遇到了很严重的问题，比如在content javascript里调用网页原来的变量和函数就不可行了，我为了想调用人人的XN模块，最后还是在师兄的帮助下用DOM直接往html插了代码才最终解决问题。

## ChangeLog:

* 1.0 版本发布：实现了状态清空功能，小试牛刀～

* 1.1 版本发布：增加了清空器的主界面 ，添加分享的清空功能


