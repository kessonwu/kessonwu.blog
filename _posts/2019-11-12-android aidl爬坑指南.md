---
layout:     post
title:      android aidl基本教程&爬坑指南- 'aidl.exe' finished with non-zero exit value 1
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

## 基本步骤
-跨进程通信一般都会用到aidl，所以先在build.gradle android域增加aidl sourceSet
```java
android {
    // ...
    sourceSets {
        main {
            aidl.srcDirs += ['src/main/java']
        }
    }
}
```
-新建java类文件，implements Parcelable，describeContents返回0即可；
```java
public class BusinessInfoRecord implements Parcelable {
	// ...
}
```
接下来要创建三种aidl文件：传输类、Service回调接口、Service调用接口。

-创建传输数据的aidl类文件：同包新建同名的aidl文件，注意文件中parcelable是小写
```java
package ...;
parcelable BusinessInfoRecord;
```

-新建Service回调接口aidl文件，即Service主动调主进程的接口，建议命名为IXXServiceCallback
```java
import ...BusinessInfoRecord;
interface IBusinessInfoCallback {
    oneway void onInit();
    oneway void onDataRefresh();
	// ...
}
```

-创建Service调用接口aidl文件，即主进程调Service的接口
```java
import ...BusinessInfoRecord;
import ...IBusinessInfoCallback;
interface IBusinessInfoService {
    oneway void registerBusinessInfoCallback(in IBusinessInfoCallback callback);
	List<BusinessInfoRecord> queryAll();
    oneway void refresh(String id, @nullable String subId);
	// ...
}
```
严重注意：
方法不能重载
有@nullable修饰，但没有@nonnull
非基本数据类型方法参数需指定数据流向in、out 或 inout，基本数据累心默认都是in，包括String

-make目录，一般用Studio的make project/module就行，会生成对应类，比如马上要用到的IBusinessInfoService.Stub

-新建Service，在onBind中返回一个实现Service调用接口的Binder
```java
public class BusinessInfoService extends Service {
	@Override
    public IBinder onBind(Intent intent) {
        return mBinder;
    }
	
    private IBusinessInfoService.Stub mBinder = new IBusinessInfoService.Stub() {
	
        @Override
        public void registerBusinessInfoCallback(IBusinessInfoCallback callback) throws RemoteException {
            // ...
        }

        @Override
        public List<BusinessInfoRecord> queryAll() throws RemoteException {
            // ...
        }
		
        @Override
        public void refresh(String id, String subId) throws RemoteException {
            // ...
		}
	}
}
```

-主进程中在需要启动Service的地方bindService，通过asInterface获得一个Binder对象，就可以主动调Service对应的方法了，需要监听Service回调的注册下之前定义好的registerBusinessInfoCallback方法实现对应接口即可
```java
// 主进程中对应管理类
public class BusinessInfoManager {
	private IBusinessInfoService sBusinessInfoService;
	private ServiceConnection sServiceConnection = new ServiceConnection() {
		@Override
		public void onServiceConnected(ComponentName name, IBinder service) {
			try {
				// 获取Service Binder调用接口
				sBusinessInfoService = IBusinessInfoService.Stub.asInterface(service);
                sBusinessInfoService.registerBusinessInfoCallback(mBusinessInfoCallback);
			} catch (Exception e) {
				// ...
			}
		}

		@Override
		public void onServiceDisconnected(ComponentName name) {
			// ...
		}
	};
    private IBusinessInfoCallback.Stub mBusinessInfoCallback = new IBusinessInfoCallback.Stub() {
		@Override
        public void onInit() throws RemoteException {
			// ...
        }
	}
}
```

-如果放在同一进程，AndroidManifest.xml中直接申明Service即可，如果要放入子进程，需指定进程名
```java
<application>
	<!-- ... -->
	<service
		android:name="...BusinessInfoService"
		android:exported="false"
		android:process=":processName" />
</application>
```

-补充说明
 'aidl.exe' finished with non-zero exit value 1，这个问题一般是aidl语法有误，编译失败了，参严重注意；
 跨进程调用会降低app性能，如非必要，可不采用，跨进程的主要优势之一是不同进程使用同一份数据。











