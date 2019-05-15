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

在properties中定义数据库连接属性和Bean的包名，DBSource会自动获取

```properties
driver=com.mysql.jdbc.Driver
url=jdbc:mysql://localhost:3306/orm_test?useUnicode=true&characterEncoding=utf8&serverTimezone=GMT
username=root
password=root123
# bean的包名
package=com.tinymvc.orm.bean
```

DBSource的实现如下，主要用来**连接数据库**，最后**返回数据库连接**

```java
public class DBSource {
    private String driver;
    private String url;
    private String username;
    private String password;

    public DBSource() {}

    public DBSource(Properties props) {
        this.driver = props.getProperty("driver");
        this.url = props.getProperty("url");
        this.username = props.getProperty("username");
        this.password = props.getProperty("password");
    }

    public Connection openConnection() throws ClassNotFoundException, SQLException {
//        Class.forName(driver);//新版驱动都不需要
        return DriverManager.getConnection(url, username, password);
    }
    
    //getter，setter方法省略
}
```



#### 注解解析

`ORMAnnoHelper`是用来解析使用了注解的类，主要是通过**[反射](<https://www.sczyh30.com/posts/Java/java-reflection-1/>)**实现的，其中的`getAnnotation(Class)`获取对应的注解是什么

```java
public class ORMAnnoHelper {

    /*
    * 获取表名
    * */
    public static String getTableName(Class<?> beanCls) {
        Table table = beanCls.getAnnotation(Table.class);
        if (null == table) {
            //类的简称即为对应的表名
            return beanCls.getSimpleName().toLowerCase();
        } else {
            return table.value();
        }
    }

    /*
     * 获取列属性名字
     * */
    public static String getColumnName(Field field) {
        Column column = field.getAnnotation(Column.class);
        if (null == column) {
            return field.getName().toLowerCase();
        } else {
            return column.value();
        }
    }

    /*
     * 获取列属性的数据类型
     * */
    public static String getColumnType(Field field) {
        Column column = field.getAnnotation(Column.class);
        if (null == column) {
            return "varchar";
        } else {
            return column.type();
        }
    }

    /*
     * 判断列属性是否可以为空
     * */
    public static boolean isNull(Field field) {
        Column column = field.getAnnotation(Column.class);
        if (null == column) {
            return false;
        }
        return column.isNull();
    }

    /*
     * 获取为主键的Field
     * */
    public static Field findIdField(Class<?> cls) {
        for (Field f: cls.getDeclaredFields()) {
            if (isId(f)) {
                return f;
            }
        }
        return null;
    }

    /*
     * 判断Field是否为主键
     * */
    public static boolean isId(Field field) {
        Column columnAnno = field.getAnnotation(Column.class);
        if (null != columnAnno) {
            return columnAnno.isId();
        }
        return false;
    }

    /*
     * 获取包路径下所有类名
     * */
    public static List<Class<?>> getClasses(String packageName) {
        List<Class<?>> classes = new ArrayList<>();
        boolean recursive = true;
        String packageDirName = packageName.replace('.', '/');
        Enumeration<URL> dirs = null;
        try {
            dirs = Thread.currentThread().getContextClassLoader().getResources(packageDirName);
            while (dirs.hasMoreElements()) {
                URL url = dirs.nextElement();
                String protocol = url.getProtocol();
                if ("file".equals(protocol)) {
                    String filePath = URLDecoder.decode(url.getFile(), "UTF-8");
                    findAndAddClassesInPackageByFile(packageName, filePath, recursive, classes);
                }
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
        return classes;
    }

    /*
     * 通过文件路径查找所有类名
     * */
    private static void findAndAddClassesInPackageByFile(String packageName, String filePath, boolean recursive, List<Class<?>> classes) {
        File dir = new File(filePath);
        if (!dir.exists() || !dir.isDirectory()) {
            return;
        }

        File[] dirFiles = dir.listFiles(new FileFilter() {

            @Override
            public boolean accept(File file) {
                return (recursive && file.isDirectory()) || (file.getName().endsWith(".class"));
            }
        });

        for (File file : dirFiles) {
            if (file.isDirectory()) {
                findAndAddClassesInPackageByFile(packageName+"."+file.getName(), file.getAbsolutePath(), recursive,
                        classes);
            } else {
                String className = file.getName().substring(0, file.getName().length()-6);
                try {
                    classes.add(Thread.currentThread().getContextClassLoader().loadClass(packageName+"."+className));
                } catch (ClassNotFoundException e) {
                    e.printStackTrace();
                }
            }
        }

    }
}
```

#### 数据库操作工厂类

`DBSessionFactory`主要用来加载数据库配置文件和返回数据库操作类。`DBSession`定义在`DBSessionFactory`中，封装一系列的数据库CURD的操作。

```java

```















