---
layout: post
title: 使用 QtCreator 编译调试Ros 教程
tags: Qt, Ros
math: true
date: 2017-07-12 15:32 +0800
---


1,编写RosNode，编译通过后，用QtCreator重新创建工程，在.pro中添加默认包含的文件和链接的so；

2,用在QtCreator编译RosNode，编译成功后，在~/.bashrc中添加：
``` shell
export ROS_MASTER_URI=http://localhost:11311
```
保存后。有两种方式可以使用QT调试程序：

A. 在终端中打开QT
``` shell
cd /opt/Qt5.7.0/Tools/QtCreator/bin（这是用sudo 安装qt后的路径，如果安装时没有加sudo可在~目录下找到相应目录）
./qtcreator
```
打开工程就可以调试。

B.不在终端中打开QtCreator，需要配置一下，
``` shell
cd /usr/share/applications/（以qt5.7为例）
```
编辑DigiaQt-qtcreator-community.desktop

编辑完之后，内容是： 
``` shell
[Desktop Entry]

Type=Application

Exec=bash -i -c /opt/Qt5.7.0/Tools/QtCreator/bin/qtcreator

Name=Qt Creator (Community)

GenericName=The IDE of choice for Qt development.

Icon=QtProject-qtcreator

Terminal=false

Categories=Development;IDE;Qt;

MimeType=text/x-c++src;text/x-c++hdr;text/x-xsrc;application/x-designer;application/vnd.qt.qm akeprofile;application/vnd.qt.xml.resource;text/x-qml;text/x-qt.qml;text/x-qt.qbs;
```
然后打开QT即可调试RosNode。
