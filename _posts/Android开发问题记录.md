



## 1. 启动项目时，遇到gradle build失败，提示：dl.google.com:443无响应？

按照网上说，打开settings勾选embed maven repository，对我并不成功。后来发现，关闭Android studio和模拟器，重启Android studio，看proxy是否设置正确，重新新建项目，然后重新运行app即可。



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

