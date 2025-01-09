---
title: 解决DataBinding和kotlin共用报错的问题
date: 2016-02-23 19:39:35
tags:
- Android 
- DataBinding
- Kotlin
categories:
- Android
aliases:
- /Android/DataBinding-kotlin-error/
summary: 两天kotlin1.0正式版发布的事情在Android 开发者和Java程序员中炸开了锅。其中不少Android程序员们肯定都想试试这个新玩意。但是如果你之前已经习惯于用Data Binding框架开发的话，你肯定会遇到这样一个问题：DataBinding和kotlin放在一个项目中编译执行会报错。

---
# 前言
> Kotlin 是一种在 Java虚拟机上执行的静态型别编程语言，它也可以被编译成为JavaScript源代码。它主要是由俄罗斯圣彼得堡的JetBrains开发团队所发展出来的编程语言，其名称来自于圣彼得堡附近的科特林岛。[2] 在2012年一月的著名期刊Dr. Dobb's Journal中 Kotlin 被认为是该月份最佳语言。[3] 虽然跟 Java 语法并不相容，但 Kotlin 被设计成可以和 Java 程式码相互运作，并可以重复使用如Java集合框架等的现有Java 类别库。 ----[维基百科](https://zh.wikipedia.org/wiki/Kotlin)

两天kotlin1.0正式版发布的事情在Android 开发者和Java程序员中炸开了锅。其中不少Android程序员们肯定都想试试这个新玩意。但是如果你之前已经习惯于用Data Binding框架开发的话，你肯定会遇到这样一个问题：DataBinding和kotlin放在一个项目中编译执行会报错。如下图

![错误信息](https://xietzt-blog.oss-cn-beijing.aliyuncs.com/blogdatabinding-kotlin_error.png)
# 解决方案
本解决方案抄袭于[此处](http://qiita.com/umetsu/items/487d38be86c31ff59075) 在app下的build.gradle文件中添加下列代码
```
dependencies {
     // ... 略 
     kapt 'com.android.databinding:compiler:1.0-rc5'//如果解决不了把版本改为你项目的版本 
} 
kapt { 
    generateStubs = true 
}
```

![解决方案](https://xietzt-blog.oss-cn-beijing.aliyuncs.com/blog%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88.png)