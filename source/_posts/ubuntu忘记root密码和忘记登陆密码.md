---
title: ubuntu忘记root密码和忘记登陆密码
date: 2017-01-11 22:14:53
tags: [ubuntu]
---
**root密码**

 - 开机按住shift
 - 键盘上下键移动到recovery模式按e
 - 找到linux /boot/vmlinuz...这一行
 - 把ro single 改成rw single init=/bin/bash
 - ctrl+x重启
 - 现在是root权限了，在命令行输passwd设定root密码

----------
**登陆密码**

 - 开机按住shift
 - 键盘上下键移动到recovery模式按回车
 - 选择root drop to root shell prompt按回车
 - 命令行passwd syt（syt是要找回密码的用户）
 - 输入新密码，看到password updated successfully即为成功

若出现​authentication token manipulation error，passwd unchanged

 - 在命令行输入 "mount -o rw,remount /"
 - 获得了修改密码的权限后重复passwd syt步骤，就会看到successfully提示
 - sudo reboot 重启，输入新的密码即可登陆进要找的syt用户​


----------
参考：
http://www.linuxidc.com/Linux/2015-03/115028.htm
