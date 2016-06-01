---
layout: post
title:  "PHP基础复习-VirtualHost"
date:   2016-05-11 09:54:00 +0800
categories: swumao update PHP基础 VirtualHost vhost
---
> 这个系列的文章将会复习总结PHP的一些基础知识，包括了前端的简要介绍，数据库，php语法，框架等。

## 简介

我们平时在开发的过程中，如果是在本地调试，每新增一个项目便要在documentRoot目录下新建一个目录以区分。而在访问的项目的时候又不得不使用类似于下面的url来访问：

http://localhost/myproject/index.php

当我们项目很多时，这种方式不仅显得url很长，而且不便于记忆，甚至有的还使得代码中的部分变量处理起来比较麻烦。为了解决这个问题，我们在这里介绍virtualHost。

我们先来展望一下理想的访问方式：

- url不能太长
- url要方便记忆，最好可以随心所欲
- 项目文件夹的位置最好也可以任我所为。

假设博客项目，最好可以这般访问：

http://blog.mine/index.php

## 原理

通过观察上面的url可以知道，其实我们只是需要使用我们自定义的域名代替默认的localhost，并且可以隐藏项目文件夹。

为了解释我们自己定义的域名能够生效，有必要先介绍一下DNS的一些内容。

我们知道，每一个域名，其实都对应一个IP地址。域名的出现更多地是为了方便人们的记忆，提供可读性。而计算机本身在网络中的标示依然是IP地址，也就是说，计算机只认识IP地址，并不认识域名。那么久需要DNS服务来帮助我们查到哪个域名对应哪个IP地址。

DNS是一个分布式的系统，这个知识在很多课程中都有涉及到，这里只是说明它的作用，更多信息翻阅一下课本就可以知道了。

我们知道了DNS的任务，那么当我们在浏览器中输入了某个域名之后，它会怎么去寻找对应的IP地址呢？

这个过程大致如下（不准确）：

1. 先在本地的hosts文件中查找是否能够找到对应的IP地址。
2. 如果没有找到，就去询问本地网络设置中的DNS服务器，这个服务器一般如果能找到则告诉你的电脑对应的IP地址是多少，如果没找到它会告诉你应该去找谁（另外一个DNS服务器，貌似直接就是根域服务器？）问这个域名对应的地址。
3. 然后根据上一步得到的DNS服务器地址再次去询问，直到最终找到IP地址或者找完所有的服务器都找不到对应的域名对应的IP地址。

也就是说，浏览器会首先在本地的hosts文件中查找，这也是我们可以设置任意域名作为我们的项目域名的原理。

### hosts文件

hosts文件位于：`C:\Windows\System32\drivers\etc`目录下

让我们先来看一下hosts文件的内容：

```
# Copyright (c) 1993-2009 Microsoft Corp.
#
# This is a sample HOSTS file used by Microsoft TCP/IP for Windows.
#
# This file contains the mappings of IP addresses to host names. Each
# entry should be kept on an individual line. The IP address should
# be placed in the first column followed by the corresponding host name.
# The IP address and the host name should be separated by at least one
# space.
#
# Additionally, comments (such as these) may be inserted on individual
# lines or following the machine name denoted by a '#' symbol.
#
# For example:
#
#      102.54.94.97     rhino.acme.com          # source server
#       38.25.63.10     x.acme.com              # x client host

# localhost name resolution is handled within DNS itself.
#	127.0.0.1       localhost
#	::1             localhost
```

恩...乍看这么多内容，其中#开头的行都表示注释，也就是说现在这个文件中等于没有内容。但是看注释大概也能看出来到底是怎么一回事，每行代表一个ip地址到域名的映射，先写IP地址，再加个空格或者制表符写对应的域名。

我们知道我们本地的IP地址为127.0.0.1，之前我们访问的localhost其实也是代表这个IP地址。那么我们想要一个自定义的域名来访问本地的地址，也是需要让这个域名指向本机的IP地址。所以我们需要在hosts文件的末尾追加下面的内容：

```
127.0.0.1  blog.mine
```

将文件保存（如果遇到无法保存的情况请将此文件的编辑权限打开）后在浏览器输入blog.mine，是不是和输入localhost的效果一样呢？

### apache的vhost配置

现在我们已经让我们访问项目的域名变得随心所欲了，但是还没有让这个域名指向我们的项目目录中，它现在依然指向我们的documentRoot中。

接下来我们将介绍在apache中如何为这个域名设置专门的documentRoot。

首先我们需要找到对应的配置文件，在windows下，phpstudy中，这个文件在`E:\PHPStudy\Apache\conf`目录下，它的文件名是：`vhosts.conf`。在别的集成环境或者别的操作系统中，这个文件一般也都在apache的conf目录下，如果是老一些的版本，可能没有这个文件，但是你可以在httpd.conf文件的末尾找到对应的地方来写接下来的配置。新一些的版本在配置目录下的`extra`目录下可能也会有一个vhost的配置文件，效果一样。

我们还是先来看下这个文件的内容，如果你先前没有配置过的话，它应该是空的。

本文我只介绍如何配置，而且是最简单的配置，如果你想要深入了解下apache的vhost配置，请自行查阅官方文档。

我们在这个文件中写入如下内容：

```
<VirtualHost *:80>
    ServerName blog.mine
    ServerAdmin yanxingmao.parallel@shandagames.com
    DocumentRoot F:\study\hello-world
</VirtualHost>
```

对的，上面这段配置几乎是vhost的最基本的配置了，每个vhost的配置内容都被一对`VirtualHost`标签包裹起来，标签的开始还指出来哪个IP的哪个端口使用这条规则。下面介绍配置项的内容：

- ServerName 代表使用哪个域名访问的时候使用本配置
- ServerAdmin 这个是服务器管理员的邮箱，本地开发随意就好
- DocumentRoot 这个是最重要的，就是它告诉服务器，如果使用ServerName访问的时候去把哪个目录当做我们的Web根目录。

恩恩，好了，我们保存文件并且重新启动apache服务器软件（不是重启计算机）。再次通过浏览器访问我们的blog.mine时会惊奇地发现显示出来的不再跟localhost一样了（当然了，这个时候你访问localhost有可能也会访问到了上面定义的位置，这个是因为apache会将第一个配置的vhost当做默认的vhost来处理，你只需要再为localhost专门设置一个vhost并放到vhost配置文件的最开始就可以了）。

## 操作

貌似已经说完了...
