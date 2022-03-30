---
layout: post
title: vscode调试(docker)程序的配置
tags: vscode
math: true
date: 2022-03-28 15:32 +0800
---



#### 1.编译要调试的可执行程序

a. bazel中要加--compilation_mode=dbg -s

```shell
bazel build common/tools/simulation:simulation_main --compilation_mode=dbg -s
```

b. cmake中要加--DCMAKE_BUILD_TYPE=Debug

```shell
cmake --DCMAKE_BUILD_TYPE=Debug ..
```


#### 3.启动docker container(没有的跳过)，打开vscode，打开相应的工程文件夹


#### 4.vscode安装Remote - Containers插件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105192504159.png)


#### 5.安装c++插件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105192403862.png)


#### 6.点击最左下角的绿色的两个箭头
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105192335311.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW54am0=,size_16,color_FFFFFF,t_70)


#### 7.选择Attach to Running Container，并选择1中已经启动好的对应Container
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105192308663.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW54am0=,size_16,color_FFFFFF,t_70)


#### 8.连上container之后，在vscode中打开工程目录，并添加launch文件
![在这里插入图片描述](https://img-blog.csdnimg.cn/20210105192216188.png)


```json
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
            "program": "${workspaceRoot}/test_dir/test_binary",
            "args": ["--arg1=a",
                     "--arg2=b",
                     "--arg3=3",
                    ],
            "stopAtEntry": false,
            "cwd": "/pwd",
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
```
program：为可执行程序

args：为执行的输入参数

cwd：为执行的工作目录
```


一些调试快捷键：
```
Continue / Pause: F5 （继续执行直到下一次运行断点）
Step Over: F10 （跳过此行）
Step Into: F11 （进入函数）
Step Out: Shift+F11 （跳出函数）
Restart: Ctrl+Shift+F5
Stop: Shift+F5
```



## 快速开发模板

[VsCode Debug C++ with Cmake](https://github.com/ytxjm/HelloWorld)



参考：
https://stackoverflow.com/questions/66057404/launch-program-does-not-exist-in-visual-studio-code










以上。
