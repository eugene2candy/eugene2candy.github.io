---
layout: post
title: "Windows 8系统安全之我所见/Part 1：起源"
date: 2013-10-21 14:35
author: sonjasper
comments: true
categories: []
tags: []
---
<span style="color: #ff0000;">1995`</span>英特网起步，传统邮件业务濒临崩溃。
此时Windows处于95时代。

<span style="color: #ff0000;">2001`</span>计算机病毒威胁（Melissa，Love Letter War）
XP系统，建立起访问控制、用户配置文件、安全策略、加密文件系统、Smartcard和PKI支持以及Windows更新，共同抵御病毒的侵害。

<span style="color: #ff0000;">2004`</span>Code Red和Nimda病毒，Blaster以及Slammer病毒横行。网络黑客的大批量崛起，从篡改到攻击，主要攻击缓冲区的溢出。
此时XP发布SP2更新，增加了地址空间布局随机化（ASLR），数据执行保护（DEP），安全开发生命周期（SDL），默认启动自动更新，默认启动防火墙，Windows安全中心，WPA支持。

<span style="color: #ff0000;">2007`</span>Zotob，Attacks病毒，0day漏洞，隐匿程式病毒，用户可任意以管理员身份运行。
Vista诞生，新增了Bitlocker驱动器加密，Patchguard内核保护，ASLR和DEP功能得以加强，完整的安全开发生命周期，用户账户控制，IESmartScreen筛选器，数字权利管理，防火墙得以改进，已签名的装置驱动程序要求，TPM支持，Windows完整性级别，默认安全配置。这一系列安全措施与改进帮助Windows用户安全使用计算机。

<span style="color: #ff0000;">2009`</span>Botnets威胁，身份盗窃，Conficker病毒侵袭。
Windows 7氤氲而生。ASLR和DEP功能继续完善，完全的安全开发生命周期，经过改进的IPSec堆栈，服务账户管理，用户账户控制功能得以改进，审核功能得以改进，IESmartScreen筛选器的完善，AppLocker应用程序控制策略，BitLocker驱动器加密扩展功能，Windows生物识别服务，Windows操作中心的引入，Windows Defender防护软件的诞生，微软将系统安全防护作出了巨大推进。

<span style="color: #ff0000;">2012`</span>Operation Aurora与Stuxnet病毒。
Windows 8华丽登场。UEFI安全启动，基于固件的TPM，可信引导（w/ELAM），标准引导和远程认证支持，对ASLR和DEP功能做出了重要改进，AppContainer，Windows应用商店的引入，IE10推出，应用程序信用度功能转至中心操作系统，BitLocker（只对加密的硬盘驱动器以及使用的硬盘空间实行加密支持），虚拟智能卡，图片密码和PIN，动态访问控制，内置反恶意软件。

简要的系统防护工作原理：
1、  恶意软件阻止功能
2、  加密技术
3、  访问控制

<span style="color: #ff0000;">**小结：**</span>Microsoft针对当下的互联网威胁，对Windows系统不断进行改进与完善，推出一个又一个安全性功能保障了每一个Windows用户的信息安全，中国从Windows 95开始，计算机渐渐进入每个普通家庭，到XP，计算机真正意义上进入了每个家庭的生活。随着时代的发展，伴随着互联网的快速兴起，以经济或者政治的犯罪正通过互联网进行着，信息安全关系着每一个人，系统安全应该是每个人所要关心的。微软后续推出的Vista，Win 7，或成功或“失败”，每个产品的诞生总会伴随掌声和嘘声，Windows 8的登场又一次遭遇大量用户的抨击，我在这里只以系统安全的角度出发，对于Win 8的出现，我们应当欢迎，因为它不仅仅在系统安全上作出了更大的努力，同时更带来了更加安全的功能。XP依然落下帷幕，那些坚守XP阵地的朋友，请不要再固执，为了更安全地使用互联网，更新吧。

（博主版权所有未经同意请勿转载）
