---
title: vs报错无法启动程序xxx.exe。系统找不到指定的文件
date: 2017-01-19 21:52:56
tags: [vs]
---
为了学习一点openGL基础知识装上了visual studio 2012，然后按教程在工程里指定好第三方库和头文件目录，链接好，但是main.cpp里即使只输出hello world都报错“无法启动程序F:\openGL\vs\test\Debug\test.exe。系统找不到指定的文件。”
搜到的答案都未能解决，最后发现是因为直接新增的.cpp文件，当然找不到了，正确的姿势应该是在工程里添加文件。
