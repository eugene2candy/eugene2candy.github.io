---
layout: post
title: "CentOS: Kernel (1/3) - I Need the Kernel Source"
date: 2013-04-22 20:04
author: sonjasper
comments: true
categories: []
tags: []
---


## 




## <span style="color: #800080;">1. Maybe you do not need the full kernel source</span>


If you need to compile a kernel **driver** module, the chances are you **do not** really need the **full** kernel source tree.  You might just need the **kernel-devel** package. (If, however, you are certain that the full source tree is required, please follow the instructions in Section 2.)

In CentOS 6, there is just one kernel-devel package available:


*   kernel-devel (both 32- &amp; 64-bit architectures)
In CentOS 5, there are three kernel-devel packages available:


*   kernel-devel (both 32- &amp; 64-bit architectures)
*   kernel-xen-devel (both 32- &amp; 64-bit architectures)
*   kernel-PAE-devel (32-bit architecture only)
If you are running the <tt>standard</tt> kernel (for example), you can install the kernel-devel package by:

&nbsp;


[root@host]# yum install kernel-devel</pre>
You can use this command to determine the version of your running kernel:

&nbsp;
<pre>[root@host]# uname -r</pre>
The result will look similar to this:

&nbsp;
<pre>2.6.18-348.4.1.el5xen</pre>
In this case, the xen kernel is installed and the way to install this specific kernel-devel package is:

&nbsp;
<pre>[root@host]# yum install kernel-xen-devel</pre>
Please ensure that you install the *kernel-devel* package that **matches** your running kernel. See [this FAQ](http://wiki.centos.org/FAQ/CentOS5#head-a6c98b9ff863c83b342649f3373a14bcab76436b) for details.

If your kernel is not listed by [yum](http://wiki.centos.org/PackageManagement/Yum) because it is in an older tree, you can download it manually from the [CentOS Vault](http://vault.centos.org/).  Pick the *version* of CentOS you are interested in and then, for the *arch*, look in either the **os/***arch*/**CentOS**/**RPMS**/ or the **updates**/*arch*/**RPMS**/ directories for the **kernel**[-*type*]-**devel**-*version.arch***.rpm**

Once you have the proper **kernel**[-*type*]-**devel**-*version.arch***.rpm** installed, try to compile your module.  It should work this way.  If it does not, please provide feedback to the module's developer as this is the way all new kernel modules should be designed to be built.

&nbsp;
<h2 id="head-a8dae925eec15786df9f6f8c918eff16bf67be0d"><span style="color: #800080;">2. If you really need the full kernel source</span>
    
    
If you really must have the kernel source tree, for whatever reason, it is obtainable.

As an ordinary user, **not root**, create a build tree based on a ~/rpmbuild/ directory:

&nbsp;
<pre>[user@host]$ mkdir -p ~/rpmbuild/{BUILD,BUILDROOT,RPMS,SOURCES,SPECS,SRPMS}
[user@host]$ echo '%_topdir %(echo $HOME)/rpmbuild' &gt; ~/.rpmmacros</pre>
You are **strongly** advised **against** package building as root. (See: [Building Source RPM as non-root under CentOS](http://www.owlriver.com/tips/non-root/))

&nbsp;

<hr />

&nbsp;

**To install the source package and tools for CentOS 6**:

As root, install the rpm-build, redhat-rpm-config, asciidoc, hmaccalc, perl-ExtUtils-Embed, xmlto, binutils-devel, elfutils-libelf-devel, newt-devel, python-devel and zlib-devel packages:

&nbsp;
<pre>[root@host]# yum install rpm-build redhat-rpm-config asciidoc hmaccalc perl-ExtUtils-Embed xmlto 
[root@host]# yum install binutils-devel elfutils-libelf-devel newt-devel python-devel zlib-devel</pre>
Find the kernel source rpm package in:


*   [http://vault.centos.org/6.N/os/Source/SPackages/](http://vault.centos.org/6.N/os/Source/SPackages/)
*   [http://vault.centos.org/6.N/updates/Source/SPackages/](http://vault.centos.org/6.N/updates/Source/SPackages/)
(Replace the "N" with the relevant update version number.)

As an ordinary user, **not root**, install the source package by executing:

&nbsp;
<pre>[user@host]$ rpm -i http://vault.centos.org/6.4/updates/Source/SPackages/kernel-2.6.32-358.2.1.el6.src.rpm 2&gt;&amp;1 | grep -v mock</pre>
&nbsp;

&nbsp;

&nbsp;

<hr />

&nbsp;

**To install the source package and tools for CentOS 5**:

As root, install the rpm-build, redhat-rpm-config and unifdef packages:

&nbsp;
<pre>[root@host]# yum install rpm-build redhat-rpm-config unifdef</pre>
Find the kernel source rpm package in:


*   [http://vault.centos.org/5.N/os/SRPMS/](http://vault.centos.org/5.N/os/SRPMS/)
*   [http://vault.centos.org/5.N/updates/SRPMS/](http://vault.centos.org/5.N/updates/SRPMS/)
(Replace the "N" with the relevant update version number.)

As an ordinary user, **not root**, install the source package by executing:

&nbsp;
<pre>[user@host]$ rpm -i http://vault.centos.org/5.9/updates/SRPMS/kernel-2.6.18-348.4.1.el5.src.rpm 2&gt;&amp;1 | grep -v mock</pre>
&nbsp;

&nbsp;

&nbsp;

<hr />

&nbsp;

**Now that the source package and tools are installed, unpack and prepare the source files**:

&nbsp;
<pre>[user@host]$ cd ~/rpmbuild/SPECS
[user@host SPECS]$ rpmbuild -bp --target=$(uname -m) kernel.spec

The value of $(uname -m) sets the target to the architecture of your current kernel. This is generally accepted, as most people will need either i686 or x86_64 as the target.

The kernel source tree will now be found under the <tt>~/rpmbuild/BUILD/kernel*/linux*/</tt> directory.
