---
layout: post
title: ROS中编译已存在的Package
tags: Ros
math: true
date: 2017-06-19 15:32 +0800
---


 一台机器上创建好的Ros Package要拷贝到另一个机器上，容易出现找不到包的情况。为了解决这个问题,做了很多方面的尝试，得出一些结论。
首先环境变量要设置的对，Ros package必须要放在环境变量所在路径下，Ros才有可能找到这个Package。
设置环境变量 ：

export ROS_PACKAGE_PATH="$ROS_PACKAGE_PATH:/home/username/rosdir"
其中：
ROS_PACKAGE_PATH：Ros环境变量名称；
/home/username/rosdir：Package所在的绝对路径；

如果环境变量弃置成功后还无法找到Package，查看一下Package中manifest.xml或package.xml文件，这里以mainfest.xml为例。
Ros在找package的时候只认manifest.xml或package.xml文件。mainifest.xml文件内容如下:
``` xml
<package>
  <description brief="TestRosNode">
     TestRosNode
  </description>
  <author>username</author>
  <license>BSD</license>
  <review status="unreviewed" notes=""/>
  <url>http://ros.org/wiki/TestRosNode</url>
  <depend package="std_msgs"/>
  <depend package="rospy"/>
  <depend package="roscpp"/>
</package>
```

其中：

TestRosNode：Package的名字，后面是一些额外的描述和依赖关系。

package.xml文件与manifest.xml文件中的内容大同小异，在这里就不做具体分析了。

在设置好manifest.xml或package.xml文件后，运行：
rosmake TestRosNode TestRosNode
如果还是提示找到不package，在终端中运行这两个命令：
rospack profile
rospack find TestRosNode
其中：

TestRosNode是Package名。

运行完之后一般就可以找到对应的Package了。知道这个之后，就可以轻松的让Ros找到一个Package，找这个包之后可以进行编译工作了。

编译的时候要有makefile文件，在Ros包中的内容是这样的，没有这个是无法编译的。makefile文件中的内容如下：

include $(shell rospack find mk)/cmake.mk
Ros有很多版本，有的版本中roscreate-pkg之后，默认的CMakeList文件中，是没有生成可执行文件的命令的，要手动加上。CMakeList.txt 文件如下所示：

``` cmake
cmake_minimum_required(VERSION 2.4.6)
include($ENV{ROS_ROOT}/core/rosbuild/rosbuild.cmake)

# Set the build type.  Options are:
#  Coverage       : w/ debug symbols, w/o optimization, w/ code-coverage
#  Debug          : w/ debug symbols, w/o optimization
#  Release        : w/o debug symbols, w/ optimization
#  RelWithDebInfo : w/ debug symbols, w/ optimization
#  MinSizeRel     : w/o debug symbols, w/ optimization, stripped binaries
#set(ROS_BUILD_TYPE RelWithDebInfo)

rosbuild_init()

#set the default path for built executables to the "bin" directory
set(EXECUTABLE_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/bin)
#set the default path for built libraries to the "lib" directory
set(LIBRARY_OUTPUT_PATH ${PROJECT_SOURCE_DIR}/lib)

#uncomment if you have defined messages
rosbuild_genmsg()
#uncomment if you have defined services
rosbuild_gensrv()

#common commands for building c++ executables and libraries
#rosbuild_add_library(${PROJECT_NAME} src/example.cpp)
#target_link_libraries(${PROJECT_NAME} another_library)
#rosbuild_add_boost_directories()
#rosbuild_link_boost(${PROJECT_NAME} thread)
#rosbuild_add_executable(example examples/example.cpp)
#target_link_libraries(example ${PROJECT_NAME})
rosbuild_add_executable(TestRosNode ${ALL_SRC})
```

其中：

rosbuild_genmsg()和rosbuild_gensrv()是在生成自定义msg和srv时使用的。

rosbuild_add_executable(TestRosNode ${ALL_SRC})这句的作用是编译之后生成可执行文件，生成的可执
行文件可以脱离Ros独立运行如：

./TestRosNode
TestRosNode是生成的可执行文件。

更多详细内容可参考官Ros方文档，http://wiki.ros.org/ROS/Tutorials/CreatingPackage，一定要注意上面有 catkin和rosbuild两种模式