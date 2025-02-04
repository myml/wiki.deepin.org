---
title: KVM详解（五）——KVM虚拟机镜像格式
description: 
published: true
date: 2022-11-24T12:44:10.905Z
tags: 
editor: markdown
dateCreated: 2022-11-24T12:38:08.010Z
---

# KVM详解（五）——KVM虚拟机镜像格式
## 一、虚拟机常用镜像格式介绍
目前，虚拟机的主流镜像格式有raw、cow、qcow、qcow2以及vmdk，下面，我就详细介绍一下这些主流的虚拟机镜像格式。

（一）raw格式
raw格式是一种很早的镜像格式，格式比较原始、简单，性能上也还不错。并且raw格式镜像的一个突出好处是它支持转换成其他格式的镜像，或者作为其他格式镜像转换的中间格式。但是，raw格式的一个突出缺点就是不支持快照。 CentOS6的虚拟机KVM和XEN默认使用raw格式。

（二）cow、qcow和qcow2格式
cow、qcow和qcow2是另外的镜像格式，cow格式、qcow格式目前已经逐步被qcow2格式所取代，qcow2格式是目前的一种主流镜像格式，性能上与raw格式相差无几，但是支持虚拟机快照。CentOS7的KVM虚拟机镜像默认使用qcow2格式。

（三）vmdk格式
vmdk是Vmware虚拟的镜像格式，其整体性能非常出色，稳定性和其他方面的能力很好，也支持快照。

## 二、KVM虚拟机镜像格式转换
下面，我来介绍一下KVM的虚拟机镜像格式转换方法。

（一）qemu命令修改虚拟机镜像格式
qemu-img命令可以被用来查看虚拟机镜像格式，并对虚拟机镜像格式进行转换。
qemu-img命令查看虚拟机镜像命令为：

`qemu-img info 【虚拟机镜像】`

执行命令：

`qemu-img info centos7-2.img`

可以查看centos7-2.img的镜像格式，结果如下所示：

![2022-11-24_53702.png](/2022-11-24_53702.png)

可以看出，该镜像的格式为qcow2。
qemu-img进行虚拟机镜像格式转换命令为：

`qemu-img convert -f 【原镜像格式】 -O 【转换后】 【源虚拟机镜像】 【转换后虚拟机镜像】`

该命令执行示例如下：

`qemu-img convert -f qcow2 -O raw centos7-2.img /var/lib/libvirt/images/centos7-2.img /var/lib/libvirt/images/centos7-2.raw`

该命令用于将centos7-2的qcow2格式转换成raw格式，该命令执行结果如下：

![2022-11-24_74675.png](/2022-11-24_74675.png)

可以看出，该命令执行成功，centos7-2虚拟机镜像格式已经成功转变。

（二）修改虚拟机镜像启动文件
在完成虚拟机镜像格式转换后，我们还需要修改我们的虚拟机配置文件，使得在虚拟机启动时，按照转换后的raw格式进行启动。
执行命令：

virsh edit centos7-2

可以打开一个类似vi命令的编辑器，打开centos7-2的配置文件，我们需要修改与启动镜像格式和启动镜像文件有关的设置，如下方红线处：

![2022-11-24_53089.png](/2022-11-24_53089.png)

我们将其修改为我们转换后的镜像文件和镜像格式，修改完成后的配置文件如下所示：

![2022-11-24_85226.png](/2022-11-24_85226.png)

这样，当我们再启动虚拟机时，就可以以新的镜像格式启动了。







