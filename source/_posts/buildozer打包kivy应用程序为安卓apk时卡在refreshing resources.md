---
title: buildozer打包kivy应用程序为安卓apk时卡在refreshing resources
date: 2018-10-16 20:28:13
tags: [Kivy]
---
按照kivy官方教程[Create a package for Android](https://kivy.org/doc/stable/guide/packaging-android.html#packaging-android)

在ubuntu虚拟机下运行到以下命令时卡在Refresh Sources: Fetching https://dl-ssl.google.com/android/repository/addons_list-2.xml
```
buildozer android debug deploy run
```
按[VMWare虚拟机通过主机shadowsocks代理上网](https://blog.csdn.net/u010726042/article/details/53187937)开启了代理后，即使可以在浏览器访问该链接也还是无法进行下去。

翻了好多帖子后，在[How to build Python for Android with Ubuntu and Buildozer](https://kivyspacegame.wordpress.com/2014/06/30/tutorial-how-to-build-python-for-android-with-ubuntu-and-buildozer/)的回复下发现有一条写
> Moved forward by following below .cfg solution.
http://stackoverflow.com/questions/10634202/android-sdk-manager-proxy-settings-in-linux

按最高赞的回复解决。
```
nano ~/.android/androidtool.cfg
```
根据个人实际情况按以下格式修改:
```
http.proxyPort=1080  # 宿主机上shadowsocks的代理端口
sdkman.monitor.density=108
http.proxyHost=192.168.2.xxx  # 宿主机的ip，这里为192.168.2.xxx
sdkman.show.update.only=true
sdkman.ask.adb.restart=false
sdkman.force.http=true
sdkman.show.updateonly=true
```

后续又卡在了
```
Downloading hostpython2 from https://python.org/ftp/python/2.7.2/Python-2.7.2.tar.bz2
```
报错为
> IOError: [Errno socket error] [SSL: UNKNOWN_PROTOCOL] unknown protocol (_ssl.c:590)

未能找到解决方案，转而使用kivy提供的虚拟机[The Kivy Android Virtual Machine](https://kivy.org/doc/stable/guide/packaging-android-vm.html)，同样进行了SDK Manager代理设置后，成功打包了apk文件，在四儿子上测试安装可运行。
