---
layout: post
title:  "Shell学习"
date:   2017-03-22
excerpt: "Shell的常用但是比较难记的命令(不定期更新)"
tag:
- Shell
comments: true
---

##### 前言：

        这学期自己去选了大三的Linux课，因为用了一年多的Linux，感觉到了瓶颈，就是那种日常使用已经
    基本没有问题，但总觉得只能写一些小东西，想进一步深入也不知道从何下手，本着去试试学校的课程的心
    态选了这门选修课。然而今天第一次上机实验看了一下全部实验内容，感觉………一时语塞…………就当是给自己
    一个学习的氛围和平台好了，并以此记录学习过程。
        其实说到shell，以前是学过的，但没有系统学，自己瞎学之后写了几个脚本就过去了。趁这个机会就
    把自己当前不熟的命令都记录下来。


#### stdio
1. **echo**

   echo默认输出是有换行的，不换行只要在后面加个`\c`即可

   `echo "Key\c";echo " is handsome"`
   > Key is handsome
2. **printf**

   printf 是shell的格式化输出,其用法除了括号与逗号外基本和C语言一致

   `printf "%s is a %s man\n" "Key" "handsome"`
   > Key is a handsome man

   *Note* : 并非所有shell的printf都支持浮点输出，若不支持可用 awk 自带的 printf

#### string
1. **expr**

   expr常被用于shell中的算术计算，但它也具有字符串操作的能力
   + 字符查找

        `expr index "Key is a handsome man" "hand"`
        > 8
        
        他的结果是 8 如果你仔细数一下 hand是第10位置，而第8是a。没错，这个命令只能查找单个字符，
        匹配并返回第一个 所要索引的字符串中第一个匹配的字符。
   + 选取子串

        `expr substr "Key is a handsome man" 1 3`
        > Key

   *Note* : expr命令的字符串位置是从1开始的

2. **自带命令**

    str="abbc,def,ghi,abcjkl"

    命令              | 结果                 |补充说明
    ------------------|----------------------|--------
    `echo ${str#a*c}` | `,def,ghi,abcjkl`    | 一个井号(#) 表示从左边截取掉最短的匹配 
    `echo ${str##a*c}`   |  `jkl，`               |两个井号(##) 表示从左边截取掉最长的匹配
    `echo ${str#"a*c"}`  |  `abbc,def,ghi,abcjkl`| 因为str中没有"a*c"子串
    `echo ${str##"a*c"}` |  `abbc,def,ghi,abcjkl`| 同理
    `echo ${str#*a*c*}` |                     |全部删除
    `echo ${str##*a*c*}` |                    |全部删除 
    `echo ${str#d*f)`    |  `abbc,def,ghi,abcjkl,`|
    `echo ${str#*d*f}`  |  `,ghi,abcjkl`|
    `echo ${str%a*l}`    |  `abbc,def,ghi`|  一个百分号(%)表示从右边截取最短的匹配
    `echo ${str%%b*l}`   |  `a`  | 两个百分号(%%)表示从右边截取最长匹配
    `echo ${str%a*c}`   | `abbc,def,ghi,abcjkl`|

    *Note* : 自带命令的匹配都必须从头（尾）开始匹配，无法直接匹配中间子串

<center>Updated Time: 3.23 0:51</center>
