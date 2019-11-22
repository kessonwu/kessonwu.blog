---
layout:     post
title:      android aidl爬坑指南- 'aidl.exe' finished with non-zero exit value 1
subtitle:   
date:       2019-11-12
author:     K
header-img: img/post-bg-desk.jpg
catalog: true
tags:
    - java
---

## 官方文档

[Android 接口定义语言 (AIDL)](https://developer.android.com/guide/components/aidl.html#Create "Android 接口定义语言 (AIDL)")

```java

```

## 基本步骤

-新建java类文件，implements Parcelable，describeContents返回0即可；
-同包新建同名的aidl文件，注意文件中parcelable是小写
-build.gradle android域增加aidl sourceSet
-make目录，一般用Studio的make project/module就行
-Service中新建Binder
-新建回调接口，建议命名为IXXServiceCallback
-RemoteCallbackList

 'aidl.exe' finished with non-zero exit value 1
 这个问题一般是aidl语法有误，编译失败了，参考：
 
## 方法不能重载

## 非基本数据类型方法参数需指定数据流向in、out 或 inout
基本数据累心默认都是in，包括String










