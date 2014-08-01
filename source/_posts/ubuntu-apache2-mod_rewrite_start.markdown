comments: true
date: 2012-08-02 14:06:55+00:00
layout: post
slug: ubuntu-apache2-mod_rewrite_start
title: Ubuntu下Apache2的mod_rewrite模块的开启
wordpress_id: 56
categories:
- Web与互联网
tags:
- apache2
- mod_rewrite
- ubuntu

---

今天在Ubuntu主机上想搭建Yourls的短地址服务，前面都很顺利，最后却遇到缩短URL不能解析的问题，想来想去就是mod_rewrite模块没开启的原因，解决方法虽然不难，但是[目前网络上给出的方法](http://www.php100.com/html/webkaifa/apache/2010/0228/4006.html)不知道是太旧了，还只是Ubuntu的发行版下结构变化，ubuntu下的httpd.conf早就是空的了。Apache2的官网文档里估计也写了模块如何开启，我在这里写出来方便大家以后遇到此问题时可以迅速解决吧，**可能仅适用于Ubuntu**。

<!-- more -->
## mod_rewrite模块简介


按照[维基百科](http://en.wikipedia.org/wiki/Rewrite_engine)的说法，mod_rewrite是[Apache2](http://httpd.apache.org/docs/current/mod/mod_rewrite.html)基于正则表达式将静态地址转化动态地址的模块。是短地址缩短服务里最重要的技术，如可将chao.lu/1映射为chao.lu/query.php?p=1。它的实现本身就是正则表达式的对准和地址解析的映射。


## Ubuntu下解决方法





	
  1.
  ``` bash 
  $ sudo vim /etc/apache2/sites-available/default
  ```
  将其中<Directory "/var/www/">下的一项改为
  > AllowOverride All
 

	
  2. 
  ``` bash
  $ sudo ln -s /etc/apache2/mods-available/rewrite.load /etc/apache2/mods-enabled/rewrite.load
  ```

	
  3. 这就可以了。


实际上WordPress和Yourls不是完全不能在一个目录下，适当的改写正则表达式还是可以兼容的，不过要动动脑筋。
