title: Hello Hexo- 博客迁移
date: 2014-07-31 15:33:13
tags: hexo
---
虽然好久不写这个博客了，之前这个博客一直是WordPress搭建的，但随着使用时间越来越长，WP暴漏的问题也越来越多。我简单在这里列举一下：

* **过于臃肿**：wordpress的代码学习成本略高，只是一个博客，为了改动主题代码，不值得。
* **动态网站**：LAMP的环境虽不说很复杂，相应的免费主机空间倒也是有很多。只是为了兼顾速度和价钱，挑选合适的host也非常费时费力。在近一年里的时间里，使用的是冯琛师兄租用的主机，该主机性能比较低下，但价格并不便宜。同时由于该主机内存较小，MySQL和Apache中间经常有一个被挤崩溃掉，造成网站无法被访问。
* **速度较慢**: 在之前的主机上，访问网页的速度接近5秒，有一部分原因是主机位置的原因，另一部分是解析过程过于复杂。
* **MarkDown**：用插件实现后较为复杂，编辑起来实在不够Hack。

<!-- more -->
由于以上原因，我决定将博客全部静态化，以便网站可以免费的托管在[GitHub](https://github.com)，关于静态化的网站生成方案，[StaticGen](http://www.staticgen.com)列出了一些知名的开源静态化网站生成器，其中我对比考虑的几个是：

* [Jekyll](http://jekyllrb.com)： Ruby，Github原生支持，较为成熟，主题比较少，且Github渲染时间间隔过长。
* Octopress： Ruby，Jekyll的增强版，我觉得过于强大了。
* Pelican： Python，不太了解。
* [Hexo](http://hexo.io)： node.js，我很喜欢npm的包管理方式，框架非常简洁，主题风格也很多。

最终我采用了Hexo方案，主题采用[pacman](https://github.com/A-limon/pacman)（现在的主题被我个人修改过），托管方式为Github。从此每年只需要交域名费了，服务器的事情全部交给Github解决吧。

