---
layout: post
title: "CentOS Wiki：Skype on CentOS-6"
date: 2013-04-06 15:04
author: sonjasper
comments: true
categories: []
tags: []
---
&nbsp;

The Linux version of Skype 4 is available. However, there's no binary RPM for CentOS. You can install the **static** version by following these instructions. The instructions assume that you already have the packages included in a default **Desktop** installation:


>


[root@host]# yum groupinstall Desktop</pre>

    
    
The available generic Skype binaries are 32-bit, so if you're running a **64-bit** system, you need to make sure you have various 32-bit libraries installed.

First, update the packages that are already installed:

    
    >
<pre>[root@host]# yum update</pre>

    
    
Then, install the new packages you may need:

    
    >
<pre>[root@host]# yum install glibc.i686 nss-softokn-freebl.i686 alsa-lib.i686 libXv.i686 
libXScrnSaver.i686 libtiff.i686 glib2.i686 libSM.i686 libXrender.i686 fontconfig.i686</pre>

    
    
Some users have reported that they must also install the following packages for sound to work:

    
    >
<pre>[root@host]# yum install pulseaudio-libs.i686 alsa-plugins-pulseaudio.i686</pre>

    
    
All these packages are in the **Base** and **Updates** repositories, which are enabled by default.
<h3 id="head-a945b75db3a2300e8e1b7b2b51e0e9c201501a6e"><span style="color: #000080;">1.Installing Skype</span>
    
    

    
    >Skype 4.1 for Linux was released in November 2012. There's no static version, and it requires Qt 4.7, which isn't available from the official CentOS repositories. It is suggested, at this time, to install version 4.0.0.8, which is still available at [http://download.skype.com/linux/skype_static-4.0.0.8.tar.bz2](http://download.skype.com/linux/skype_static-4.0.0.8.tar.bz2)
    
    
Installing to /opt (modify as desired):

    
    >
<pre>[root@host]# cd /tmp
[root@host]# ## wget http://www.skype.com/go/getskype-linux-beta-static  ## Use the following command for now instead
[root@host]# wget http://download.skype.com/linux/skype_static-4.0.0.8.tar.bz2
[root@host]# cd /opt
[root@host]# tar xjvf /tmp/skype_static-4.0.0.8.tar.bz2
[root@host]# rm /tmp/skype_static-4.0.0.8.tar.bz2
[root@host]# ln -s skype_staticQT-4.0.0.8 skype</pre>

    
    
You need to replace the version number to match the one downloaded. At the time of writing (September 2012), the latest version is 4.0.0.8.

Set up some symlinks (the first is required for sound to work; the second is needed to avoid the error "libtiff.so.4: cannot open shared object file"; the third is optional).

    
    >
<pre>[root@host]# ln -s /opt/skype /usr/share/skype
[root@host]# ln -s /usr/lib/libtiff.so.3 /usr/lib/libtiff.so.4
[root@host]# ln -s /opt/skype/skype /usr/bin/skype</pre>

    
    
**Note:** Use "Sound Preferences" to select the correct input/output audio devices.
<h3 id="head-fbd1b09d898f7ed7550513f34c82b451563272df"><span style="color: #000080;">2.Skype video with CentOS x86_64</span>
    
    
If you're running a **64-bit** system and video doesn't work with Skype, install the 32-bit **libv4l** package:

    
    >
<pre>[root@host]# yum install libv4l.i686</pre>

    
    
Then, to run Skype, load the 32-bit v4l1compat.so:

    
    >
<pre>[user@host]$ LD_PRELOAD=/usr/lib/libv4l/v4l1compat.so /opt/skype/skype




<h3 id="head-af82666e22cc82c01baf6e9ae88c5d2981c7635b"><span style="color: #000080;">3.Skype and NFS</span>


If you're using NFS, see the comment from **sipan_** dated 19 June at [http://blogs.skype.com/linux/2012/06/skype_40_for_linux.html](http://blogs.skype.com/linux/2012/06/skype_40_for_linux.html)
<h3 id="head-177735ac44d30e5df3c59d6c7917d01eb8245140"><span style="color: #000080;">4.Conflict between Skype and Wine</span>


There's a conflict between Skype and Wine (more precisely, between Skype and <tt>pulseaudio-libs.i686</tt>, on which Wine depends).

This conflict has at least two consequences:


>


*   Skype's sound quality is very poor.
*   One cannot choose different devices for "Ringing" and "Speakers", for example loudspeakers for "Ringing" and a headset for "Speakers".



The work-around is to remove <tt>pulseaudio-libs.i686</tt>. Of course, this will also make Wine unavailable.

（via：[http://wiki.centos.org/HowTos](http://wiki.centos.org/HowTos)）
