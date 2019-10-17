---
title: dietpi镜像有时无法使用pishrink脚本缩小的原因
date: 2019-10-17 22:04:08
tags: [树莓派, pishrink, dietpi]
---
记录以备查

一般使用Win32DiskImager在windows系统上备份树莓派的镜像，这种方式备份出来的镜像为sd卡容量的大小，若在16g卡上安装了一个4g不到的系统，备份出来不仅占空间，刷入时也更花时间，最尴尬的是都是16g的卡也有可能出现略微的容量差异，导致容量小一点的就没法刷入。

在看[Raspberrypi 3 系统备份还原， 基于最小系统镜像实现](https://blog.csdn.net/lb1885727/article/details/77574502)一文时，注意到以下这句话，才意识到dietpi镜像使用pishrink脚本缩小报ERROR: Image already shrunk to smallest size的原因。
![](/img/2019/boot_and_root.png)

经搜索，发现dietpi系统应该是只在第一次启动时会扩展分区，这被写成一个service而不是像raspberry系统中可以在raspi-config命令后找到，推测运行一次就被mask（注销unit）了。而在工作过程中，16g的卡，我可能用pishrink脚本将初始的dietpi镜像缩小成了4g的dietpi-shrink.img，再把dietpi-shrink.img写入到新的sd后，扩展分区的服务（dietpi-fs_partition_resize.service）并不会运行了，所以/root分区的大小可能就只有3.7g，若再把这个新sd卡上的镜像备份出来（16g），那就无法使用pishrink缩小了。

[DietPi-Drive_Manager | Expand file system button, in case of changed storage](https://github.com/MichaIng/DietPi/issues/1821)
以上链接给出了解决方法，不需要手动扩展，比较方便，只需把扩展分区服务unmask。

```
systemctl unmask dietpi-fs_partition_resize.service
systemctl enable dietpi-fs_partition_resize.service
reboot
```
重启后备份这个新sd卡上的镜像出来，就又可以使用pishrink缩小了。

