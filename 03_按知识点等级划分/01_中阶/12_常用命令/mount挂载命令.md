---
title: mount挂载命令
description: 
published: true
date: 2022-10-26T07:00:13.475Z
tags: 
editor: markdown
dateCreated: 2022-04-21T03:48:39.601Z
---

## 简介

Linux操作系统将系统中的一切都作为文件来管理，例如硬件设备、磁盘分区等，我们需要将它们挂载到相关的目录下，才能对它们进行对应的操作。

在Linux下需要打开一个文件，那么必须挂载该文件所在的目录到包含该文件的文件系统必须先进行挂载的动作，此时使用者要对该文件系统下mount的指令以进行挂载。通常是使用在USB或其他可卸载存储设备上，而根目录则保持挂载的状态。但Unix文件系统可以对应一个文件而不一定要是硬件设备。

## 挂载点

挂载点我们以磁盘为例，每块硬盘都分为若干个分区，每个分区都有自己的文件系统。GNU/Linux 使用唯一的树形结构来管理文件，而每个文件系统都挂载于树形结构的某个位置。

正如 Windows 需要有 C: 驱动器一样，GNU/Linux 必须能够将根文件系统挂载于文件树的根(/)上。当根挂载完成之后，您就可以将其它文件系统挂载于树形结构各种挂载点上。根结构下的任何目录都可以作为挂载点，而您也可以将同一文件系统同时挂载于不同的挂载点上。

挂载点实际上就是linux中的磁盘文件系统或者设备的入口目录。

### FHS

Filesystem Hierarchy Standard 是在 Linux 或其他 UNIX® 类系统上指定目录公共布局的一个文档。通过将文件跨 Linux 发行版放置在同一个地方，FHS 简化了独立于发行版的软件开发。FHS 在 Linux Standard Base 中也有用到。FHS 允许用户和软件预测所安装文件和目录的位置。一个遵从 FHS 的文件系统假定操作系统支持大多数 UNIX 文件系统中找到的基本安全特性。

FHS 的核心是文件的两个独立特征： 可共享 vs. 不可共享

可共享文件可以位于一个系统上，而在另一个系统上使用。

不可共享文件必须位于要使用的同一个系统上。

静态 vs 变量

静态文件仅通过系统管理员进行变更，比如安装或更新一个包，且包含文档、库和二进制文件。

变量文件是所有其他文件，比如日志、打印后台文件、数据库和用户数据，这些文件都可由用户和系统进程进行更改。

这些特征可使带不同特征集的文件存储在不同的文件系统上。表 1 是一个 FHS 文档示例，显示遵循 FHS 的一个布局。

根文件系统中的 FHS 目录

FHS 目标是要尽量使根文件系统足够小。不过，它必须包含启动、还原、恢复或修复系统所需的所有文件，包括一个有经验的管理员执行这些任务所需的实用工具。注意，启动一个系统需要在根文件系统上有足够的空间，从而可以安装其他文件系统。

下表显示了 FHS 在根（或 /）文件系统下需要的目录的用途。目录或符号链接都是必需的，除了标记为可选的那些之外，这些可选项仅在相应的子系统存在时才需要。

### 详解

挂载点解释

 /  根目录，唯一必须挂载的目录。/是衍生以下挂载点的根源

/bin 主要存放用户经常使用的命令。静态且不可共享。

/boot 主要存放着系统的内核以及启动时所需要的文件，比如Linux内核文件vmlinuz和核心解压缩所需的RAM Disk文件initrd都 在这里，如果安装了grub，这里还会有Grub目录。静态且不可共享。

/dev 存放设备文件，比如/dev/sda代表第一块IDE硬盘。正常情况下，每种设备有一个独立的子目录，其中存放这些设备的内容。静态且不可共享。

/etc 主要存放系统管理所需的配置文件和子目录。静态且不可共享。

/home 用户的个人数据存放在主目录中，比如有个用户ztg，它的主目录就是/home/ztg。变化的且可共享。（可选）

/lib 主要存放系统最基本的函数库，几乎所有的应用程序都要用到这些函数库。它也在其 /lib/modules/内核_版本号 子目录中存放内核模块。静态且不可共享。（可选）

/lib32 专门存放系统的32位函数库

/lib64 专门存放系统的64位函数库

/lost found 平时是空的，当系统不正常关机后，这里保存一些文件的片段

/media 用途同mnt，比如挂载u盘等

/mnt 可以将别的文件系统临时挂载到这里，比如挂载windows硬盘分区。变化的且不可共享。

/opt 包含对系统操作并非基本的软件包。一般是用户安装的应用程序。

/proc 是一个虚拟的目录，由系统运行时产生，是系统内存的映射，可以通过直接访问这个目录来获取系统信息。注意：这个目录的内容不在硬盘上而是在内存里

/root 超级用户(也叫系统管理员或root用户)的主目录。变化的且不可共享。（可选）

/sbin 主要存放系统管理员使用的管理程序，其他的还有有/usr/sbin、/nsf/local/sbin。静态且不可共享。

/selinux 使用selinux的Linux，其安全级别可以达到B1级

/srv 存放一些服务启动之后需要服务的文件

/sys 系统的核心文件，这个目录是2．6内核的一个很大的变化，该目录下安装了2．6内核中新出现的一个系统文件Sysfs，Sysfs文件系统集成了下面三种文件系统信息：针对进程信息的proc文件系统、针对设备的devfs文件系统、针对伪终端的derpts文件系统。

/tmp 存放临时文件，需要经常清理，这是除了/usr/local目录以外一般用户可以使用的—个目录，启动时系统并不自动删除这里的文件，所以需要经常清理这里的无用文件。变化的且不可共享。

/usr 包含系统的主要程序、用户自行安装的程序、图形界面需要的文件、共享的目录与文件、命令程序文件、程序库，手册和其他文件等，这些文件—般不需要修改。静态且可共享。

/var 含系统执行过程中的经常变化的文件．例如打印机、邮件、新闻等假脱机目录、日志文件、格式化后的手册页以及一些应用程序的数据文件等。建议单独的放在一个分区。其中的各个子目录可以是可共享的，也可以是不可共享的。

Swap（交换分区） 类似于Windows的虚拟内存，就是当内存不足的时候，把一部分硬盘空间虚拟成内存使用，从而解决内存容量不足的情况。

### 理解技巧

把Linux系统的挂载点想象成一棵大树，/为大树的根源，/衍生的其他挂载点为这颗大树的树枝，挂载点下的文件为树叶。

## 挂载文件系统
### 图形管理

对于普通用户,一般来说都只是挂载和反挂载硬盘上的win分区,只需要打开文件管理器,在文件管理器左侧的树状图找到win分区,点击一下即可挂载.
反挂载只需要点击挂载分区时候参数的小三角符号即可。

### 命令管理

Linux没有驱动器盘符来指代文件系统，所有的文件系统是挂载在目录下面的。

安装 Linux 基本系统的位置在 /，其他所有的都位于 / 的子目录。/mnt/hd 是临时挂载分区的的常见地方，所以我们在例子中会使用它。

mount

mount可将指定设备中指定的文件系统加载到Linux目录下（也就是挂载点）。可将经常使用的设备写入文件/etc/fstab,以使系统在每次启动时自动加载。
mount加载设备的信息记录在/etc/mtab文件中。使用umount命令卸载设备时，记录将被清除。

        语法：mount [-afFhnrvVw] [-L] [-o] [-t] [设备名] [加载点] 
        
        参数和选项： 
        -a 加载文件/etc/fstab中设置的所有设备。 
        
        -f 不实际加载设备。可与-v等参数同时使用以查看mount的执行过程。 
        
        -F 需与-a参数同时使用。所有在/etc/fstab中设置的设备会被同时加载，可加快执行速度。 
        
        -h 显示在线帮助信息。 
        
        -L 加载文件系统标签为的设备。 
        
        -n 不将加载信息记录在/etc/mtab文件中。 
        
        -o 指定加载文件系统时的选项。有些选项也可在/etc/fstab中使用。
        
        这些选项包括：

        async 以非同步的方式执行文件系统的输入输出动作。 
        atime 每次存取都更新inode的存取时间，默认设置，取消选项为noatime。 
        auto 必须在/etc/fstab文件中指定此选项。执行-a参数时，会加载设置为auto的设备，取消选取为noauto。 
        defaults 使用默认的选项。默认选项为rw、suid、dev、exec、anto nouser与async。 
        dev 可读文件系统上的字符或块设备，取消选项为nodev。 
        exec 可执行二进制文件，取消选项为noexec。 
        noatime 每次存取时不更新inode的存取时间。 
        noauto 无法使用-a参数来加载。 
        nodev 不读文件系统上的字符或块设备。 
        noexec 无法执行二进制文件。 
        nosuid 关闭set-user-identifier(设置用户ID)与set-group-identifer(设置组ID)设置位。 
        nouser 使一位用户无法执行加载操作，默认设置。 
        remount 重新加载设备。通常用于改变设备的设置状态。 
        ro 以只读模式加载。 
        rw 以可读写模式加载。 
        suid 启动set-user-identifier(设置用户ID)与set-group-identifer(设置组ID)设置位，取消选项为nosuid。 
        sync 以同步方式执行文件系统的输入输出动作。 
        user 可以让一般用户加载设备。 
        
        -r 以只读方式加载设备。 
        -t 指定设备的文件系统类型。
        
        常用的选项说明有： 

        minix Linux最早使用的文件系统。 
        ext2 Linux目前的常用文件系统。 
        msdos MS-DOS 的 FAT。 
        vfat Win85/98 的 VFAT。 
        nfs 网络文件系统。 
        iso9660 CD-ROM光盘的标准文件系统。 
        ntfs Windows NT的文件系统。 
        hpfs OS/2文件系统。Windows NT 3.51之前版本的文件系统。 
        auto 自动检测文件系统。 
        
        -v 执行时显示详细的信息。 
        
        -V 显示版本信息。 
        
        -w 以可读写模式加载设备，默认设置。

实例:

1.挂载Linux能处理Lnux权限的文件系统

我们在第一个串口设备上的第三个分区有一个 ext3 文件系统，我们决定将其内容挂载到 /mnt/hd 目录下。

我们将其挂载为只读，所以对该分区上的内容不能作出更改。

为了挂载文件系统的内容，我们必须首先告诉 mount 文件系统的类型、挂载点及特殊选项。终端执行:

         mount -t ext3 /dev/hda3 /mnt/hd -o ro

分解命令: [-t ext3] 参数告诉 mount 我们使用的文件系统类型，在这里为 ext3。

        /mnt/hd 告诉 mount 将文件系统的内容放在哪里。
        [-o] 参数来声明我们需要使用的选项

如果这是你第一次安装 Linux，你需要特别考虑的只有 [ro] 和 [rw]。

2.挂载不能处理传统 Linux 权限的如 vfat 或 NTFS 文件系统时。终端执行:

         mount -t vfat /dev/hda4 /mnt/hd -o uid=alan

在这样的情况下，你需要使用 [uid] 和 [gid] 选项来允许非 root 用户读取这些文件系统。

umount

umount可卸除目前挂在Linux目录中的文件系统。

        语法：umount[-ahnrvV][-t<文件系统类型>][文件系统]
        
        参数：
        -a卸除/etc/mtab中记录的所有文件系统。
        -h显示帮助。
        -n卸除时不要将信息存入/etc/mtab文件中。
        -r若无法成功卸除，则尝试以只读的方式重新挂入文件系统。
        -t<文件系统类型>仅卸除选项中所指定的文件系统。
        -v执行时显示详细的信息。
        -V显示版本信息。
         [文件系统] 除了直接指定文件系统外，也可以用设备名称或挂入点来表示文件系统。

实例:

1.卸载的分区时，可直接用设备名或挂载点卸载文件系统，如：

         umount /dev/sdb1
        umount /media/disk
        fuser

如果分区正在被使用，umount会报错：

         umount: /dev/sdb1: device is busy

此时可使用lsof命令打开正在使用该文件系统的进程，如：

         lsof /dev/sdb1

再按需操作相关进程即可。

fuser与lsof类似，另外提供向相关进程发送信号等功能。基本用法：

获取正在访问某文件的进程和访问方式：

         fuser /media/disk

更详细的信息：

         fuser -l /media/disk

向正访问 /media/disk 的进程发送 SIGKILL 信号：

         fuser -k /media/disk

注意： fuser与lsof不同的是，fuser只支持寻找正在访问某文件夹的进程，而不能得到正在访问某设备的进程。

lazy umount

lazy umount正是针对上面错误中的busy而提出的，即可以卸载“busy”的文件系统。

举个例子，假设/dev/sdb1被mount到/media/disk，且处于“busy”状态，那么可以执行：

         umount -l /media/disk

请注意，该方法并不是完全安全的，lazy umount主要完成如下操作：

1.立即从目录结构中实现卸载，即新进程将无法通过/media/disk访问/dev/sdb1 正在访问该文件系统的程序不受影响。即正在操作/media/disk的进程不会被打断，且仍可以读写/dev/sdb1中的所有文件。

2.如果所有进程对/media/disk的操作都执行完，那么才真正地umount 由此可知，lazy umount并没有真正实现umount，仅用于特殊需要的情况，而用这种方法来卸载U盘是不安全的。

/etc/fstab

/etc/fstab 文件被一些程序用来确定哪些文件系统默认要加载，哪些需要检查，顺序是怎样的。

fstab看起来像是一个包含要挂载的设备、文件系统类型及可选参数的表格。

       $ cat /etc/fstab
        ## 文件系统 挂载点 类型 选项 转储 fsck
        /dev/hda1 / reiserfs defaults 1 1
        /dev/hda2 /home reiserfs defaults 1 2
        /dev/hda3 swap swap defaults 0 0
        /dev/cdrom /mnt/cdrom auto noauto,owner,ro,users 0 0
        /dev/fd0 /mnt/floppy auto noauto,owner 0 0
        devpts /dev/pts devpts gid=5,mode=620 0 0
        proc /proc proc defaults 0 0

如果 fstab 下面有对于你的文件系统的条目，你只需要告诉 mount 该设备的节点及挂载点。

        mount /dev/cdrom
        mount /home

## 挂载点管理

降低SWAP使用率

如果你的机器有2G或者更大的内存，同时又分有一个 SWAP 分区（交换分区），那么这个 SWAP 分区往往会拖慢你的电脑(内存读取速度远高于硬盘)。此时我们可以降低SWAP的使用率以提高速度。

对内存为1G和1G SWAP的机器来说，一般最好将 swappiness 设为5，而 深度操作系统默认是60。

修改方法

手工修改

终端输入：

         sudo gedit /etc/sysctl.conf 

你应该可以看到这样一行：

         vm.swappiness = 60

如果内存大于1GB，则可以将这个数值改为10或5。

提示：如果没有找到这一行，也可以自己添加。

## 常见问题

### 为什么不默认开机挂载Win分区

深度操作系统默认开机不默认挂载Win分区,但是很多用户是双系统用户(Linux+Windows).所以很多用户问为什么不默认开机挂载Win分区.原因如下:

Windows8默认开启快速启动功能,导致关闭实质是休眠,在Windows8启动的时候会抹去Linux下对Win分区数据的写入,会造成数据丢失.

如果默认开机挂载Win分区,但是Win分区出现了错误,会导致开机直接卡在挂载Win分区的步骤,因此不默认开机挂载Win分区.

如果Win分区损坏，会影响系统的正常启动

win分区有数据加密或者同步一类的软件保护，也会影响系统的正常启动

如果你确保你的系统自动挂载Windows分区不会影响系统启动,并且需要自动挂载win分区,请往下看.

注意:如果需要Linux系统默认启动挂载Windows系统分区,请一定要关闭Windows8系统的快速启动功能,并且确定win分区自动挂载并不会影响系统启动!!

方法一

打开默认搭载的" 磁盘" 软件,选中想要开机挂载的分区，点击下方的“齿轮”，选择“编辑挂载选项”,如下图

先将“自动挂载选项”的开关关闭，然后勾选“启动时挂载”和“显示用户界面”（可选项），点击确定保存设置。

注释:如果挂载的分区名字不好区分，可以在”显示名称“处给分区添加一个名称。

方法二

Linux系统原生支持Windows分区的文件系统,但是系统启动默认不挂载Windows下的分区,我们可以通过安装NTFS写入支持配置程序设置Linux系统启动自动挂载windows系统分区.

安装

终端执行：

         sudo apt-get install ntfs-config

配置

想要自动挂载 Windows 分区，请点击：开始 --- 系统管理 --- NTFS 写入支持配置程序，即可配置：

NTFS 写入支持配置程序

### 不能挂载特定的win分区

如果发现不能挂载特定的win分区,请检查以下可能存在的原因:

不能挂载windows8下的分区,请检查是否关闭windows8的快速启动,并且完全将windows8重启一次

不能挂载windows下的分区,请检查该分区下是否有数据保护和网盘等软件.

如果依然无法挂载,可能硬盘分区表存在问题.

#### 为什么要自定义多个分区？

分多个区有以下几个目的：

在不损失数据的情况下重装系统，比如独立设置/home挂载点,重装系统的时候直接标记回/home。

针对不同挂载点的特性分配合适的文件系统以合理发挥性能，比如对/var使用reiserfs，对/home使用xfs，对/使用ext4。

针对不同的挂载点开启不同的挂载选项，如是否需要即时同步，是否开启日志，是否启用压缩。

### Linux系统盘分区太小，不够用，问是不是要重装系统？

比如将用户目录 /home 挂载到单独的分区:

1. 首先打开Gpart分区编辑器，找一个空闲的分区，调整好分区大小，格式化城ext4格式。

2. 记录下分区的路径，比如 /dev/sda2 ,记录下分区的 UUID。

3. 在 /mnt 目录下新建一个 home,并将我们的 /dev/sda2 挂载上去。

```
cd  /mnt
sudo mkdir  home
sudo mount  /dev/sda2  /mnt/home
```

4.将现在的home目录下面的所有文件拷贝到 /mnt/home 中

```
cd /home   
sudo cp -r  *   /mnt/home
```

5. 查看/mnt/home 目录的权限和拥有者，用户组是否跟 /home 一样

```
 ls -l  /home
ls -l  /mnt/home
```

如果不一样，使用chown命令更改 /mnt/home 所有者和使用chmod命令更改 /mnt/home 权限到与/home一致

然后卸载分区

```sudo  umount  /dev/sda2```

6. 修改系统启动时的分区挂载设置文件

```sudo vim  /etc/fstab```

添加挂载 /dev/sda2 为 /home 的配置

```UUID= 上面/dev/sda2的UUID   /home   ext4  defaults  0  1```

恩，保存

7. 重启电脑，查看挂载点 /home 的设备路径是否是 /dev/sda2

```df```

如果输出内中有如下所示的行：

```
文件系统           1K-块      已用      可用     已用% 挂载点
/dev/sda2       61691088   7026960  51500616   13% /home
```

恭喜，你已经成功的把 /home 挂载到一个单独的分区了。

8. 其实还有许多其他的目录都可以挂载到单独的分区，目前我就把 /opt, /home 挂载成为单独分区的。挂载到单独分区还有一个好处是重装系统时可以保存资料和配置，这就要自己酌情处理了。

## 相关链接
[ubuntu中文论坛：细说linux挂载——mount，及其他……](http://forum.ubuntu.org.cn/viewtopic.php?f=120&t=257333)

[ubuntu Ubuntusoft：linux 挂载详解](http://www.ubuntusoft.com/ubuntu-linux-mount-xiangjie.html)

[Unix目录结构的来历](http://www.ruanyifeng.com/blog/2012/02/a_history_of_unix_directory_structure.html)

[Linux吧-分区(六楼)](http://tieba.baidu.com/p/2218502310)

[FHS文档](http://www.pathname.com/fhs/)

[学习 Linux，101: 查找并放置文件系统](http://www.ibm.com/developerworks/cn/linux/l-lpic1-v3-104-7/#resources)

[Linux Deepin 论坛:图形化操作，实现开机自动挂载NTFS分区](http://www.linuxdeepin.com/forum/6/15161)

[Umount](http://linux-wiki.cn/wiki/zh-hans/Umount)

[将linux系统目录挂载到其他分区，扩大系统可用空间](http://blog.csdn.net/naivor/article/details/68059961)
