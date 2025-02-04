---
title: ext4-linux分区修复
description: 
published: true
date: 2022-10-25T02:11:10.816Z
tags: ext4 linux分区
editor: markdown
dateCreated: 2022-06-28T08:20:41.036Z
---

# ext4 linux 分区修复

## 前言

想调整分区，然后下载了一个 live cd <https://www.livecd.com/utilities.html>， 这是一个 kde 桌面的 linux 系统，然后我使用 kde 分区管理器（ partitionmanager )。

首先，我将 /home 分区腾出 100g， 然后扩大 / 分区， 然后新建一个 交换分区，和一个普通分区，结果执行得非常慢，等了10分钟进行到 1%，所以我取消了，但这时候卡住不动，等了30分钟，强制关闭。

然后就出现如下情况：

1. / 正常
2. 100 G 空间
3. /dev/nvme0n1p5 剩余空间（无法识别）

这就是本文的案例。

## 恢复文件

第一时间，我就想着用 windows 上著名的磁盘工具 diskgenius 来恢复，但是， dg 也无法识别磁盘，只是粗略的判断这是一个 linux 分区。我只能点击恢复文件，但是运行10分钟后，我发觉 dg 是按照 windows 分区的逻辑来恢复文件（实际是在搜索数据，匹配文件类型），这种恢复可能对于一些特别重要的文件的恢复有一点意义，对我想要的全面恢复正常，是没有多大意义的。

因为文件都是零散的，几乎没有目录结构，甚至名字都没有。这一堆文件给我恢复了，又有什么意思？而且看上去并没有恢复多少。

所以这个方案我否决了。

## 修复 ext4 文件系统

我粗略的知道 ext4 是有一定健壮性的，讲道理它的文件系统被破坏，但实际数据应该没有被破坏多少，只要能重建文件系统，大部分都是能够用的。

我判断这个该死的 kde 分区工具，它只是运行了1%的样子，就算损毁也就1%的数据遭到破坏。

所以我就找到了ext 的修复文档，非常感谢作者： 麻辣糖果╭(╯ε╰)╮ ，知识挽救数据！

当然，你们可以参考文档来修复。参考链接放在文章后面。

过程：

1. `dumpe2f2 /dev/nvme0n1p4`  : 这个命令可以显示 ext4 分区的修复块的信息
2. `tune2fs -h /dev/nvme0n1p4` : 这个看分区的一些参数

以上两个我测试了一个 p4，就是我的根分区，是可以显示信息的，但是这些信息我看不懂。

1. `mkfs.ext4 -n /dev/nvme0n1p5` : 这个可以看修复块的位置。

这个位置实际上都是固定的： 32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632....

其中每一个块都能够完整的恢复文件系统。我们只需要使用其中一个就可以了。

另外要注意的是块大小： Creating filesystem with 2621440 4k blocks and 655360 inodes  。 看到类似的信息，就是 4k 大小的块。

4. `mount -t ext4 -o sb=131072 /dev/nvme0n1p5 /mnt` : 这个命令就是使用位于 131072 位置上的备份块来挂载文件系统到 /mnt，如果能挂载就说明该备份块的数据没有损毁，否则换一个位置。

这个 131072 从哪里来？ sb 是按 1k 为单位，备份块之前的信息是 4k，所以备份块的编号 * 4 就是 sb 的参数。 32768 * 4 = 131072 。

顺利的话，你已经快完成了修复。 但是我的案例不能直接这样做。首先我是把 p5 压缩了，所以p5 的位置，它偏移了 100G，所以我试了几个数字都不行。

如何才能恢复到原来的分区位置呢？很简单，把 p5 删了，然后空间就和100g合并在一起，然后将所有新建一个新的 p5.

这里需要普及一个概念，那就是删掉分区，并不等于将数据毁掉！这和删除文件不同（其实删除文件也不会把数据毁掉，只是让你丢失和数据的联系而已）。

分区表就是让文件系统知道自己可以利用 哪一块区域而已，分区改变了，也就是它告知文件系统的区域位置错了，那么文件系统按区域对齐后，数据自然是凌乱不堪，是无效的。但是只要我们给出正确的区域参数，文件系统就能匹配回去了。

原理就是这样。

```bash
# 对磁盘重新分区，删掉错误的p5， 
# 和100G 合并，所有空间创建 p5
# 以上操作等于恢复没有调整大小前 p5 区域
cfdisk /dev/nvme0n1 

# 再次运行就 顺利挂载到 /mnt ,分区顺利挂载
mount -t ext4 -o sb=131072 /dev/nvme0n1p5 /mnt

ls /mnt # 大概查看一下数据，是正常的

# 正式修复
# 会提示你输入 y 或者 all 来确认，输入即可
fsck.ext4 -b 32768 /dev/nvme0n1p5

# 以下命令在迫于无奈的时候强行修复
# 慎用 (如果我用了估计就完蛋了)
# 重建文件系统
mkfs.ext4 -S /dev/nvme0n1p5
mkfs.ext4 -y /dev/nvme0n1p5
```

看上去挺简单的，但是没有知识，就无法恢复。

分区会改变 PARTUUID ，导致 /etc/fstab 挂载不上 /home，修复一下即可。

再次感谢作者：麻辣糖果！

## 参考

1. Ext4文件系统修复: <https://www.cnblogs.com/xcbki/p/11288149.html>