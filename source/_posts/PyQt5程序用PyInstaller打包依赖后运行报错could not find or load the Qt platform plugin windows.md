---
title: PyQt5程序用PyInstaller打包依赖后运行报错could not find or load the Qt platform plugin windows
date: 2018-09-27 20:46:13
tags: [PyQt5, PyInstaller, 打包]
---

用PyQt5写了个简单的程序，PyInstaller打包依赖后在两个同事电脑上运行正常，但另一同事使用后发现报错，内容为：
> This application failed to start because it could not find or load the Qt platform plugin "windows" in "".
>
> Reinstalling the application may fix this problem.

谷歌到的相关问题很多，参考[Qt 5.1.1: Application failed to start because platform plugin “windows” is missing](https://stackoverflow.com/questions/20495620/qt-5-1-1-application-failed-to-start-because-platform-plugin-windows-is-missi)进行了一系列的尝试，比如：
1. 在.exe同目录下添加Qt目录下的platforms\qwindows.dll
2. 在.exe通路录下添加Qt目录下的libEGL.dll
3. 。。。

均无果。

![](/img/pyqt5_error.png)

最后无意中发现U盘直接拷贝过去的可以进行，而区别在于目录名一个为中文（❌）一个为英文（✔）。

对于我这种情况的解决方案：把程序放在纯英文目录下。
