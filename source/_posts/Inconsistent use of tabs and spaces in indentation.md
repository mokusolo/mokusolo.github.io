---
title: Inconsistent use of tabs and spaces in indentation
date: 2017-01-11 22:14:23
tags: [python, SyntaxError]
---
众所周知，写python必经SyntaxError。
学习c++时习惯了tab，对四个空格怎么也喜欢不起来。
作为一个非专业python用户，在sublime text改的一点python代码，格式上怎么改动都看不出异常，用Notepad++查看发现有不符合对应关系的+（可展开标志），在python 3.4.3 IDLE能明显看出问题，改动部分的tab步长为8，而不是4。遂改之，谨记。
