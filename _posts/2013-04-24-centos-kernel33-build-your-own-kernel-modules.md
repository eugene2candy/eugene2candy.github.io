---
layout: post
title: "CentOS: Kernel(3/3) - Build Your Own Kernel Modules"
date: 2013-04-24 11:17
author: sonjasper
comments: true
categories: []
tags: []
---
&nbsp;

There are occasions when you need to modify a kernel module or create a new one.  This may be to add certain features or simply to apply a patch.  In this tutorial, we will attempt to apply a bug fix to an existing module and then install it.

It is assumed that you already have the full kernel source tree installed. If you followed **Section 2** of [I need the Kernel Source](http://wiki.centos.org/HowTos/I_need_the_Kernel_Source), it will be found in the directory <tt>~/rpmbuild/BUILD/kernel-2.6.18/linux-2.6.18.</tt><tt>`uname -m`</tt><tt>/.</tt> Throughout this tutorial a system with 32-bit architecture is assumed. Hence the string returned by <tt>`uname -m`</tt> is i686 and the directory referenced above will be <tt>~/rpmbuild/BUILD/kernel-2.6.18/linux-2.6.18.i686/</tt>.

You are **strongly** advised **against** performing module building as root. (See: [Building Source RPM as non-root under CentOS](http://www.owlriver.com/tips/non-root/))

&nbsp;
<h2 id="head-d2e4c05886f94c701e4ae74387d41d8c40c25d01"><span style="color: #800080;">1. Building a kernel module (*.ko)</span>


Let us assume, as an example, you are applying a patch to the cifs module which is located in the <tt>~/rpmbuild/BUILD/kernel-2.6.18/linux-2.6.18.i686/fs/cifs/</tt> directory.

1. Apply the patch(es) to the source file(s) as required.

2. Change to the **root directory of the kernel source **and, if this is the first time the kernel has been compiled, configure the kernel remembering to ensure that the relevant component of the kernel is set, within its configuration file (.conf), to be built as a module.

&nbsp;


[user@host]$ cd ~/rpmbuild/BUILD/kernel-2.6.18/linux-2.6.18.i686
[user@host linux-2.6.18.i686]$ make oldconfig
[user@host linux-2.6.18.i686]$ make menuconfig
[user@host linux-2.6.18.i686]$ make prepare</pre>
3. Create the files required for compiling external modules.

&nbsp;
<pre>[user@host linux-2.6.18.i686]$ make modules_prepare</pre>
Note: *make modules_prepare* will not build a Module.symvers file even if the parameter CONFIG_MODULEVERSIONING has been set. Therefore a full kernel build will have to have been executed previously to make module versioning work. For more details, see Sections #2.4 &amp; #7 of <tt>/usr/share/doc/kernel-doc-2.6.18/Documentation/kbuild/modules.txt</tt>

4. Compile the module by specifying the relative path to the module’s Makefile and source code.

&nbsp;
<pre>[user@host linux-2.6.18.i686]$ make M=fs/cifs</pre>
Note: The whole directory containing the module can be located anywhere. If, for example, it is in <tt>~/mycifs</tt>/ run the following command making sure that you are still in the **root directory of the kernel source**.

&nbsp;
<pre>[user@host linux-2.6.18.i686]$ make M=~/mycifs</pre>
5. Unless you have compiled this module for debugging purposes, it should now be stripped of unnecessary symbols.

&nbsp;
<pre>[user@host linux-2.6.18.i686]$ strip --strip-debug fs/cifs/cifs.ko</pre>
6. In this example, the file cifs.ko has just been created. As **root**, copy the .ko file to the /lib/modules/&lt;*kernel-version*&gt;/extra/ directory.

&nbsp;
<pre>[root@host linux-2.6.18.i686]# cp fs/cifs/cifs.ko /lib/modules/`uname -r`/extra</pre>
For further details of the directories into which modules are installed, see Section #6 of <tt>/usr/share/doc/kernel-doc-2.6.18/Documentation/kbuild/modules.txt</tt>

7. As **root**, run the depmod command to update the module dependency files.

&nbsp;
<pre>[root@host linux-2.6.18.i686]# depmod -a</pre>
8. A further few steps are required to build the different modules for the different types of kernel. Edit the main Makefile in the** root directory of the kernel source**. For CentOS 5, find:

&nbsp;
<pre>EXTRAVERSION = -prep</pre>
and replace it with (as appropriate):

&nbsp;
<pre>EXTRAVERSION = -348.4.1.el5
EXTRAVERSION = -348.4.1.el5PAE
EXTRAVERSION = -348.4.1.el5xen
EXTRAVERSION = -348.4.1.el5.centos.plus
EXTRAVERSION = -348.4.1.el5.centos.plusPAE
EXTRAVERSION = -348.4.1.el5.centos.plusxen</pre>
The number is not very important if you are going to use modprobe to install the module. (A <tt>-348.el5</tt> module will install into a <tt>-348.4.1.el5</tt> kernel, etc.). **However the PAE and xen modifiers are required to be used for those types of kernels**.

Hint: After compiling the module for the standard kernel, edit the <tt>EXTRAVERSION</tt> line to add **PAE** or **xen** and run <tt>make modules_prepare</tt>.  Then run <tt>make M=xxx</tt> to compile the module for the kernel version specified on the <tt>EXTRAVERSION</tt> line.

&nbsp;
<h2 id="head-d313bd351f90d4f25a2143b7bbcff73f927731f0"><span style="color: #800080;">2. Building a kernel module using Dynamic Kernel Module Support (DKMS)</span>
    
    
The method described above builds a module for a particular kernel version.  If you upgrade the kernel or change the hardware architecture, you will have to manually build the module once again. The dynamic kernel module support (DKMS) framework is basically a duplicate tree, outside of the kernel source, that holds the source and compiled binaries for a particular module. DKMS can be called on to build, install or uninstall modules. DKMS requires the module source code to be located on your system. The DKMS binary takes care of building and installing the module(s) into any kernel(s) you may have on your system.

Here we will use the same example as above and build &amp; install the <tt>cifs</tt> module. You will need to assume **root**'s powers for this entire section.

1. Install the <tt>kernel-devel</tt> package that matches your current kernel.

2. Install the <tt>dkms</tt> package from the [Repositories/RPMForge](http://wiki.centos.org/Repositories/RPMForge) repository.

3. Create a directory <tt>/usr/src/&lt;module&gt;-&lt;module-version&gt;/</tt>

&nbsp;
<pre>[root@host]# mkdir /usr/src/cifs-1.45fixed/</pre>
4. Copy the module's source code to that directory.

&nbsp;
<pre>[root@host]# cd ~/rpmbuild/BUILD/kernel-2.6.18/linux-2.6.18-i686/fs/cifs
[root@host cifs]# cp -a * /usr/src/cifs-1.45fixed/</pre>
5. Create a <tt>dkms.conf</tt> file in the directory <tt>/usr/src/&lt;module&gt;-&lt;module-version&gt;/</tt>

&nbsp;
<pre>[root@host cifs]# cd /usr/src/cifs-1.45fixed
[root@host cifs-1.45fixed]# vi dkms.conf</pre>
The <tt>dkms.conf</tt> file will need to contain these lines:

&nbsp;
<pre>PACKAGE_NAME="cifs"
PACKAGE_VERSION="1.45fixed"
BUILT_MODULE_NAME[0]="cifs"
DEST_MODULE_LOCATION[0]="/kernel/fs/cifs/"
AUTOINSTALL="yes"</pre>
Note: The <tt>DEST_MODULE_LOCATION</tt>[0] line will be ignored during module installation because this will always be to the /lib/modules/&lt;*kernel-version*&gt;/extra/ directory. This parameter, however, will be used when a module is uninstalled as the location to which the stored, old module (if any) will be restored.

6. Add the <tt>&lt;module&gt;/&lt;module-version&gt;</tt> to the DKMS tree.

&nbsp;
<pre>[root@host cifs-1.45fixed]# dkms add -m cifs -v 1.45fixed</pre>
7. Compile the module, under DKMS control.

&nbsp;
<pre>[root@host cifs-1.45fixed]# dkms build -m cifs -v 1.45fixed</pre>
8. Install the module, again under DKMS control.

&nbsp;
<pre>[root@host cifs-1.45fixed]# dkms install -m cifs -v 1.45fixed</pre>
Other DKMS actions to note are <tt>uninstall</tt>, <tt>remove</tt>, <tt>status</tt> and <tt>mkrpm</tt> which uninstalls the module from the kernel, removes the <tt>&lt;module&gt;/&lt;module-version&gt;</tt> from the DKMS tree, displays the current DKMS status of the system and creates a rpm file in the directory <tt>/var/lib/dkms/&lt;module&gt;/&lt;module-version&gt;/rpm/</tt> respectively.

See also:


*   The man page [http://linux.dell.com/dkms/manpage.html](http://linux.dell.com/dkms/manpage.html)
*   The white paper [http://linux.dell.com/dkms/dkms-ols2004.pdf](http://linux.dell.com/dkms/dkms-ols2004.pdf)
&nbsp;
<h2 id="head-b86b6eec08d5719cf1838929f26a64af88e2b7f0"><span style="color: #800080;">3. Building a kernel module rpm package (kmod)</span>
    
    
You can also create rpm files for kernel modules. The kernel module rpm package (kmod) may then be installed by using the rpm command just like any other package. However, rpm packaging of kernel modules is handled differently from the standard packaging process. The example, below, provides the basic kernel module rpm packaging technique.

Once again we will use the cifs module as an example and create a set of kmod-cifs rpm files.

1. Install **all** the <tt>kernel-devel</tt> packages for the kernel version that this package will be built against.

2. The patched source code is in the <tt>~/rpmbuild/BUILD/kernel-2.6.18/linux-2.6.18.i686/fs/cifs/</tt> directory as in Section 1, above. Make a working copy of that directory.

&nbsp;
<pre>[user@host]$ mkdir ~/cifs-1.45/
[user@host]$ cd ~/rpmbuild/BUILD/kernel-2.6.18/linux-2.6.18.i686/fs/cifs
[user@host]$ cp -a * ~/cifs-1.45/</pre>
3. Create a bzip2 compressed tarball containing the source directory.

&nbsp;
<pre>[user@host]$ cd
[user@host]$ tar -jcf cifs-1.45.tar.bz2 cifs-1.45/</pre>
4. Copy the compressed tarball to the SOURCES directory.

&nbsp;
<pre>[user@host]$ cp cifs-1.45.tar.bz2 ~/rpmbuild/SOURCES/</pre>
5. Make a copy the script <tt>kmodtool</tt> (part of the <tt>redhat-rpm-config</tt> package) in the SOURCES directory.

&nbsp;
<pre>[user@host]$ cd ~/rpmbuild/SOURCES/
[user@host SOURCES]$ cp /usr/lib/rpm/redhat/kmodtool kmodtool-cifs-el5.sh</pre>
6. Edit your copy of the <tt>kmodtool-cifs-el5.sh</tt> file to ensure that the line referencing <tt>kmod-common</tt> is commented out and to add a line that expands to become a reference to a <tt>kmod-cifs.conf</tt> file under the *%files* section.

&nbsp;
<pre>[user@host SOURCES]$ vi kmodtool-cifs-el5.sh</pre>
Line 105 onwards --

&nbsp;
<pre>#
# RHEL5 - Remove common package requirement on general kmod packages.
# Requires: ${kmod_name}-kmod-common &gt;= %{?epoch:%{epoch}:}%{version}
#</pre>
Line 168 onwards --

&nbsp;
<pre>echo "%files         -n kmod-${kmod_name}${dashvariant}"
if [ "" == "$kmp_override_filelist" ];
then
    echo "%defattr(644,root,root,755)"
    echo "/lib/modules/${verrel}${variant}/"
    echo "%config /etc/depmod.d/kmod-${kmod_name}.conf"
    #BZ252188 - I've commented this out for the moment since RHEL5 doesn't
    #           really support external firmware e.g. at install time. If
    #           you really want it, use an override filelist solution.
    #echo "/lib/firmware/"
else
    cat "$kmp_override_filelist"
fi</pre>
7. Create a <tt>cifs-kmod.spec</tt> file in the SPECS directory.

&nbsp;
<pre>[user@host SOURCES]$ cd ~/rpmbuild/SPECS/
[user@host SPECS]$ vi cifs-kmod.spec</pre>
&nbsp;
<pre># Define the kmod package name here.
%define kmod_name cifs

# If kversion isn't defined on the rpmbuild line, define it here.
%{!?kversion: %define kversion 2.6.18-8.el5}

Name:    %{kmod_name}-kmod
Version: 1.45
Release: 1%{?dist}
Group:   System Environment/Kernel
License: GPLv2
Summary: %{kmod_name} kernel module(s)
URL:     http://www.centos.org/

BuildRequires: redhat-rpm-config
BuildRoot:     %{_tmppath}/%{name}-%{version}-%{release}-build-%(%{__id_u} -n)
ExclusiveArch: i686 x86_64

# Sources.
Source0:  %{kmod_name}-%{version}.tar.bz2
Source10: kmodtool-%{kmod_name}-el5.sh

# Define the variants for each architecture.
%define basevar ""
%ifarch i686
%define paevar PAE
%endif
%ifarch i686 x86_64
%define xenvar xen
%endif

# If kvariants isn't defined on the rpmbuild line, build all variants for this architecture.
%{!?kvariants: %define kvariants %{?basevar} %{?xenvar} %{?paevar}}

# Magic hidden here.
%{expand:%(sh %{SOURCE10} rpmtemplate_kmp %{kmod_name} %{kversion} %{kvariants})}

# Disable the building of the debug package(s).
%define debug_package %{nil}

# Define the filter.
%define __find_requires sh %{_builddir}/%{buildsubdir}/filter-requires.sh

%description
This package provides the CentOS-5 bug-fixed %{kmod_name} kernel module (bug #1776).
It is built to depend upon the specific ABI provided by a range of releases
of the same variant of the Linux kernel and not on any one specific build.

%prep
%setup -q -c -T -a 0
for kvariant in %{kvariants} ; do
    %{__cp} -a %{kmod_name}-%{version} _kmod_build_$kvariant
done
echo "/usr/lib/rpm/redhat/find-requires | %{__sed} -e '/^ksym.*/d'" &gt; filter-requires.sh
echo "override %{kmod_name} * weak-updates/%{kmod_name}" &gt; kmod-%{kmod_name}.conf

%build
for kvariant in %{kvariants} ; do
    KSRC=%{_usrsrc}/kernels/%{kversion}${kvariant:+-$kvariant}-%{_target_cpu}
    %{__make} -C "${KSRC}" %{?_smp_mflags} modules M=$PWD/_kmod_build_$kvariant
done

%install
%{__rm} -rf %{buildroot}
export INSTALL_MOD_PATH=%{buildroot}
export INSTALL_MOD_DIR=extra/%{kmod_name}
for kvariant in %{kvariants} ; do
    KSRC=%{_usrsrc}/kernels/%{kversion}${kvariant:+-$kvariant}-%{_target_cpu}
    %{__make} -C "${KSRC}" modules_install M=$PWD/_kmod_build_$kvariant
done
%{__install} -d %{buildroot}%{_sysconfdir}/depmod.d/
%{__install} kmod-%{kmod_name}.conf %{buildroot}%{_sysconfdir}/depmod.d/
# Set the module(s) to be executable, so that they will be stripped when packaged.
find %{buildroot} -type f -name *.ko -exec %{__chmod} u+x {} ;

%clean
%{__rm} -rf %{buildroot}

%changelog
* Wed Jan 05 2011 Alan Bartlett &lt;ajb@elrepo.org&gt; - 1.45
- Revised this specification file.

* Fri May 18 2007 Akemi Yagi &lt;toracat@centos.org&gt; - 1.45
- Initial el5 build of the kmod package.</pre>
8. Build the package.

&nbsp;
<pre>[user@host SPECS]$ rpmbuild -bb --target=`uname -m` cifs-kmod.spec 2&gt; build-err.log | tee build-out.log</pre>
If you do not wish to build the kmod packages for your currently running kernel, you can specify the the kernel version on the command line. For example:

&nbsp;
<pre>[user@host SPECS]$ rpmbuild -bb --target=`uname -m` --define 'kversion 2.6.18-348.el5' cifs-kmod.spec 2&gt; build-err.log | tee build-out.log</pre>
will build the kmod packages for the 2.6.18-348.el5 kernel.

In a similar fashion, you may select which kernel-variant kmod package(s) to build. For example:

&nbsp;
<pre>[user@host SPECS]$ rpmbuild -bb --target=`uname -m` --define 'kvariants ""' cifs-kmod.spec 2&gt; build-err.log | tee build-out.log

will build just the base-kernel kmod package.

When the build completes, there will be a set of <tt>kmod-cifs</tt> rpm files in the <tt>~/rpmbuild/RPMS/</tt><tt>`uname -m`</tt><tt>/</tt> directory.

See also:


*   Kmod rpm building [http://www.kerneldrivers.org/RedHatKernelModulePackages](http://www.kerneldrivers.org/RedHatKernelModulePackages) and [http://fedoraproject.org/wiki/Obsolete/KernelModules](http://fedoraproject.org/wiki/Obsolete/KernelModules)
*   The white paper [http://driverupdateprogram.com/presentations/DriverUpdateProgramTechnical.pdf](http://driverupdateprogram.com/presentations/DriverUpdateProgramTechnical.pdf)
*   General rpm building [http://docs.fedoraproject.org/drafts/rpm-guide-en/ch08s02.html](http://docs.fedoraproject.org/drafts/rpm-guide-en/ch08s02.html)
