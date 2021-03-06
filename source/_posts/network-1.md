---
title: 计算机网络初探（一）：IP地址与域名解析
date: 2018-9-2 21:49:00
categories:
 - 理论学习
tags: 
 - 计算机网络
 - IP地址
 - 域名解析
 - dns
mathjax: false
---

作为一个工科男，一直对互联网这个每天离不开的庞大计算机网络充满了好奇。但互联网的知识如此繁杂，想了解但却又无从下手。虽说之前也配置过家里的路由器，利用过shadowsocks进行科学上网，但是却使用只能照着教程一步一步来，对互联网还是一知半解。正好遇上了同样喜欢研究的[丁博士](http://blog.chauncey.ml/wordpress)，趁着和他请教搭建VPS服务器的同时，研究了一下计算机网络的知识，对计算机网络的搭建有了初步的了解。

# IP地址

当今的互联网是基于TCP/IP的思路设计的，由一些小的子网通过路由器连接起来形成一个巨大的网络。在计算机网络中，所有的设备都会被分配到一个IP地址，这个地址就像是家庭住址，互联网之间的信息传递就像是快递员送信，快递员如果不想把信送错地方，那么就需要每一个设备都有一个唯一的门牌号，例如“XX区XX街XX号”。实际的IP地址是一串32比特的数，每8比特（1字节）一组，分为四组，所以我们看到的IP地址就是这样的：192.168.0.1。

# 子网掩码

TCP/IP是将多台计算机组成一个子网，然后再利用路由器将这些子网连接起来，IP地址是由网段号+主机号构成的，但是单看IP地址是无法确定哪些计算机在一个子网下的，于是乎一个叫子网掩码的东西就呼之欲出了。子网掩码（mask）是一串与IP地址相同长度的32比特数字，其左边一半都是1，右边一半都是0。子网掩码为1的部分表示网段号（子网下的计算机IP地址相同的部分），子网掩码为0的部分表示主机号（子网下不同的计算机IP地址不同的部分）。其表示方法有两种，例如，网段号24位，主机号8位的子网下的计算机A：192.168.0.1，可以表示为192.168.0.1/255.255.255.0，或者192.168.0.1/24。这就表示在192.168.0.1这个IP地址中192.168.0代表网段号，而.1代表主机号，于是IP为192.168.0.X（X表示1～254中的任意值）均处在同一个子网下。

值得一提的是，主机号部分比特全为0代表整个子网而不是某一台设备，例如192.168.0.0/24代表整个子网；而主机号部分比特全为1代表向子网上所有设备发送包，即广播（Broadcast，这很有用，比如ARP查询MAC地址时就需要利用广播）。

# 域名解析

正常来讲，我们想访问网络中的任意一台计算机，只需要输入它的IP就能和它通信了。比如我们想访问[百度](baidu.com)，其实可以直接在浏览器下输入其服务器的IP[123.125.115.110](123.125.115.110)即可，可是几乎没人这样做，大家都是直接输入域名[baidu.com](baidu.com)来访问的，这就像我们知道每个人的名字，但是却不会记住每个人的身份证一样，与其记住一串毫无规律的数字，不如去记住一个直观的名字来的容易。但是计算机之前是靠IP地址来通信的，当我们输入[baidu.com](baidu.com)这个域名的时候，DNS服务器（Domain Name System，域名服务系统）就登场了，浏览器会去最近的DNS服务器查询[baidu.com]这个域名对应的IP地址，然后利用查询到的IP地址来登陆百度的网站，这个过程就叫域名解析。

DNS服务器同样是有IP地址的，有时候我们在Windows下配置IPv4协议时，输入首选和备用DNS服务器的地址，为的就是能够把域名正确解析到对应的IP地址。