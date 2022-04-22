# tunning packets
This repository holds compiled packages for different linux distributions. These compiled packages are used for the optimization of KubeOVN.



### How to use:

First check the distribution version of your system:

```bash
# for centOS
$ uname -r
3.10.0-1160.el7.x86_64
$ yum install kernel-devel --assumeno
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.nju.edu.cn
 * extras: mirrors.maine.edu
 * updates: mirrors.seas.harvard.edu
Package kernel-devel-3.10.0-1160.59.1.el7.x86_64 already installed and latest version
Nothing to do
# for ubuntu
$ uname -r
5.11.0-43-generic
$ apt install linux-headers-generic --assume-no
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following additional packages will be installed:
  linux-headers-5.4.0-109 linux-headers-5.4.0-109-generic
The following NEW packages will be installed:
  linux-headers-5.4.0-109 linux-headers-5.4.0-109-generic linux-headers-generic
0 upgraded, 3 newly installed, 0 to remove and 175 not upgraded.
Need to get 12.4 MB of archives.
After this operation, 86.3 MB of additional disk space will be used.
Do you want to continue? [Y/n] N
Abort.
```

Use the result of `uname -r` as a standard VERSION, e.g. `5.11.0-43-generic` in the example above. 

But mark the more precise version number as VERSION, e.g. `3.10.0-1160.59.1.el7.x86_64`. 

The following optimisation options are available now:

1. ##### module Fastpath

   Choose the right version for your distribution  and download the package.

   ```bash
   # for centos
   $ wget https://github.com/lut777/tunning-packets/raw/master/rhel/centos7/${VERSION}/kube_ovn_fastpath.ko
   ```

   Then insert module into system:

   ```bash
   $ insmod ./kube_ovn_fastpath.ko
   ```

   Use `dmesg` to verify the module:

   ```bash
   [619631.323788] init_module,kube_ovn_fastpath_local_out
   [619631.323798] init_module,kube_ovn_fastpath_post_routing
   [619631.323800] init_module,kube_ovn_fastpath_pre_routing
   [619631.323801] init_module,kube_ovn_fastpath_local_in
   ```

   If the log exits then the module is installed.

2. ##### module stt

   Also choose the right version for your distribution  and download the package.

   ```bash
   $ wget https://github.com/lut777/tunning-packets/raw/master/rhel/centos7/${VERSION}/openvswitch-kmod-2.17.2-1.el7.x86_64.rpm
   ```

   Then install the RPM package:

   ```bash
   $ rpm -i openvswitch-kmod-2.17.2-1.el7.x86_64.rpm
   ```

   Verify the module:

   ```bash
   $ modprobe -c | grep vport | grep stt
   alias xxxxxxxx. vport_stt
   ```

   If the installation is smooth and verification passes, then the module is installed.



### How to submit requirement:

​	Limited distributions are supported currently, as there are too many sub-versions.

​	So we have switched to a new form: 

​	We recommend that users propose which versions they would like to support via PR. The steps are as follows:

 1. add the distribution version and download url of the corresponding linux-header package to the file `kernel.list` in the corresponding distribution as a PR.

    e.g. add a new version `3.10.0-1160.49.1.el7.x86_64` in `tunning-packets/rhel/centos7/kernel.list`:

    ```bash
    3.10.0-1160.62.1.el7.x86_64 http://ftp.riken.jp/Linux/cern/centos/7/updates/x86_64/Packages/kernel-devel-3.10.0-1160.62.1.el7.x86_64.rpm
    3.10.0-1160.59.1.el7.x86_64 http://ftp.riken.jp/Linux/cern/centos/7/updates/x86_64/Packages/kernel-devel-3.10.0-1160.59.1.el7.x86_64.rpm
    3.10.0-1160.53.1.el7.x86_64 http://ftp.riken.jp/Linux/cern/centos/7/updates/x86_64/Packages/kernel-devel-3.10.0-1160.53.1.el7.x86_64.rpm
    # the new added line
    3.10.0-1160.49.1.el7.x86_64 http://ftp.riken.jp/Linux/cern/centos/7/updates/x86_64/Packages/kernel-devel-3.10.0-1160.49.1.el7.x86_64.rpm
    ```

    Note that the versions are added in descending order.

 2. submit the PR
