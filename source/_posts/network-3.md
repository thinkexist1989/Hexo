---
title: 计算机网络初探（三）：路由里的小搬运工—NAT
date: 2018-9-7 13:05:00
categories:
 - 理论学习
tags: 
 - 计算机网络
 - 网络地址转换
 - NAT
mathjax: false
---

现在由于我们每个人的手机、电脑、平板电脑等设备都有接入互联网的要求，可是我们家里通常只有一路网线入户，为了解决这个问题我们家里现在都会购买一台无线路由器，将这根入户网线连接到路由器的WAN口之后，便可以通过路由器上的LAN口或者WIFI接入来上网了。这时候我们查看一下我们设备的IP地址，通常为`192.168.1.110`这种形式，可是我们登陆[ip138.com](http://ip138.com)这种IP查询网站时，显示的却是类似`96.46.185.33`这样的IP，为什么本机显示的IP地址和IP查询网站上显示的IP不一样呢？到底是哪里出现了问题？其实这两个IP显示的都没错，这是因为我们路由器有一个小搬运工—NAT的存在，可是NAT的功能是什么？存在的意义是什么？这就要先从我们的IP地址说起。

## 公网IP与私有IP

之前的博客里提过，IP地址是一串32比特的字符串，每8比特用一个`.`隔开，可表示为xx.xx.xx.xx，IP地址又有公网IP和私有IP之分，负责分配IP地址的机构预留了三块专有地址，作为私有IP地址供内部组网使用：

- A类：10.0.0.0～10.255.255.255/8
- B类：172.16.0.0～172.31.255.255/12
- C类：192.168.0.0～192.168.255.255/16

IP地址是互联网设备的身份证，也就是每个接入互联网的设备都必须具备唯一的IP地址，这个唯一的IP地址就是公网IP。具备公网IP地址的设备就像是有了具体的门牌号，我们可以直接利用公网IP来和这个设备进行通信。可是IP地址是珍贵的资源，全世界只有20亿左右的IP地址资源，若以现在的互联网发展速度，是无法为每一台设备都分配一个公网IP地址的。但是由于互联网通信协议的要求，每个接入互联网的设备必须具备唯一的公网IP，因此为了解决这个冲突，NAT就应运而生了。

## 什么是NAT

NAT(Network Address Translation，网络地址转换) ，用于将一整个内部网络通过一个公网IP连接进互联网。我们家里的路由器都具备NAT的功能，也就是将我们分配给我们的设备私有IP转换为公网IP接入互联网，从而实现只具备私有IP的设备也可以接入互联网的功能。

## NAT工作过程

NAT的工作开始要从用户连接上路由器之后开始说起。当用户A利用手机WIFI连接上路由器后，通过路由器内置的DHCP服务器，会自动为用户分配以下信息：

- IP地址：192.168.1.100
- 子网掩码：255.255.255.0
- 缺省网关：192.168.1.1
- DNS服务器：192.168.1.1

用户A的手机在局域网下的IP(192.168.1.100)是唯一的，但这个IP是私有IP地址，无法直接连接互联网，可是现在用户A在手机上打开了浏览器，想要访问公网IP为2.2.2.2的服务器上的网页，于是乎一个隐藏在路由器内部的小搬运工—NAT即将登场了。

### 1 客户端的IP报文发送给路由器

用户A的手机浏览器会填写一个包含以下信息的报文发送给路由器：

- 目的地：2.2.2.2
- 目的端口：80
- 发送方地址：192.168.1.100
- 发送方端口：1234

### 2 小搬运工NAT出场

路由器收到了从192.168.1.100这个IP发送过来的报文，可是它不能直接将这个报文发送到互联网中，因为192.168.1.100这个IP是一个私有IP，若原封不动地发送上去的话，是无法收到2.2.2.2返回信息的。路由器的WAN是连接着互联网的，具有公有IP 1.1.1.1。于是路由器就请来了小搬运工NAT，将这个报文做了一些修改：

- 目的地：2.2.2.2
- 目的端口：80
- 发送方地址：1.1.1.1
- 发送方端口：6789

我们发现，NAT将报文中的发送方地址和端口修改成了路由器的公网IP 1.1.1.1，并且将端口号换成了6789。于是这个报文的发送方就具备了公网IP，于是便可以顺利的接收从2.2.2.2返回的消息了。

但这还没有结束，如果NAT只是简单的修改了发送方的地址和端口之后便撒手不管了，那么就算2.2.2.2返回了消息，可也只是路由器接收到了消息，路由器下面可能连接着数十个私有IP的设备，那这个消息到底应该转发给谁呢？

### 3 NAT会作记录

小搬运工NAT有一个好习惯，就是每次修改了发送方地址和端口之后，都会用小本把这个改动记录下来，这就是地址转换表，每次路由器呼叫NAT来进行一次地址转换，NAT都会细心的把这个改动记录下来，这个记录大致是如下形式：

|    公网IP |    私有IP  | 路由器端口号 |主机端口号|存活时间（秒）|
| :---: | :---: |   :---:   |  :---: | :---: |
|  2.2.2.2  |  192.168.1.100   | 6789 | 1234 | 300 |
|  3.3.3.3  |   192.168.1.106  | 12859| 5896 | 300 |

### 4 NAT将服务器报文转发给私有IP下的设备

正是因为有了地址转换表的存在，当服务器2.2.2.2将用户A的手机想要得到的报文返回时，NAT会参照地址转换表中的内容，将相应的报文转发给局域网下具有私有IP的手机，手机就可以和互联网通信啦！

## 写在后面

NAT的一系列动作，都是悄无声息的在路由器中完成的，甚至让很多人忘记了它的存在。这就是为什么我们在本地电脑上查看IP是私有IP，而在查询IP的网站[ip138.com](http://ip138.com)下查询到的却是公网IP了，全部都是NAT这个小搬运工在后面捣的鬼！可是我们要感谢NAT，要是没有它，我们的电脑可能就要被困在路由器的小黑屋中，无法和互联网中的世界联系啦！
