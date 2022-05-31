---
layout:     post
title:      android无反射插件化框架Shadow笔记
subtitle:   
date:       2019-10-15
author:     K
header-img: img/post-bg-desk.jpg
catalog: true
tags:
    - android 
	- 插件化 
---

## Tencent/Shadow

[Tencent/Shadow](https://github.com/Tencent/Shadow "Tencent/Shadow") 

官方描述：
Shadow是一个腾讯自主研发的Android插件框架，经过线上亿级用户量检验。 
Shadow不仅开源分享了插件技术的关键代码，还完整的分享了上线部署所需要的所有设计。

官方技术分享：
Shadow的设计细节将在掘金持续分享:[Shadow掘金](https://juejin.im/user/5d12faee6fb9a07ed8425178/posts "Shadow掘金") 

# 原理

原话：
用代理Activity作为壳子注册在宿主中真正运行起来，然后让它持有插件Activity，想办法在收到系统的生命周期方法调用时转调插件Activity的对应生命周期方法。

白话：
搞了个中间层ShadowActivity（壳），这个才是真的Activity，插件里的Activity都在编译阶段transform成了普通的java class，作为Activity的成员使用，方法基本和Activity一一映射。

原话：
```java
class ShadowActivity {
    ContainerActivity containerActivity;
    public void onCreate(Bundle savedInstanceState) {
        containerActivity.superOnCreate(savedInstanceState);
    }
}

class PluginActivity extends ShadowActivity {
    @Override
    public void onCreate(Bundle savedInstanceState) {
        savedInstanceState.clear();
        super.onCreate(savedInstanceState);
    }
}

class ContainerActivity extends Activity {
    ShadowActivity pluginActivity;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        pluginActivity.onCreate(savedInstanceState);
    }

    public void superOnCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
    }
}
```
仔细思考下现在的调用结果，是不是PluginActivity在正常安装运行和插件环境下运行时行为就一致了？

白话：
通过上述略显别扭的方法解决了PluginActivity生命周期方法super和自身方法的调用顺序问题。


原话：
现在就可以考虑怎么实施AOP手段将PluginActivity的父类从系统Activity改为ShadowActivity了。Shadow最终使用的是字节码编辑手段。字节码编辑可以通过Android官方的构建过程中的Transform API来实现

白话：
没啥说了，参考[Transform API](http://tools.android.com/tech-docs/new-build-system/transform-api "Transform API") ，一般要用到Javassist 

原话：
通过Shadow Transform将App中用到的WebView都换成ShadowWebView。再Override ShadowWebView的loadUrl方法。将请求来的file:///android_asset/协议都修改成http://android.asset/协议。

白话：
所有file://通过loadUrl转http://，再通过shouldInterceptRequest之类的拦截成本地资源

备注：
Shadow要求插件和宿主包名一致，主要是怕找资源时（比如resources.arsc）没找着crash，另外也不想去兼容各种OEM系统；
Fragment代码调试断点对不上；






