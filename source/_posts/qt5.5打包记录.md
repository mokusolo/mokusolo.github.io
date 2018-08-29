---
title: qt5.5打包记录
date: 2017-03-02 00:37:35
tags: [qt5, 打包]
---

#### <i class="icon-pencil"></i> windeployqt.exe

qt内置的应用，在qt目录搜索就得到了路径，~/Qt/Qt5.5.1/5.5/mingw492_32/bin/仅作参考。用法为用命令行windeployqt.exe your_application_name.exe（qt在release下编译得到的.exe文件），就会把依赖的东西都拷贝进去。但是很不靠谱！

#### <i class="icon-pencil"></i> 手动拷贝
现在your_application_name.exe和一堆依赖文件在一个文件夹，双击看看还差什么dll去qt里搜了复制过来。

#### <i class="icon-pencil"></i> 无法定位程序输入点于动态链接库
一个字，坑。。。可能是因为我环境变量比较乱，上面脚本拷贝的QtCore5.dll这种库不对，从ProcessExplorer和depends看了半天也没想通。。后来看到有人说环境变量的事，和qt目录下的dll一对比的确大小不一样，拷贝正确的过去就可以了。

#### <i class="icon-pencil"></i> Enigma Virtual Box打包没什么好记录的
别忘记勾压缩选项


参考：
【1】https://www.shintaku.cc/posts/qt/
【2】http://blog.163.com/dongjuan_1112/blog/static/546829832011213111556/
