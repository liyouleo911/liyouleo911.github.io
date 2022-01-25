---
layout: post
title:  "让Objective-C库支持Swift Package Manager"
categories: iOS 
---

这篇文章主要讲述两个知识点
> - Objective-C源码库支持 Swift Package Manager
> - 将源码打包成 XCFramework支持 Swift Package Manager

### 将 Objective-C 项目库支持 Swift Package Manager
以[YTKKeyValueStore](https://github.com/yuantiku/YTKKeyValueStore)为例，该项目为Objective-C编写，且作者只支持了CocoaPods的集成方式，我们现在让它支持通过Swift Package Manager集成

* 先fork一下官方库，将fork后的库clone到本地

  ![step1](/assets/Pasted image 20211214160532.png)

* 在当前目录打开终端，执行`swift package init`初始化SPM

  ![step2](/assets/Pasted image 20211214160618.png)

* 为了尽量不改变原工程的结构，我们删除Sources和Tests两个目录，新建一个src目录，将YTKKeyValueStore.h和YTKKeyValueStore.m两个文件拷入；在src目录下新建include目录，在include目录打开终端，执行`ln -s ../YTKKeyValueStore.h YTKKeyValueStore.h`为头文件创造软链接，这样如果后续有改动头文件这里会同步改动

  ![step3](/assets/Pasted image 20211214161415.png)

* 修改Package.swift文件，加入FMDB的依赖，删除testTarget，并将target的path指向src文件夹[^path]

  ![step4](/assets/Pasted image 20211214161524.png)

* 编译通过，推送到git上即可通过Swift Package Manager引入该库

### 将二进制库支持Swift Package Manager
未完待续。。。

[^path]:Swift Package Manager的Target有一个叫做publicHeadersPath的参数，是需要设置暴露的头文件的。因为publicHeadersPath默认的地址就是就是path/include，默认path的路径是Sources/package_name。现在我们修改了path，那么publicHeadersPath的默认路径就变成了src/include