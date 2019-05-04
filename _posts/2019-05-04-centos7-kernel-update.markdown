---
layout: default
title: "centos7升级最新kernel"
date: 2019-05-04 18:40:00
categories: main
---

### 前言
活跃各类别内核版本见[官方](https://www.kernel.org/category/releases.html)。  
主要有以下几个类别：  
- **Prepatch**  
Prepatch或“RC”内核是主线内核预发行版，主要针对其他内核开发人员和Linux爱好者。它们必须从源代码编译，并且通常包含必须在可以放入稳定版本之前进行测试的新功能。准备内核由Linus Torvalds维护和发布。  
- **Mainline**  
主线树由Linus Torvalds维护。它是引入所有新功能的树，以及所有令人兴奋的新开发项目。新的主线内核每2-3个月发布一次。  
- **Stable**  
在每个主线内核发布后，它被认为是“稳定的”。任何针对稳定内核的错误修复都从主线树反向移植，并由指定的稳定内核维护者应用。在下一个主线内核可用之前，通常只有少数bugfix内核版本可用 - 除非它被指定为“长期维护内核”。根据需要发布稳定的内核更新，通常每周一次。
- **Longterm**  
通常有几个“长期维护”内核版本，用于向旧内核树的后端移植错误修复。只有重要的错误修正应用于此类内核，并且它们通常不会看到非常频繁的版本，特别是对于较旧的树。  

centos7系统可以使用`uname -r`查看其内核版本。
### 升级内核
**1.更新仓库**  
```
yum -y update
```
**2.启用ELRepo源**  
ELRepo仓库是基于社区的企业级Linux仓库，提供对RedHat Enterprise (RHEL)和其他基于 RHEL的Linux发行版（CentOS、Scientific、Fedora 等）的支持。 

启用ELRepo仓库：  
```
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
yum install https://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm
```
**3.查看elrepo仓库kernel**  
```
yum --disablerepo="*" --enablerepo="elrepo-kernel" list available
```
**4.安装最新内核**  
```
yum -y --disablerepo="*" --enablerepo="elrepo-kernel" install kernel-ml
```
**5.设置grub2**  
**5.1查看系统里所有可用内核**  
```
# awk -F\' '$1=="menuentry " {print i++ ":" $2}' /etc/grub2.cfg
0:CentOS Linux (5.0.10-1.el7.elrepo.x86_64) 7 (Core)
这里已经升级完了
```
**5.2 设置grub2**  
第一种方式，`grub2-set-default 0`  
```
grub2-set-default 0
```
第二种方式，编辑`/etc/default/grub`  
```
GRUB_TIMEOUT=5
GRUB_DISTRIBUTOR="$(sed 's, release .*$,,g' /etc/system-release)"
GRUB_DEFAULT=0 #默认为save
GRUB_DISABLE_SUBMENU=true
GRUB_TERMINAL_OUTPUT="console"
GRUB_CMDLINE_LINUX="net.ifnames=0 rhgb quiet"
GRUB_DISABLE_RECOVERY="true"
```
**5.3生成grub配置文件并重启**  
```
grub2-mkconfig -o /boot/grub2/grub.cfg
reboot
```
**5.4验证内核**  
```
# uname -r
5.0.10-1.el7.elrepo.x86_64
```
### 删除旧内核(可选)  
**1.查看系统内所有已安装内核**  
```
rpm -qa|grep kernel
```
**2.清除旧版内核**  
```
yum remove kernel-3.10.0-862.el7.x86_64 kernel-tools-3.10.0-957.10.1.el7.x86_64 kernel-3.10.0-957.10.1.el7.x86_64 kernel-tools-libs-3.10.0-957.10.1.el7.x86_64 kernel-headers-3.10.0-957.10.1.el7.x86_64
```

结语，旧内核移除时会清除依赖包gcc等，后续如果需要gcc，使用yum安装的时候还会安装原旧kernel-header等依赖包，并无大碍，关于gcc的升级，算了，我选择换系统😁
