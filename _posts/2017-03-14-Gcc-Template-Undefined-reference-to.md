---
layout: post
title:  "C++模板遇到undefined reference to值得注意的地方"
date:   2017-03-14
excerpt: "Gcc与模板的冲突与解决方法"
tag:
- Little Knowledge
comments: true
---
前几天用Qt写课设的时候，发现写完链表之后总是会出现这个错误

`undefined reference to (某方法)`

一开始我以为是我语法的问题，最后直接写了一个最简单的模板，还是无法解决。
无奈这个课设比较赶，就没用模板就直接可以的

结果今天写红黑树的时候又碰到了这个问题

经查阅资料发现，**对于模板并且头文件与实现文件分开时，Gcc无法找到其方法**。~~这就完完全全是所谓的编译器问题了~~

其实解决方法也是很简单的：
+ 将include中的头文件改成实现文件。 eg #include <rbtree.h> => #include <rbtree.cpp>
+ 在头文件的最后 include 实现文件。

实际上这两个方法是一个道理，就是将两个文件并成一个文件。
