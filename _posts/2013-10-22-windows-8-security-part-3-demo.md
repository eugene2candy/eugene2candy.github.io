---
layout: post
title: "关于【Part 3：强制完整性】论述中的官方demo演示"
date: 2013-10-22 15:48
author: sonjasper
comments: true
categories: []
tags: []
---
原文：


>1) Launch Notepad

2) Enter some text and save the file as “test.txt” on your desktop. Now we have a text file with the default Medium IL.

3) Launch a command prompt as Administrator (from the shortcut, right click and choose “Run As Administrator”)

4) Navigate to your desktop (“cd C:UsersUSER_NAMEDesktop” in the command line for a default Vista installation).

5) Issue this command to set the created file’s integrity level to a High IL: “icacls test.txt /setintegritylevel H”. Now that the file has a high integrity level a default notepad process won’t be able to save the file.

6) Launch Notepad again (make sure it isn’t launched as Administrator since Administrator permissions will make it have an integrity level of High, voiding this experiment).

7) Open test.txt from your desktop

8) Change the file and try to save it. Notepad will fail while trying to save (Notepad gives a “user-friendly” error, but the underlying error is actually “Access Is Denies”).


&nbsp;

在桌面上新建记事本之后，管理员模式启动命令提示符。

cd进桌面，运行“icacls test.txt /setintegritylevel H”使得刚才新建的记事本强制完整性级别为【高】。

此时在记事本随便写入内容之后，你会发现无法保存该记事本。

效果如下图所示：

<a href="http://ec2-54-64-38-60.ap-northeast-1.compute.amazonaws.com/blog/wp-content/uploads/2013/10/demo.jpg">![强制完整性_demo](http://sonjasper.com/blog/wp-content/uploads/强制完整性_demo-1024x575.jpg)</a>

&nbsp;
