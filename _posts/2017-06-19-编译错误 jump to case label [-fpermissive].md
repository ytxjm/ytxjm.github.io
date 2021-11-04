---
layout: post
title: 编译错误 jump to case label [-fpermissive]
tags: C++
math: true
date: 2017-06-19 15:32 +0800
---


问题：Ｃ语言编程时，在switch case 结构中,　如果在case里定义变量就会出现这种编译错误：jump to case label [-fpermissive]

原因：编译器认为这种写法会错过变量的定义,因些报这个错。
解决方法:将变量的定义移到switch case结构之上；
总结:不要在case语句下定义变量;
