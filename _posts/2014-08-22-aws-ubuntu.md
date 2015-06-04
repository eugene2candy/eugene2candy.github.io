---
layout: post
title: "AWS下部署Ubuntu搭建WordPress完整环境与ftp配置详解"
date: 2014-08-22 09:41
author: sonjasper
comments: true
categories: []
tags: []
---
**转发请注明出处：<a title="http://sonjasper.com/blog/?p=422" href="http://sonjasper.com/blog/?p=422" target="_blank"><span style="color: #666666;">http://sonjasper.com/blog/?p=422</span></a>**

putty的使用就不解释了。

Ubuntu下搭建wordpress环境：
<div>**1.安装apache2**</div>

sudo apt-get install apache2

(重启apache2

sudo /etc/init.d/apache2 restart

<div>**2.安装php**</div>

sudo apt-get install php5 //安装PHP5

sudo apt-get install libapache2-mod-php5 //配置APACHE+PHP

sudo /etc/init.d/apache2 restart //重启apache

<div>**3.安装mysql**</div>

sudo apt-get install mysql-server

<div>**4.让apache、php支持mysql**</div>

sudo apt-get install libapache2-mod-auth-mysql

sudo apt-get install php5-mysql

sudo /etc/init.d/apache2 restart

<div>至此apache2+php5+mysql5环境建好</div>

<div>**5.安装phpmyadmin**</div>

sudo apt-get install phpmyadmin

<div>此时phpmyadmin被安装到/usr/share/phpmyadmin</div>
    
<div>需要去/var/www/html做一个phpmyadmin的超链接</div>

sudo ln -s /usr/share/phpmyadmin

<div>**6.建数据库，可以用phpmyadmin**</div>
    
<div>启动 sudo start mysql</div>

<div>是否开启 pgrep mysqld</div>

<div>查看 show databases;</div>

<div>进入 mysql -u root -p</div>

<div>创建 create database name</div>

<div>删除 drop database name</div>

<div>**7.解压wordpress**</div>

sudo tar -zxvf wordpress-3.8-zh_CN.tar.gz

<div>**8.移动**</div>

sudo cp -a ./wordpress /var/www


<div>**修改文件权限，在某个目录下，可以把当前目录与子目录文件全部改成777权限：**</div>

<div>chmod 777 -R *</div>

<div>修改某一个文件的权限：</div>

<div>chmod 644 name</div>


<div>**wordpress无更新下载权限：**</div>

<div>ps -aux 可以查看到apache2的用户是www-data</div>

<div>因此 sudo chown -R www-data /var/www/html/你的博客位置</div>


<div>**架设FTP：**</div>

<div>安装 sudo apt-get install vsftpd</div>

<div>/etc/vsftpd.conf 中修改</div>

<div>listen=YES</div>

<div>anonymous_enable=NO #一定要是no，然后别人就不能用ftp匿名账户登录了#</div>

<div>lacal_enable=YES</div>

<div>write_enable=YES</div>

<div>dirmessage_enable=YES</div>

<div>use_localtime=YES</div>

<div>xferlog_enable=YES</div>

<div>connect_from_port_20=YES</div>

<div>ascii_upload_enable=YES</div>

<div>ascii_download_enable=YES</div>

<div>chroot_local_user=YES</div>

<div>chroot_list_enable=YES</div>

<div>chroot_list_file=/etc/vsftpd/chroot_list</div>

<div>secure_chroot_dir=/var/run/vsftpd/empty</div>

<div>pam_service_name=vsftpd</div>

<div>ras_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem</div>

<div>ras_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key</div>

<div>pasv_enable=YES</div>

<div>pasv_min_port=1024</div>

<div>pasv_max_port=1048</div>

<div>pasv_addr_resolve=YES</div>

<div>pasv_address="PUBLIC DNS"</div>

<div>userlist_enable=YES</div>

<div>userlist_deny=NO</div>

<div>userlist_file=/etc/vsftpd/user_list</div>


<div>local_root=/var/www</div>

<div>chroot_local_user=YES</div>

<div>anon_root=/var/www **此三行是目录修改**</div>


<div>anon_mkdir_write_enable=YES</div>

<div>anon_other_write_enable=YES</div>

<div>anon_upload_enable=YES</div>

<div>同时需要在/etc中建立一个vsftpd文件夹</div>

<div>文件夹中创建两个文件chroot_list和user_list，</div>

<div>其中chroot_list不需要填写内容，</div>

<div>在user_list里写上你在下面步骤中创建的user名字。</div>


<div>**创建ftp用户(此步骤很重要)：**</div>

<div>sudo useradd -s /sbin/nologin -d /var/www -g ftp user  #user改成你想要的名字#</div>

<div>sudo passwd user #上面你写的名字，回车后输入两次密码#</div>

<div>sudo chmod 755 /var/www #修改www/文件夹权限为755#</div>

<div></div>

<div></div>

<div>**重启vsftpd：**</div>

<div>service vsftpd restart</div>

如果你发现做完上述步骤FTP登陆不了，去删除/etc/pam.d/vsftpd这个文件，重启服务

<div></div>

<div>**开启端口：**</div>

<div>20-21</div>

<div>1024-1048</div>

<div>22</div>

<div>80</div>

<div>443</div>

<div>3306</div>

<div>3389</div>


<div>**绑定域名，去/etc/apache2/sites-available中修改000-default.conf**</div>

<div>新增：</div>

<div>&lt;VirtualHost 主机的地址或者DNS&gt;</div>

<div>ServerName 域名或者子域名</div>

<div>DocumentRoot 具体路径如/var/www/html/</div>

<div>完了之后重启apache：</div>

<div>sudo /etc/init.d/apache2 restart</div>
