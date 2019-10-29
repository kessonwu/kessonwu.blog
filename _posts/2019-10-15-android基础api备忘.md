---
layout:     post
title:      android api备忘
subtitle:   
date:       2019-10-15
author:     K
header-img: img/post-bg-desk.jpg
catalog: true
tags:
    - java
---

## Scroller OverScroller

[Scroller](https://developer.android.com/reference/android/widget/Scroller "Scroller") api 1

[OverScroller](https://developer.android.com/reference/android/widget/OverScroller "OverScroller") api 9

都是用来辅助实现平滑滚动的类，基本用OverScroller就可以了，ScrollerCompat(deprecated)是对OverScroller的封装，OverScroller相对Scroller增加了边缘相关的一些api

```java
isOverScrolled()
springBack(int startX, int startY, int minX, int maxX, int minY, int maxY)
fling(int startX, int startY, int velocityX, int velocityY, int minX, int maxX, int minY, int maxY, int overX, int overY)
notifyHorizontalEdgeReached(int startX, int finalX, int overX)
notifyVerticalEdgeReached(int startY, int finalY, int overY)
```

## Assets读取
Assets不能直接读取文件为File对象，不过可以拷贝到外存再new File()使用
```java
public static void writeBytesToFile(InputStream is, File file) throws IOException{
    FileOutputStream fos = null;
    try {  
        byte[] data = new byte[2048];
        int len = 0;
        fos = new FileOutputStream(file);
        while((len=is.read(data))>-1){
            fos.write(data,0,len);              
        }
    }
    catch (Exception ex) {
        Log.e("Exception",ex);
    }
    finally{
        if (fos!=null){
            fos.close();
        }
    }
}
```
WebView可以直接访问内部资源
```java
WebView wv = new WebView(context);      
wv.loadUrl("file:///android_asset/help/index.html");
```

getExternalFilesDir()能获取到Android/data/包名/files目录，目录不需申请读写权限
```java
File Context.getFilesDir();
File Context.getExternalFilesDir();
File Context.getCacheDir();
File Context.getExternalCacheDir();
```








