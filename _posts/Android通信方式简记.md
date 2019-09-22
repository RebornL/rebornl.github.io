同进程内service和activity通过binder进行通讯。

1. 定义service，通过onBind方法返回通信的binder对象
2. 自定义binder，定义对应的回调方法
3. 在Activity中，通过serverConnection对象获取binder对象，然后设置显式intent，最后通过bindService方法绑定服务（会调用serverConnection中onServiceConnected方法获取服务）



AIDL注意事项：

1. 不能再主线程上调用，应该在其他线程上调用，这样可以避免ANR
2. 发生异常不应回传给调用方



Android IPC通信

- Android中特色的进程间通信方式Binder
- Socket通信
- Service还可以使用Messenger接口进行IPC通信，可能会比AIDL简单很多。



Android中使用多进程，在AndroidManifest文件中，给activity，service，receiver，contentprovider中指定android:process属性

- `:remote`：带`:`表明该进程是属于当前应用的私有进程，其他应用组件不可以和它跑在同一个进程中。
- `com.xxx.xxx.remote`：以完整命名方式来命名，属于全局进程，其他应用通过ShareUID方式可以和它跑在同一个进程中。