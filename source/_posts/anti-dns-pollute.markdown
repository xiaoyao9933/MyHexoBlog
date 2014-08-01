comments: true
date: 2013-01-22 03:08:34+00:00
layout: post
slug: anti-dns-pollute
title: 有效防止DNS污染的方法一览
wordpress_id: 272
categories:
- Web与互联网
tags:
- dns
- tcp
- 污染

---

## 前言


众所周知我们的wall对国内的骨干DNS上做了的污染，导致DNS不能正确解析某些网站的ip地址。有些说，那我们用8.8.8.8吧，其实查询国外的DNS在通过wall的时候，都会被污染。

<!-- more -->
### DNS污染实验


做个简单实验，实验里的地址去掉/就好了，我还是比较担心我的blog被关键词过滤。
``` bash
$ nslookup g/i/h/u/b.c/o/m 8.8.8.8
Server: 8.8.8.8
Address: 8.8.8.8#53
Non-authoritative answer:
Name: /g/i/t/h/u/b/./c/o//m//
Address: 59.24.3.173`
这个返回的59.24.3.173就是一个子虚乌有的ip，被污染了。这是因为UDP方式DNS，被干掉了。
```
``` bash
$ nslookup -vc g/i/t/h/u/b.c/o/m 8.8.8.8
Server: 8.8.8.8
Address: 8.8.8.8#53
Non-authoritative answer:
Name: g/i/t/h/u/b/./c/o/m/
Address: 207.97.227.239
```

但上面这个查询我们加了一个-vc参数，表示使用TCP连接方式进行查询，可见其返回了正确的结果。


###  HTTPS对wall的无视性


由于SSL加密连接的特点，传统的旁路攻击wall对其的破坏性极其微弱。由于旁路的中间人攻击方式除了骚扰之外，并不能完全阻断和某个ip之间的通信。我们假如得到了正确的ip，在https连接的情况下，是可以访问某些网站的。ps:其实wall对https的连接还是有点儿阻碍的，表现在https建立初期连接较慢。

这个简单的实验法，就是在hosts表里绑定ip。比如刚才的那个github的ip，绑定后，就可以正常上网了。


## 方法篇




### Linux下搭建基于TCP通信的本地DNS缓存服务器


首先安装pdnsd，建议去[官网](http://members.home.nl/p.a.rombouts/pdnsd/dl.html)下载最新的source或者二进制文件，ubuntu源里的那个太陈旧了。

下载后,先
``` bash
$ sudo cp /etc/pdnsd.conf.sample /etc/pdnsd.conf
```
再编辑pdnsd.conf

``` bash
global {
perm_cache=4096;
cache_dir=”/var/cache/pdnsd”;
# pid_file = /var/run/pdnsd.pid;
run_as=”nobody”;
server_ip = any;
status_ctl = on;
paranoid=on;
query_method=tcp_only; // 仅用TCP查询,这个是最重要的地方
min_ttl=1d; // 把ttl最小时间提升为一天 (1 day)，这个可以优化dns查询效率
max_ttl=1w; # One week.
timeout=10; # Global timeout option (10 seconds).
neg_domain_pol=on;
udpbufsize=1024; # Upper limit on the size of UDP messages.
}
# The following section is most appropriate if you have a fixed connection to
# the Internet and an ISP which provides good DNS servers.
server {
label= "google dns";
ip = 8.8.8.8, 8.8.4.4; # Put your ISP’s DNS-server address(es) here.
timeout=4; # Server timeout; this may be much shorter
# that the global timeout option.
uptest=ping; # Test if the network interface is active.
purge_cache=off; # Keep stale cache entries in case the ISP’s
# DNS servers go offline.
edns_query=no; # Use EDNS for outgoing queries to allow UDP messages
# larger than 512 bytes. May cause trouble with some
# legacy systems.
}
```
这里的代码引用了[这篇文章](http://kafei.in/archives-2/749.html)

保存后，用/etc/init.d/pdnsd restart 或者其他的服务控制方式进行重启。

在本机设置dns为127.0.0.1,或者在其他机子上设置你的ip号，即可完成DNS请求。


### Windows下一劳永逸的强制TCP查询法


[这个文章](http://www.bingtech.net/wordpress/2011/04/233/)提到的替换dnsapi.dll方法，十分巧妙，十分本土化。我自己还没有对其进行过反汇编，不过用了已经修改过的文件，效果很好。


### 过滤UDP污染结果的方法


[dnsproxycn](http://code.google.com/p/dnsproxycn/)项目，利用了wall的污染结果早到于正确结果的特性，过滤了污染结果。这也是一个很有意思的项目。


### 其他方法


[PWX-DNS-Proxy 可以强制使用 TCP 查询的 DNS 服务器](http://www.quakemachinex.com/blog/?p=183)， 这个方法也是类似linux下的方法，搭建了一个dns缓存。

[dnscrypt](http://www.opendns.com/technology/dnscrypt/) 这个是opendns搞的加密dns，自从墙升级后，对加密链接的封杀已经愈发凶狠，目前效果已经非常差劲，表现为经常进入unprotected状态


## 总结


如果在实验室内，供其他人方便使用的话，还是搭建一个本地dns代理服务器比较合适，既可以自由上网，又可以加快dns查询速度，一人操作，全民享福。实际操作中的效果，访问u2b,脸谱,github,google服务等具有ssl的网站都是可以的。其实u2b的高清视频打开速度比优酷快多了。我觉得本方法不叫fan墙，应该是合法穿墙，因为我们想要获得正确的DNS查询是合理的要求，https加密链接也是合理的。

其实如果推广一下，如果有人用公网ip搭建一个这样的服务器并公布出来就真是造福百姓了。如果未来TCP方法被封了怎么办？其实也不用担心，利用国外vps再搭一层dns代理即可，然后利用ssh把dns端口映射到国内的服务器或本地上建立dns缓存。
