



## 1. 启动项目时，遇到gradle build失败，提示：dl.google.com:443无响应？

按照网上说，打开settings勾选embed maven repository，对我并不成功。后来发现，关闭Android studio和模拟器，重启Android studio，看proxy是否设置正确，重新新建项目，然后重新运行app即可。



2019-05-27：发现AS有时候设置了proxy（v2ray开全局，1080），依旧是没响应。后来换Brook，设置2080端口，很顺利。不知道是VPN的原因还是端口。



## 2. 遇到`java.lang.IllegalStateException: You need to use a Theme.AppCompat theme (or descendant) with this activity.`这样的错误

在app/src/main/AndroidManifest.xml中，我对注册的activity加了`android:theme`这个属性，如下所示：

```xml
<activity  android:name=".DialogActivity"
           android:theme="@android:style/Theme.Dialog"/>
```

但是对应的`DialogActivity`定义由于继承`AppCompatActivity`导致的错误。所以解决办法有两个：

1. 修改`DialogActivity`继承的类为`Activity`即可
2. 修改`android:theme`这个属性为`@style/Theme.AppCompat.Dialog`即可

```xml
<activity  android:name=".DialogActivity"
           android:theme="@style/Theme.AppCompat.Dialog"/>
```

## 3. AndroidStudio3.4遇到`Caused by: java.lang.IllegalArgumentException: Unexpected type tag 67 found.`

解决办法：重启Android Studio。（只想说重启大法真香）



## 4. Android8以上的广播失效的问题？`Background execution not allowed: receiving Intent`

因为我看的《第一行代码》第二版用的最高安卓版本是Android7，内容有点旧。广播的写法有所改变，是因为Android8以上对广播进行改造有所限制造成。