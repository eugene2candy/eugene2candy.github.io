---
layout: post
title: "CentOS: Kernel(2/3) - I Need to Build a Custom Kernel"
date: 2013-04-23 12:20
author: sonjasper
comments: true
categories: []
tags: []
---
&nbsp;

There are two ways to build a custom kernel for CentOS. One is to build a kernel with custom options from the CentOS sources and the other is to build a mainline kernel using sources obtained from [The Linux Kernel Archive](http://www.kernel.org/).

This tutorial will cover the building of a kernel from the CentOS sources with your own options or modifications.  It is written primarily for CentOS-5. Look out for the sign for notes regarding the building of other versions of CentOS kernels.

(If you wish to build a mainline kernel, ***do not*** follow [How To Compile A Kernel](http://howtoforge.com/kernel_compilation_centos/). That site is not endorsed, as it details building as root **which is unsafe** and flawed in its approach. See [Building Source RPM as non-root under CentOS](http://www.owlriver.com/tips/non-root/) for the detailed reasoning. A good reference for building a mainline kernel is the book [Linux Kernel in a Nutshell](http://www.kroah.com/lkn/).)
<div>
<table>
<tbody>
<tr>
<td>These actions are for your own personal use. Custom kernels are not supported by the development team as they have no control over your build environment, the options chosen, etc. If you choose to build your own kernel, you will be responsible for continuing to maintain it for security updates, new releases, the second coming of $DEITY and any other possible scenario which may warrant such maintenance.</td>
</tr>
</tbody>
</table>
</div>
&nbsp;

<hr />

&nbsp;

&nbsp;
<h2 id="head-72952b3465e2fc1d84e08512448aa60c7a2e6ec2"><span style="color: #800080;">1. Build preparations</span>


To be able to perform a successful kernel build, you will need to install the following packages:


*   <tt>yum groupinstall "Development Tools"</tt> # This will ensure that you have all the required tools for the build.
*   <tt>yum install ncurses-devel</tt> # This is required to enable a *make *config* command to execute correctly.
*   <tt>yum install qt-devel</tt> # This is only necessary if you wish to use *make xconfig* instead of *make gconfig* or *make menuconfig*.
*   <tt>yum install hmaccalc zlib-devel binutils-devel elfutils-libelf-devel</tt> # These are required when building a CentOS-6 kernel.
*   **The full kernel source tree.** Follow the instructions in **Section 2** of **[I Need the Kernel Source](http://wiki.centos.org/HowTos/I_need_the_Kernel_Source)**.
Whist running the above yum commands note if there are any exclude references. If there are 'exclude' lines referring to the kernel and related packages in the yum configuration files, they will also need to
be removed. One should understand that while the CentOS project does not endorse using such excludes, some vendors do not permit user modified kernels to run in their supported environment. For more details on this topic, see [ Section 3, Broken Virtual Server](http://wiki.centos.org/TipsAndTricks/BrokenVserver) article.

Note that it is always wise to have a current and tested backup of any content which you are not willing to lose.

If you have any kernel patches to add, copy them to the SOURCES directory now.

&nbsp;
<h2 id="head-87b9be9f01554c45ebab1d9cf76971348cafcb8e"><span style="color: #800080;">2. Configuring the kernel</span>


If building for a CentOS-6 kernel, replace **2.6.18** with **2.6.32** throughout the rest of this tutorial.
<div>
<table>
<tbody>
<tr>
<td></td>
<td colspan="2">If you you do not intend to modify the distributed kernel configuration file you may omit this section, as long as you perform the equivalent of the final step:

[user@host] $ cp ~/rpmbuild/BUILD/kernel-2.6.18/linux-2.6.18.<tt>`uname -m`</tt>/configs/* ~/rpmbuild/SOURCES</td>
</tr>
</tbody>
</table>
</div>
With the buildroot correctly set up, it's time to modify the kernel configuration. Change directory to ~/rpmbuild/BUILD/kernel-2.6.18/linux-2.6.18.<tt>`uname -m`</tt>/ and copy into this directory, *as **.config***, one of two files:

Either the appropriate **type** of configuration file, from the ./configs/ directory:


*   For CentOS-6: base only (no **type** definition)
*   For CentOS-5: base, xen, or (if the architecture is 32-bit) PAE
Or the currently running kernel's configuration file, from the /boot directory.

See the example below:

&nbsp;


[user@host]$ cd ~/rpmbuild/BUILD/kernel-2.6.18/linux-2.6.18.`uname -m`</pre>
Either -

&nbsp;
<pre>[user@host]$ cp configs/kernel-2.6.18-`uname -m`[-type].config .config</pre>
- or -

&nbsp;
<pre>[user@host]$ cp /boot/config-`uname -r` .config</pre>
First run *make oldconfig*. Now you should run either *make menuconfig*, *make gconfig* or *make xconfig* to customize the kernel configuration. Once complete, remember to save your changes.
<div>
<table>
<tbody>
<tr>
<td></td>
<td colspan="2">If you have installed the full kernel source to **build a kernel module**, you should stop at this point. Now please refer to the [Build Your Own Kernel Modules](http://wiki.centos.org/HowTos/BuildingKernelModules) tutorial.</td>
</tr>
</tbody>
</table>
</div>
Next, add a line that contains the commented out equivalent of the hardware platform to the top of the configuration file (equivalent to the output returned by a *uname -i* command) just before you copy it back to the configs/ directory.  This will either be **i386** for the 32-bit architecture or **x86_64** for the 64-bit architecture.  It needs to be commented out with a # and must be the first line of the file. Note that there must be a space between the hash symbol and the hardware platform descriptor.

Add, as the first line of the .config file, either:

&nbsp;
<pre># i386</pre>
- or -

&nbsp;
<pre># x86_64</pre>
Copy the .config file back to the configs/ directory. This is basically the opposite of the earlier copy command:

&nbsp;
<pre>[user@host]$ cp .config configs/kernel-2.6.18-`uname -m`[-type].config</pre>
The final step is to copy the entire contents of the configs/ directory to the ~/rpmbuild/SOURCES/ directory.

&nbsp;
<pre>[user@host]$ cp configs/* ~/rpmbuild/SOURCES</pre>
&nbsp;
<h2 id="head-566abc4208fceb902d41ee82633f509dbf386a4d"><span style="color: #800080;">3. The kernel ABI</span>
    
    
One feature of the CentOS kernel is that its ABI will be preserved for the entire life of the product and an advantage of having a consistent ABI is that external kernel modules can be built which are independent of the kernel version -- hence they do not need to be rebuilt for each new kernel released. This is the basis of the  [ kABI tracking kmod package](http://wiki.centos.org/HowTos/BuildingKernelModules#head-b86b6eec08d5719cf1838929f26a64af88e2b7f0) -- to provide updated device drivers and other file system support, for example.

To maintain this ABI consistency, the original kernel's ABI was recorded and stored in a file. This file is used during the kABI checking step for each new kernel being built. If the new kernel has been configured or modified in such a way that it is at variance with the published ABI, the build will fail with a message indicating that kABI breakage has occurred. The kernel builder will then have two choices: (a) re-configure the new kernel so that it does agree with the published ABI and, hence, continue to enjoy the benefits that a consistent ABI provides or (b) disable kABI checking during the build process. Of the two choices, the former is desired but, just occasionally, the latter is the only way to proceed.

Disabling the kernel ABI checking is a simple process of supplying a flag and its argument on the rpmbuild command line:

&nbsp;
<pre>--without kabichk</pre>
&nbsp;
<h2 id="head-9cdd87b12cf068c1b9a2aa018c8a37fbedf2cfe6"><span style="color: #800080;">4. Modifying the kernel specification file</span>
    
    
<div>
<table>
<tbody>
<tr>
<td></td>
<td colspan="2">The line numbers mentioned in this section relate to the **current** CentOS kernel specification files only.</td>
</tr>
</tbody>
</table>
</div>
Now you will need to modify the kernel specification file.

&nbsp;
<pre>[user@host]$ cd ~/rpmbuild/SPECS
[user@host SPECS]$ cp kernel.spec kernel.spec.distro
[user@host SPECS]$ vi kernel.spec</pre>
At line 74 (CentOS-5) or line 18 (CentOS-6), the definition of *buildid* is commented out. This must be uncommented and given a value to avoid a conflict with your currently installed kernel. Change the line in similar manner to the example below:

&nbsp;
<pre>%define buildid .your_identifier</pre>
<div>
<table>
<tbody>
<tr>
<td></td>
<td colspan="2">There should be no space between the "%" and the word "define".</td>
</tr>
</tbody>
</table>
</div>
The following step is required to build CentOS-5 kernels only.

Starting at line 698 there is a block of 25 lines of code that needs to be commented out to build customised CentOS-5 kernels:

&nbsp;
<pre>#if a rhel kernel, apply the rhel config options
#%if 0%{?rhel}
#  for i in %{all_arch_configs}
#  do
#    mv $i $i.tmp
#    $RPM_SOURCE_DIR/merge.pl $RPM_SOURCE_DIR/config-rhel-generic $i.tmp &gt; $i
#    rm $i.tmp
#  done
#%ifarch x86_64 noarch
#  for i in kernel-%{kversion}-x86_64*.config
#  do
#    mv $i $i.tmp
#    $RPM_SOURCE_DIR/merge.pl $RPM_SOURCE_DIR/config-rhel-x86_64-generic $i.tmp &gt; $i
#    rm $i.tmp
#  done
#%endif
#%ifarch ppc64 noarch
#  #CONFIG_FB_MATROX is disabled for rhel generic but needed for ppc64 rhel
#  for i in kernel-%{kversion}-ppc64.config
#  do
#    mv $i $i.tmp
#    $RPM_SOURCE_DIR/merge.pl $RPM_SOURCE_DIR/config-rhel-ppc64-generic $i.tmp &gt; $i
#    rm $i.tmp
#  done
#%endif
#%endif</pre>
Finally if you have any patches to apply, you need to make reference to them in two places. First, in CentOS-5, after line 426 which should be near the end of the patch declarations, add your declaration starting with the number 40000, so that *your* patch is not in any danger of conflicting with the RHEL/CentOS kernel patch space. For example:

&nbsp;
<pre>Patch40000: my-custom-kernel.patch</pre>
In CentOS-6, add the same line just before line 604 which reads, "# empty final patch file ..."

Secondly, in CentOS-5, after line 682  add the line to apply your patch. All you need to do is add is the patch number you declared earlier and rpmbuild will automagically apply it for you. For example:

&nbsp;
<pre>%patch40000 -p1</pre>
In CentOS-6, just before line 904 which reads, "ApplyOptionalPatch ...", *either* add the same line as that for CentOS-5 *or* use the following format:

&nbsp;
<pre>ApplyOptionalPatch my-custom-kernel.patch</pre>
The following modifications to the spec file are required in CentOS-6 only.


*   **Replace** line 898:
&nbsp;
<pre>cp $RPM_SOURCE_DIR/config-* .</pre>
with:
<pre>cp $RPM_SOURCE_DIR/kernel-*.config .</pre>


*   **Remove** line 902 that reads:
&nbsp;
<pre>make -f %{SOURCE20} VERSION=%{version} configs</pre>
&nbsp;
<h2 id="head-1bdbef9520f6f16073699cec5681ad79077ba79b"><span style="color: #800080;">5. Building the new kernel</span>
    
    
Start the build:

&nbsp;
<pre>[user@host SPECS]$ rpmbuild -bb --target=`uname -m` kernel.spec 2&gt; build-err.log | tee build-out.log</pre>
For kernels &gt;= 2.6.18-53.el5, you can add some useful options to the rpmbuild command by using the --with and/or --without flags and associated arguments. The options to note are:

&nbsp;
<pre>--with baseonly
--with xenonly
--without up
--without xen
--without debug
--without debuginfo
--without fips
--without kabichk</pre>
For example, to build just the base kernel packages use:

&nbsp;
<pre>--with baseonly --without debug --without debuginfo</pre>
To build just the xen kernel packages use:

&nbsp;
<pre>--with xenonly --without debug --without debuginfo</pre>
To build just the PAE kernel packages use:
<pre>--without up --without xen --without debug --without debuginfo

When the build completes, your custom kernel rpm files will be found in the ~/rpmbuild/RPMS/<tt>`uname -m`</tt>/ directory. Make sure that you install those files, as **root**, using an *rpm -ivh kernel-*.rpm *command. Note: If you have built a kernel version that is older than a currently installed version you will also have to use the *--oldpackage* flag with the rpm command.

**UNDER NO CIRCUMSTANCES** use an *rpm -Uvh* command to install your kernel as this will update (overwrite) the currently installed version.  Hence if you have a problem with your custom kernel, you will not be able to revert to the previous, working, version.
