



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



5-29：突然发现一个靠谱的方法，首先定义权限，然后请求权限，使用的时候按照Android8新的写法去实现。参考文章，可以看[这篇](<https://codeday.me/bug/20171207/104059.html>)看怎么定义和使用权限。



## 5. AndroidStudio3.4使用Genymotion模拟器

自带模拟器真的卡的我非常难受，虽然说是改进了很多，但是给我的体验还是跟好几年的体验的啊，用完内存，卡顿感非常明显。总结网上的步骤，亲测可用：

1. 下载genymotion，自行注册和下载，选择带virtualbox版本（主要是省事）
2. 安装genymotion，自行安装设备，在setting中设置ADB，选择`Use custom Android SDK tools`

![](https://raw.githubusercontent.com/RebornL/picbed/master/20190529164655.png)

3. 在AndroidStudio中打开plugin，搜索`genymotion`插件安装，安装完成后，在settings中设置genymotion的路径。

![](https://raw.githubusercontent.com/RebornL/picbed/master/20190529165216.png)

![](https://raw.githubusercontent.com/RebornL/picbed/master/20190529201433.png)

4. 点击AndroidStudio中View菜单，勾选toolbars之后，就能看到genymotion管理按钮，点开之后，选择你的模拟器，点击start按钮即可

![](https://raw.githubusercontent.com/RebornL/picbed/master/20190529165352.png)

![](https://raw.githubusercontent.com/RebornL/picbed/master/20190529165450.png)

5. 最后，点击运行按钮，选择genymotion的设备即可，使用起来跟自带的一样，可以看到logcat消息，但是运行更快，电脑也不卡了。

![](https://raw.githubusercontent.com/RebornL/picbed/master/20190529165715.png)





















