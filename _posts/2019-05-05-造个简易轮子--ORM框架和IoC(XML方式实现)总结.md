---
layout:     post
title:      造个简易轮子--ORM框架和IoC(XML方式实现)总结
subtitle:   反射，注解，DTD定义，XML解析
date:       2019-05-05
update:     2019-05-14
author:     Reborn
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Java
    - 框架实现
---

## 前言

五一假期，终于学完科三，在5号考完科三和科四，顺利拿到驾照。这是这段时间最开心的事了![](https://raw.githubusercontent.com/RebornL/picbed/master/9DBB76BE0A98F5E2922EE3C04EC64156.png)。这几天争取整理这段时间学到的和实现的一些简易轮子，然后就去跟着实验室研三师兄毕业旅行放松一下。



## 基本模式

查阅了相关的资料和博客之后，实现ORM框架和IoC都有一个基本的套路，如下图所示。ORM框架中定义`@Table`,`@Column`注解分别表示表和列属性。IoC中则通过文档类型定义（DTD）定义`beans`,`bean`,`property`等xml节点表示一个JavaBean。

![](https://raw.githubusercontent.com/RebornL/picbed/master/20190514212259.png)

（参考资料：[DTD简介](<http://www.w3school.com.cn/dtd/dtd_intro.asp>)）

## ORM框架实现

该ORM框架的实现功能有：

- 通过properties文件配置数据库

- `@Table`注解表明一个表，其中value值为数据库对应的表名
- `@Column`注解定义列属性，value为列属性名，isNull表明是否为空，isId表明是否为主键，type表明数据类型
- 通过扫描bean包，自动创建不存在的表
- 实现基本CURD功能

idea中定义的包结构

![](https://raw.githubusercontent.com/RebornL/picbed/master/20190514220424.png)

类之间的关系

![](https://raw.githubusercontent.com/RebornL/picbed/master/20190514215000.png)

### 具体实现

#### 注解

`@Table`只定义一个默认的value属性表示表明，当没有用在Bean上时，保持默认时，即value属性值为空时，以类型小写作为表名，否则以value作为表名。

`@Column`注解中value表示列属性名，isNull表示是否为空，isId表示是否为主键，type表示数据类型

```java
@Target({ElementType.TYPE})//使用的位置
@Retention(RetentionPolicy.RUNTIME)//有效性，运行时
public @interface Table {
    String value();//设置属性值，类名和表名一致时，不用写value=，直接默认即可
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
public @interface Column {
    String value() default "";
    boolean isNull() default false;
    boolean isId() default false;
    String type() default "varchar(100)";//varchar要指定位数，否则MySQL会报错
}
```

#### 数据库连接

在properties中定义数据库连接属性和Bean的包名

#### 注解解析

`ORMAnnoHelper`时















