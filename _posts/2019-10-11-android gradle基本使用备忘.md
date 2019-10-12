---
layout:     post
title:      android gradle基本使用备忘
subtitle:   
date:       2019-10-11
author:     K
header-img: img/post-bg-desk.jpg
catalog: true
tags:
    - android
---

## build.gradle为啥有两repositories和dependencies

```java
buildscript {
    repositories {
        maven {
            url "http://maven.oa.com/nexus/content/groups/androidbuild/"
        }
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:2.2.0'
    }
}

repositories {
    maven {
        url "http://maven.oa.com/nexus/content/groups/androidbuild/"
    }
}
dependencies {
    compile fileTree(include: '*.jar', dir: 'libs')
	// ...
}
```

buildscript里的表示插件构建脚本自己依赖的，根层的表示该module所依赖的

## 




