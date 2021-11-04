---
layout: post
title: VSCode调试C++
tags: C++
math: true
date: 2021-01-05 15:32 +0800
---


测试可用版本集合：
**vscode： 1.48.2
remote-containers：v0.128.0
C/C++:v0.28.0**


## 0.编译
```bash
bazel build common/tools/simulation:simulation_main --compilation_mode=dbg -s
```

## 1.启动docker Container
## 2.vscode安装Remote - Containers插件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105192504159.png)

## 3.安装c++插件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105192403862.png)

## 4.点击最左下角的绿色的两个箭头
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105192335311.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW54am0=,size_16,color_FFFFFF,t_70)

## 5.选择Attach to Running Container，并选择1中已经启动好的对应Container
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105192308663.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW54am0=,size_16,color_FFFFFF,t_70)

## 6.连上container之后，在vscode中打开工程目录，并添加launch文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105192216188.png)


```
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "name": "(gdb) Launch",
            "type": "cppdbg",
            "request": "launch",
            "program": "/test_dir/test_binary",
            "args": ["--arg1=a",
                     "--arg2=b",
                     "--arg3=3",
                    ],
            "stopAtEntry": false,
            "cwd": "/autocar",
            "environment": [],
            "externalConsole": false,
            "MIMode": "gdb",
            "setupCommands": [
                {
                    "description": "Enable pretty-printing for gdb",
                    "text": "-enable-pretty-printing",
                    "ignoreFailures": true
                }
            ]
        }
    ]
}
```
其中:
**program**：为可执行程序
**args**：为执行的输入参数
**cwd**：为执行的工作目录

**一些调试快捷键：**
Continue / Pause: F5 （继续执行直到下一次运行断点）
Step Over: F10 （跳过此行）
Step Into: F11 （进入函数）
Step Out: Shift+F11 （跳出函数）
Restart: Ctrl+Shift+F5
Stop: Shift+F5

最新的remote-containers有bug经常会stuck(卡住),可以用其它的版本1.28比较好![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105211650290.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW54am0=,size_16,color_FFFFFF,t_70)


 ，这里提供老版的链接[remote-containers old version](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers&ssr=false#version-history)，
自存的备份：https://download.csdn.net/download/xianxjm/14028853
下载完成后需要用离线的方法安装vscode插件。

# 离线安装VSCode插件
## 1.打开VSCode的软件，选择左侧Extension
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105190349233.png)
## ２.点击“…”，选择“从VSIX安装”，选择离线下载的VSCode插件进行安装。![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105190349320.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW54am0=,size_16,color_FFFFFF,t_70)
# 快速开发模板
VsCode Debug C++ with Cmake
https://download.csdn.net/download/xianxjm/36789410