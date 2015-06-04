---
layout: post
title: "WordPress出现 Forbidden: Your don`t have permission to access /??? on this server解决方案"
date: 2013-03-19 21:14
author: sonjasper
comments: true
categories: []
tags: []
---
网上有很多人遇到这个问题，

也提出不少解决方案，但是可能每个人遇到的问题并不相同。

我这里不一一列出可能出现的状况跟解决方法，因为其他人已经提出，大家可以google搜索。

我这里只说一种其他人没提过的情况。

这种情况应该常见于将wordpress转移空间。。

且原wp应该处于插件加密状态，这时候，当输入链接&ldquo;ip/priblog/index.php&rdquo;的时候，由于index已经被加密插件保护，无法访问，这应该是虚拟空间权限的问题，因为我从VPS转移到虚拟空间的时候就遇到这个问题（个人认为是VPS某些权限高于虚拟空间，毕竟虚拟空间出于安全考虑的因素比较多...）。先说明一下我的空间布局。在sonjasper.com下有两个文件夹，一个blog和一个priblog，后者是用插件加密的，sonjasper.com首页有两张图片，后者超链接为主页所在文件夹的/priblog/index.php，点击之后可能是由于虚拟空间的权限，导致该加密插件无法跳转，而index.php又无法读取，故产生了错误页面。

因此我将超链接改成了/priblog，去掉了index.php，让访问请求自己去请求访问。然后就可以正常读取网页的加密页，解除forbidden故障了~



