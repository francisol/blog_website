+++
title='conan使用简要实例'
date = '2017-02-15 14:19:27'
tags= ['工具']
categories= ['C++']
keywords=['C++','conan']
aliases= ["/C/conan-example/"]
summary='在Java开发中,我们有maven做依赖管理,在PHP开发中我们有Composer做管理,Android、iOS、Python、NodeJS等等都有各自的管理工具,但是C/C++没那么幸运了,很晚才出现包管理工具,下面就让我们看看如何使用C/C++的包管理工具.'
+++

{{<outdated  content="本内容为 conan1，且年代久远，目前已过期。">}}

# 前言
在Java开发中,我们有maven做依赖管理,在PHP开发中我们有Composer做管理,Android、iOS、Python、NodeJS等等都有各自的管理工具,但是C/C++没那么幸运了,很晚才出现包管理工具.

在C/C++开发的时候,尤其是Linux开发.我们需要加入一些第三方的依赖,例如Openssl或者其他的一些库.之前我们或者直接在系统安装开发包;或者从源码编译,然后引用.过程是痛苦的,管理是混乱的.想我这样有不想把系统"搞脏",又不想自己去build,急需一个工具替我进行管理,解放我的双手.

# 工具介绍
在网上没有一个权威的管理工具,也许是因为C/C++太自由了吧,勉强找到两个工具一个是[conan](https://www.conan.io) 、一个是[biicode](https://github.com/biicode/),但是biicode服务器已经关闭了,代码也是两年前的了,基本是废了,不用考虑.如果有精力的话可以折腾一下它的代码,试着自己搭建一个服务器.

接下来我们就试着用conan来管理我们的项目.

# conan使用

## 安装

conan是用python写的,所以在安装conan之前要先安装Python,当然*nix系统自带了Python,如果是Windows用户,建议安装Python2.7, py3是异端啊!!! py的安装就不赘述了.

Python安装好之后执行一下代码安装
```
pip install conan
```
之后运行`conan`,输出一大堆信息表示安装成功了.

conan推荐用[CMake](https://cmake.org/)组织项目,所以,还需要下载CMake.

## 创建项目

用官网给出的Timer例子进行示范,可以通过下面的命令获取项目

```
git clone https://github.com/memsharded/example-poco-timer.git mytimer
```

或者自己在文件夹下创建三个文件**timer.cpp** **conanfile.txt** **CMakeLists.txt**

**timer.cpp**
```cpp
// $Id: //poco/1.4/Foundation/samples/Timer/src/Timer.cpp#1 $
// This sample demonstrates the Timer and Stopwatch classes.
// Copyright (c) 2004-2006, Applied Informatics Software Engineering GmbH.
// and Contributors.
// SPDX-License-Identifier:     BSL-1.0

#include "Poco/Timer.h"
#include "Poco/Thread.h"
#include "Poco/Stopwatch.h"
#include <iostream>

using Poco::Timer;
using Poco::TimerCallback;
using Poco::Thread;
using Poco::Stopwatch;

class TimerExample{
public:
        TimerExample(){ _sw.start();}

        void onTimer(Timer& timer){
                std::cout << "Callback called after " << _sw.elapsed()/1000 << " milliseconds." << std::endl;
        }
private:
        Stopwatch _sw;
};

int main(int argc, char** argv){
        TimerExample example;
        Timer timer(250, 500);
        timer.start(TimerCallback<TimerExample>(example, &TimerExample::onTimer));

        Thread::sleep(5000);
        timer.stop();
        return 0;
}
```
**conanfile.txt**
```ini
[requires]
Poco/1.7.3@lasote/stable

[generators]
cmake
```
**conanfile.txt** 是用来声明依赖的,依赖可以在其官网[搜索](https://www.conan.io/search?q=*),要看清相关的依赖是否支持工作的操作系统
文件中有四个值`[requires]`、`[generators]`、`[options]`、`[imports]`
- [options] 用来配置包的一些属性,例如Poco:shared=True,格式为 `库名称:属性=值`
- [requires] 用来申明依赖,每一行代表一个依赖
- [generators] 指明依赖build的工具,多数是cmake
- [imports] 用来拷贝动态链接库

**CMakeLists.txt**
```
project(FoundationTimer)
cmake_minimum_required(VERSION 2.8.12)

include(${CMAKE_BINARY_DIR}/conanbuildinfo.cmake)
conan_basic_setup()

add_executable(timer timer.cpp)
target_link_libraries(timer ${CONAN_LIBS})
```

## 编译

在项目根目录新创建一个文件夹进行编译工作
```
mkdir build && cd build
```
在依赖安装之前我们要查看一下我们的依赖信息
```
conan info ..
```
如果这些依赖之前用过那么就需要我们只需要执行
```
conan install ..
```
如果有一些依赖我们之前没有没有用过,需要执行
```
conan install .. --build lib1name lib2name ....//libname表示之前没有使用过的依赖名称例如 OpenSSL等等(不需要理会 zlib 和electric-fence)
```

因为需要对依赖进行编译,所以等待时间稍长.

命令执行完之后,对项目进行编译
```
(win)
$ cmake .. -G "Visual Studio 14 Win64"
$ cmake --build . --config Release

(linux, mac)
$ cmake .. -G "Unix Makefiles" -DCMAKE_BUILD_TYPE=Release
$ cmake --build .
```
编译之后,执行`./bin/timer`便大功告成

## 配置
 ### options 
  如果设置库 share=ture,那么该库就会以链接的方式而不打包进可执行文件
  添加 conanfile.txt 如下:
  ```
  [options]
  Poco:shared=True
  ```
  再次进行编译,发现这次生成的文件会比之前小很多,而且执行 `ldd ./bin/timer`, 发现可执行文件链接了一个**libPocoFoundation.so.43*的库

  ### imports
  ```
  [imports]
  bin, *.dll -> ./bin # Copies all dll files from packages bin folder to my "bin" folder
  lib, *.dylib* -> ./bin # Copies all dylib files from packages lib folder to my "bin" folder
  ```
  用来拷贝动态链接库对Linux没用


----
相关链接
- [conan帮助文档](http://docs.conan.io/en/latest/index.html)  