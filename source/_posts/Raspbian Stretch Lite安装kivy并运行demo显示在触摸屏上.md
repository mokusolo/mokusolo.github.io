---
title: Raspbian Stretch Lite安装kivy并运行demo显示在触摸屏上
date: 2018-12-05 20:39:12
tags: [树莓派, Kivy]
---
记录下在Raspbian Stretch Lite安装kivy的过程以备查

## 准备工作
1. 从树莓派官网下载[Raspbian Stretch Lite镜像](https://www.raspberrypi.org/downloads/raspbian/)
2. windows通过Win32DiskImager（macos可通过Etcher或者命令行）将.img刷入microsd卡（此处使用的是3b）。Stretch Lite的话，4g的卡即可，但现在一般不会买4g了。。。
3. 在/boot/目录新建不含后缀的文件ssh，开启ssh功能

```
touch ssh
```
4. 上电，ssh连接。

## 在树莓派上安装kivy
官方安装指南[Installation on Raspberry Pi](https://kivy.org/doc/stable/installation/installation-rpi.html#manual-installation-on-raspbian-jessie-stretch)

此处想要安装的是python3版本，将官方教程的pip和python改为pip3和python3

1.
```
sudo apt-get update
```
2.
```
# 这里如果说没有获取某些包让apt-get update或者--fix-missing那就再后面加上--fix-missing

sudo apt-get install libsdl2-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-ttf-dev \
   pkg-config libgl1-mesa-dev libgles2-mesa-dev \
   python3-setuptools libgstreamer1.0-dev git-core \
   gstreamer1.0-plugins-{bad,base,good,ugly} \
   gstreamer1.0-{omx,alsa} python3-dev libmtdev-dev \
   xclip xsel python3-pip
```
3.
```
sudo pip3 install -U Cython==0.28.2
```
4.
```
sudo pip3 install git+https://github.com/kivy/kivy.git@master
```
太慢了，就直接浏览器下载了[1.10.x的stable版本](https://codeload.github.com/kivy/kivy/zip/stable-1.10)，再通过WinSCP传到树莓派。

5.
```
cd kivy-stable-1.10
```
6.
```
make
```
make这里会报错，后面再一起记录下

7.
```
echo "export PYTHONPATH=$(pwd):\$PYTHONPATH" >> ~/.profile
```
8.
```
source ~/.profile
```
9. 运行demo

### 遇到的问题
1. make报错Cython is missing, it's required for compiling kivy !
这里通过改变Makefile的PYTHON值解决

```
sudo nano Makefile
```
把第一行PYTHON=python改为PYTHON=python3，因为之前依赖所安装的都是python3的，Cython也是。
经过很长时间的等待，编译就通过了。
这个时候运行demo
```
python3 kivy-stable-1.10/examples/demo/showcase/main.py
```
会继续报错
2. 运行main.py报错ImportError: No module named 'pygments'

```
sudo pip3 install pygments
```
不得不提下面这个issue里有个comment简直道出了我的心声
[Unable to run kivy on raspberry pi with](https://github.com/kivy/kivy/issues/2115)
> aleksandaratanasov commented on 4 May 2016
Shouldn't this dependency be actually listed as such? I find it rather annoying that following the official guide on installing Kivy on the RPi you have to dig into the issues on GitHub to find a solution for this problem. After all the demos should be the ones that run without any issues WITH the things that are listed as requirements to get Kivy installed on the system. Please consider adding at least a warning if the required packages for the showcase are not met.

3. 这个时候运行python3 kivy-stable-1.10/examples/demo/showcase/main.py这个demo不会报错了，但是如果不连接显示屏就看不到效果。
4. 树莓派连接显示屏显示no signal

```
sudo nano /boot/config.txt
```
把
```
#hdmi_force_hotplug=1
```
的注释去掉，变为
```
hdmi_force_hotplug=1
```
参考[HDMI monitors says NO SIGNAL (solved)](https://www.raspberrypi.org/forums/viewtopic.php?t=34061)
5. 重启后运行demo应该有显示了，但是发现触摸好像没反应。
参考官方安装指南中[Using Official RPi touch display](https://kivy.org/doc/stable/installation/installation-rpi.html#using-official-rpi-touch-display)

```
sudo nano ~/.kivy/config.ini
```
更改[input]模块为
```
mouse = mouse
mtdev_%(name)s = probesysfs,provider=mtdev
hid_%(name)s = probesysfs,provider=hidinput
```
到这里就正常显示及操作了。

