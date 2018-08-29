---
title: 一次跳闸引起的ubuntu命令学习记录
date: 2017-09-01 15:41:01
tags: [ubuntu, linux]
---

# ubuntu命令行学习记录
公司有个ubuntu12.04系统的samba文件服务器。一次意外跳闸后，就无法进入系统了。文件检测卡在checking battery之类，可以ctrl+alt+F1然后登陆。

经多次推测排查，认为是意外断电导致的文件系统丢失等问题。
/usr/lib/x86_64-linux-gnu/目录下的共享动态链接库等都消失了。即使dpkg -i libapt-pkg.deb后也没用。

定位某个库文件

```
locate libapt-pkg.so.5.0
```

```
sudo fdisk -l;  # 查看硬盘和分区情况
```
Disk /dev/sda doesn't contain a valid partition table这种提示是正常的！！如果没分区直接挂载使用当然没有分区表！！

```
sudo df -h; # 查看挂载的设备的大小
```

```
sudo du -h --max-depth=1 /fileDirectory; # 查看单个目录的大小，最后一项会显示为./大小。
```

```
sudo mount /dev/sdb /mnt;
ls /mnt; # 为/dev/sdb/下内容
sudo umount /mnt;
ls /mnt; # 为空
```

## 用ultralISO制作U盘启动盘
用U盘启动盘try ubuntu，查看其他硬盘状态；
必须先取消挂载

```
sudo umount /media/uuid/;
```

```
sudo fsck /dev/sdb/;
```

ubuntu分区问题
/：          4g，系统所在分区，boot可以不单独分区直接放/下
/home：      其他所有
swap区：      2G，一般按物理内存大小分配？

##设置静态ip

```
sudo vim /etc/network/interfaces
```

## 关于防火墙配置
一个相对iptables简单很多的防火墙配置工具：ufw，默认不启用。也就是说，ubuntu的端口默认都是开放的。
启动ufw：

```
$ sudo ufw default deny
$ sudo ufw enable
```

打开ssh服务的22端口：

```
$ sudo ufw allow 22
```
由于在/etc/services中， 22端口对应的服务名是ssh。所以下面的命令是一样的：

```
$ sudo ufw allow ssh
```
禁用22端口：

```
$ sudo ufw delete allow 22
```
查看防火墙状态：

```
$ sudo ufw status
```
关闭防火墙：

```
$ sudu ufw disable
```
更多用法可以通过以下命令查看

```
$ man ufw
```

## 关于开启ssh登陆
新安装的ubuntu系统，默认是不支持ssh登录的。
SSH分客户端openssh-client和openssh-server
如果只是想登陆别的机器的SSH只需要安装openssh-client（ubuntu有默认安装，如果没有则sudoapt-get install openssh-client），如果要使本机开放SSH服务就需要安装openssh-server。
这里根据需求只安装了服务端。

```
$ sudo apt-get update
$ sudo apt-get install openssh-server
```
开启和停止ssh服务端：

```
#启动
$ /etc/init.d/ssh start

#停止
$ /etc/init.d/ssh stop

#重新启动
$ /etc/init.d/ssh restart

#利用service命令
#启动
$ sudo service ssh start

#停止
$ sudo service ssh stop

#重新启动
$ sudo service ssh restart
```
查看ssh是否在运行：

```
#如果没有任何显示，是没有运行ssh服务的。反之，出现sshd字样，表示ssh服务已运行。
$ sudo ps -e |grep ssh
```
ssh配置文件地址：`/etc/ssh/sshd_config`

到这里就不得不提一下安全性。。因为我有个鲨鱼的小机只用来搭$$居然也有1000+次尝试登录失败的提醒。
（1）改ssh的默认端口为其他如2333
改之前先将2333端口打开，配置文件中的port从22改为2333，然后service ssh restart重启服务，命令行指定端口号为
```
$ ssh -p 2333 userName@ip
```
（2）在配置里禁止root登陆，创建密钥对登陆，详见[使用密钥认证服务器](https://loricheung.github.io/2016/10/02/ssh-server-with-public-key.html)但是当你需在不同机器上登录时还是会有点麻烦，而且万一丢失了私钥也会比较麻烦。

## 关于linux系统下的挂载
详见[Ubuntu - 硬盘分区、格式化、自动挂载配置](http://bruce007.blog.51cto.com/7748327/1322236)

根据UUID自动挂载要修改/etc/fstab 文件，一般不重新分区UUID号不会变化。

```
sudo blkid # 查看设备uuid
```


## 关于samba文件服务器
Samba的配置文件为‘/etc/samba/smb.conf’。
开启smbd服务后有时候还是无法连接，可能是防火墙的原因，通过关闭防火墙来验证。如果是，只用文件和打印共享服务的话，开启139(TCP)端口就可以。

```
$ sudo service smbd restart
```


```
# 测试工具。比如设置的限定用户无效。
$ sudo testparm
```


```
# 查看已添加的sabd用户
$ sudo pdbedit -L
```
## [scp跨机远程拷贝](http://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/scp.html)
1. 从远处复制文件到本地目录

```
$ scp root@10.6.159.147:/opt/soft/demo.tar /opt/soft/
```

2. 从远处复制到本地

```
$ scp -r root@10.6.159.147:/opt/soft/test /opt/soft/
```

3. 上传本地文件到远程机器指定目录

```
$ scp /opt/soft/demo.tar root@10.6.159.147:/opt/soft/scptest
```

4. 上传本地目录到远程机器指定目录

```
$ scp -r /opt/soft/test root@10.6.159.147:/opt/soft/scptest
```


## 其他
因为服务器文件夹和文件名都为中文，ls -ll 后看到的都是？？？无法区分内容，按某宗师给的建议，ls -ll > /seeFileName.txt 拷贝到U盘，在windows上查看中文名称。
从ubuntu的utf-8拷贝到windows机器上文件名会乱码且无法更改编码方式情况下，一种不严谨的做法：格式化一个ntfs的硬盘挂载上去，拷贝出来就不会，因为linux会自动转换ntfs能识别的格式。


参考文档：
[Ubuntu的防火墙配置-ufw-iptables](http://forum.ubuntu.org.cn/viewtopic.php?f=169&t=292611)

[Ubuntu开启SSH登录](https://segmentfault.com/a/1190000004686476)

[Ubuntu环境下SSH的安装及使用](http://blog.csdn.net/netwalk/article/details/12952051)

[Linux添加/删除用户和用户组](http://www.cnblogs.com/xd502djj/archive/2011/11/23/2260094.html)

[samba服务器配置](http://seanxp.com/2016/samba/)

