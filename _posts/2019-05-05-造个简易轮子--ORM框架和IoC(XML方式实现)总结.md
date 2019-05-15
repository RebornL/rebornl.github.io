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
public class DBSessionFactory {

    private DBSource dbSource;
    private Properties props;
    private DBSession dbSession;

    public DBSessionFactory() {
        //加载配置，新建DBSource
        props = new Properties();
        try {
            props.load(ClassLoader.getSystemResourceAsStream("config.properties"));

            dbSource = new DBSource(props);
            // 打开数据库连接
            Connection conn = dbSource.openConnection();
            dbSession = new DBSession(conn);
            dbSession.scanAndCreateTable(props.getProperty("package"));
//            System.out.println(props.getProperty("driver"));
        } catch (IOException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }

    }

    // 打开数据库连接
    public DBSession openSession() throws SQLException, ClassNotFoundException {
        return new DBSession(dbSource.openConnection());
    }

    /*
    * 操作数据库类
    * */
    public static class DBSession {
        private Connection conn;
        private DBSession(Connection conn) {
            this.conn = conn;
        }

         /*
        * 扫描并创建不存在的表
        * */
        public void scanAndCreateTable(String packageName) throws SQLException {
            String createTableSql = "CREATE TABLE IF NOT EXISTS `%s`(%s)ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 " +
                    "COLLATE=utf8_unicode_ci;";
            List<Class<?>> tableClasses = ORMAnnoHelper.getClasses(packageName);
            List<String> sqls = new ArrayList<>(tableClasses.size());
            for (Class<?> cls: tableClasses) {
                String sqltemp = createTableSql;
                String tableName = ORMAnnoHelper.getTableName(cls);
                StringBuilder column = new StringBuilder();
                Field[] fs = cls.getDeclaredFields();
                for (int i = 0, len = fs.length; i < len; i++) {
                    column.append("`"+ORMAnnoHelper.getColumnName(fs[i])+"` "+ORMAnnoHelper.getColumnType(fs[i]));

                    if (!ORMAnnoHelper.isNull(fs[i])) {
                        column.append(" NOT NULL");
                    }

                    if (ORMAnnoHelper.isId(fs[i])) {
                        column.append(" PRIMARY KEY");
                    }

                    if (i < len-1) {
                        column.append(", ");
                    }

                }
                sqltemp = String.format(sqltemp, tableName, column);
                System.out.println(sqltemp);
                sqls.add(sqltemp);
            }

            Statement stmt = conn.createStatement();
            for (String sql: sqls) {
                stmt.executeUpdate(sql);
            }
            stmt.close();

        }

        /*
         * 查询操作
         * */
        public <T> List<T> list(Class<T> cls) throws IllegalAccessException, InstantiationException, NoSuchMethodException, InvocationTargetException, SQLException {
            // 获取SQL语句
            String sql = "select %s from %s";
            StringBuilder columns = new StringBuilder();
            Field[] fs = cls.getDeclaredFields();
            for (int i = 0, len = fs.length; i < len; i++) {
                columns.append(ORMAnnoHelper.getColumnName(fs[i]));
                if (i!=len-1) {
                    columns.append(",");
                }
            }

            sql = String.format(sql, columns.toString(), ORMAnnoHelper.getTableName(cls));

            // 创建执行SQL语句对象
            Statement statement = conn.createStatement();
//            PreparedStatement preparedStatement = conn.prepareStatement(sql);
//            preparedStatement.s
            ResultSet rs = statement.executeQuery(sql);

            List<T> list = listResultHandler(cls, rs);

            statement.close();

            return list;
        }

        private <T> List<T> listResultHandler(Class<T> cls, ResultSet rs) throws IllegalAccessException,
                InstantiationException, SQLException, NoSuchMethodException, InvocationTargetException {
            List<T> list = new ArrayList<>();
            T obj = null;
            Field[] fs = cls.getDeclaredFields();
            while (rs.next()) {
                obj = getObjFromResultSet(cls, fs, rs);
                list.add(obj);
            }

            return list;
        }

        private <T> T getObjFromResultSet(Class<T> cls, Field[] fs, ResultSet rs) throws IllegalAccessException,
                InstantiationException, SQLException, NoSuchMethodException, InvocationTargetException {
            T obj = cls.getDeclaredConstructor().newInstance();
            for (Field field: fs) {
                field.setAccessible(true);
                Class<?> type = field.getType();
                if (String.class == type) {
                    field.set(obj, rs.getString(ORMAnnoHelper.getColumnName(field)));
                } else if (int.class == type || Integer.class == type) {
                    field.set(obj, rs.getInt(ORMAnnoHelper.getColumnName(field)));
                } else if (double.class == type || Double.class == type) {
                    field.set(obj, rs.getDouble(ORMAnnoHelper.getColumnName(field)));
                } else if (long.class == type || Long.class == type) {
                    field.set(obj, rs.getLong(ORMAnnoHelper.getColumnName(field)));
                } else if (Date.class == type) {
                    field.set(obj, rs.getDate(ORMAnnoHelper.getColumnName(field)));
                }
            }
            return obj;
        }

        private <T> T oneResultHandler(Class<T> cls, ResultSet rs) throws IllegalAccessException,
                InstantiationException, SQLException, NoSuchMethodException, InvocationTargetException {

            T obj = null;
            Field[] fs = cls.getDeclaredFields();
            while (rs.next()) {
                obj = getObjFromResultSet(cls, fs, rs);

            }

            return obj;
        }

        /*
         * 插入操作
         * */
        public int insert(Object obj) throws SQLException, IllegalAccessException {
            String sql = "insert %s(%s) values(%s)";
            StringBuilder columns = new StringBuilder();
            StringBuilder params = new StringBuilder();

            Field[] fs = obj.getClass().getDeclaredFields();
            List<Field> insertFields = new ArrayList<>();
            for (int i = 0, len = fs.length; i < len; i++) {
                columns.append(ORMAnnoHelper.getColumnName(fs[i]));
                params.append("?");

                if (i != len-1) {
                    columns.append(",");
                    params.append(",");
                }

                insertFields.add(fs[i]);

            }

            sql = String.format(sql, ORMAnnoHelper.getTableName(obj.getClass()), columns.toString(), params.toString());

            System.out.println(sql);
            // 预处理的SQL语句
            PreparedStatement preparedStatement = conn.prepareStatement(sql);
            paramHandler(insertFields, obj, preparedStatement);

            int row = preparedStatement.executeUpdate();

            preparedStatement.close();
            return row;
        }

        /*
        * 更新操作
        * */
        public int update(Object obj) throws IllegalAccessException, SQLException {
            // update: update tableName set %s=%s... where id = %s;
            List<Field> updateFields = new ArrayList<>();
            String sql = "update %s set %s where %s";
            StringBuilder columns = new StringBuilder();
            StringBuilder id = new StringBuilder();
            Field[] fs = obj.getClass().getDeclaredFields();
            for (int i = 0, len = fs.length; i < len; i++) {
                fs[i].setAccessible(true);
                if (ORMAnnoHelper.isId(fs[i])) {
                    id.append(ORMAnnoHelper.getColumnName(fs[i]));
                    id.append("=");
                    if (String.class == fs[i].getType()) {
                        id.append("'"+String.valueOf(fs[i].get(obj))+"'");
                    } else {
                        id.append(fs[i].get(obj));

                    }
                }
                columns.append(ORMAnnoHelper.getColumnName(fs[i]));
                columns.append("=?");
                if (i < len-1) {
                    columns.append(",");
                }
                // 添加更新的字段到集合中
                updateFields.add(fs[i]);

            }

            sql = String.format(sql, ORMAnnoHelper.getTableName(obj.getClass()), columns.toString(), id.toString());
            System.out.println(sql);

            PreparedStatement preparedStatement = conn.prepareStatement(sql);
            paramHandler(updateFields, obj, preparedStatement);


            int row = preparedStatement.executeUpdate();
            preparedStatement.close();

            return 0;
        }

        private void paramHandler(List<Field> fields, Object obj, PreparedStatement preparedStatement) throws IllegalAccessException, SQLException {
            Field field = null;
            Class<?> type = null;
            for (int i = 0, len = fields.size(); i < len; i++) {
                field = fields.get(i);
                field.setAccessible(true);
                type = field.getType();
                if (String.class == type) {
                    preparedStatement.setString(i+1, String.valueOf(field.get(obj)));
                } else if (int.class == type || Integer.class == type) {
                    preparedStatement.setInt(i+1, field.getInt(obj));
                } else if (double.class == type || Double.class == type) {
                    preparedStatement.setDouble(i+1, field.getDouble(obj));
                } else if (long.class == type || Long.class == type) {
                    preparedStatement.setLong(i+1, field.getLong(obj));
                } else if (Date.class == type) {
                    Date date = (Date)field.get(obj);
                    preparedStatement.setDate(i++, new java.sql.Date(date.getTime()));
                }
            }
        }

        public <T> T getById(Class<T> cls, Object id) throws SQLException, InstantiationException, IllegalAccessException, NoSuchMethodException, InvocationTargetException {
            Field idField = ORMAnnoHelper.findIdField(cls);
            String where = idField.getName()+"=";
            if (String.class == idField.getType()) {
                where += "'"+id+"'";
            } else {
                where += id;
            }
            String sql = String.format("select * from %s where %s", ORMAnnoHelper.getTableName(cls),
                    where);
            System.out.println(sql);

            Statement statement = conn.createStatement();
            ResultSet rs = statement.executeQuery(sql);

            T result = oneResultHandler(cls, rs);

            return result;
        }

        /*
        * 删除操作
        * */
        public int delete(Class<?> cls, Object id) throws SQLException {
            Field idField = ORMAnnoHelper.findIdField(cls);
            String where = ORMAnnoHelper.getColumnName(idField)+"=";
            if (String.class == id.getClass()) {
                where += ("'"+id+"'");
            } else {
                where += id;
            }
            String sql = "delete from %s where %s";
            sql = String.format(sql, ORMAnnoHelper.getTableName(cls), where);
            System.out.println(sql);
            Statement statement = conn.createStatement();
            int row = statement.executeUpdate(sql);
            return row;
//            return 0;
        }

        public void close() {
            if (null != conn) {
                try {
                    conn.close();
                } catch (SQLException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```



## IoC实现（XML方式）

IoC实现可以采用跟ORM一样注解方式实现，这里采用XML方式，类似Spring那种通过XML定义Bean。

![](https://raw.githubusercontent.com/RebornL/picbed/master/20190515132139.png)

另外还需要在resources中定义dtd和beans.xml

### 实现具体

#### DTD定义和使用

factory.dtd文件

```xml-dtd
<?xml version="1.0" encoding="UTF-8" ?>
<!--这个表示beans节点可以包含很多bean-->
<!ELEMENT beans (bean*) >
<!--这个表示bean节点里面可以包含很多property-->
<!ELEMENT bean (property*)>
<!--定义bean节点中属性值-->
<!ATTLIST bean
        id ID #REQUIRED
        class CDATA #REQUIRED
        scope (singleton | prototype | session| request) "singleton">
<!ELEMENT property (#PCDATA)>
<!ATTLIST property
        name CDATA #REQUIRED
        value CDATA #REQUIRED
        type (string|int|long|double|float) #IMPLIED>
```

beans.xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!--本地通过绝对路径引入-->
<!DOCTYPE beans SYSTEM "D:\project\tingmvc\src\main\resources\factory.dtd">

<beans>
    <bean id="test" class="com.tinymvc.xml.test.User">
        <property name="id" value="10000" type="long"/>
        <property name="name" value="test"/>
        <property name="phone" value="1231231"/>
    </bean>

    <bean id="ordertest" class="com.tinymvc.xml.Order">
        <property name="" value=""/>
    </bean>
</beans>
```

#### Bean和Property对应实现

```java
public class BeanInfo {

    private String id;
    private String clsName;//对应class属性
    private String scope;

    private List<PropsInfo> props;
    // 构造器和getter，setter方法省略
}

public class PropsInfo {

    private String name;
    private String value;
    private String type;

    // 构造器和getter，setter方法省略
}
```

#### 解析XML和构造Bean

主要采用SAXParser解析XML，很方便（qName为标签名，attributes为属性值）

```java
public class FactoryBuilder {

    private HashMap<String, BeanInfo> beanMap;

    public FactoryBuilder(String xmlPath) {
        try {
            File file = new File(xmlPath);
            if (!file.exists()) {
                throw new FileNotFoundException("文件不存在，请检查xml的路径");
            }

            //1.SAXParser解析器工厂对象
            SAXParserFactory factory = SAXParserFactory.newInstance();
            //2.创建解析器对象
            SAXParser parser = factory.newSAXParser();
            //3.开始解析XML文件
            parser.parse(new FileInputStream(xmlPath), new DefaultHandler() {
                private BeanInfo beanInfo;

                @Override
                public void startDocument() throws SAXException {
                    //文档开始
                    beanMap = new HashMap<>();
                }

                @Override
                public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
                    //标签开始
                    if ("bean".equals(qName)) {
                        beanInfo = new BeanInfo();
                        beanInfo.setId(attributes.getValue("id"));
                        beanInfo.setClsName(attributes.getValue("class"));
                        beanInfo.setScope(attributes.getValue("scope"));

                        beanInfo.setProps(new ArrayList<PropsInfo>());
                    } else if("property".equals(qName)) {
                        beanInfo.getProps().add(new PropsInfo(
                                attributes.getValue("name"),
                                attributes.getValue("value"),
                                attributes.getValue("type")
                        ));
                    }
                }

                @Override
                public void endElement(String uri, String localName, String qName) throws SAXException {
                    //标签结束
                    if ("bean".equals(qName)) {
                        beanMap.put(beanInfo.getId(), beanInfo);
                        beanInfo = null;
                    }
                }
            });
        } catch (SAXException e) {
            e.printStackTrace();
        } catch (ParserConfigurationException e) {
            e.printStackTrace();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    public Factory newFactory() {
        return new Factory();
    }
    /*
    * 这个factory使用获取HashMap中Java对象
    * */
    public class Factory {
        private Factory(){}

        /*根据id获取Bean类对象*/
        public Object getBeanById(String id) {
            BeanInfo beanInfo = beanMap.get(id);
            if (beanInfo == null) return null;
            Object obj = null;
            //通过反射实例化Bean标签的class属性指定的类对象
            try {
                Class cls = Class.forName(beanInfo.getClsName());
                obj = cls.getDeclaredConstructor().newInstance();
                for (PropsInfo pi: beanInfo.getProps()) {
                    Field field = cls.getDeclaredField(pi.getName());
                    field.setAccessible(true);//设置可访问性
                    //向字段注入属性值
                    if ("long".equals(pi.getType())) {
                        field.set(obj, Long.parseLong(pi.getValue()));
                    } else if ("int".equals(pi.getType())) {
                        field.set(obj, Integer.parseInt(pi.getValue()));
                    } else if ("double".equals(pi.getType())) {
                        field.set(obj, Double.parseDouble(pi.getValue()));
                    } else if ("float".equals(pi.getType())) {
                        field.set(obj, Float.parseFloat(pi.getValue()));
                    } else {
                        field.set(obj, pi.getValue());
                    }
                }
            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            } catch (IllegalAccessException e) {
                e.printStackTrace();
            } catch (InstantiationException e) {
                e.printStackTrace();
            } catch (NoSuchFieldException e) {
                e.printStackTrace();
            } catch (NoSuchMethodException e) {
                e.printStackTrace();
            } catch (InvocationTargetException e) {
                e.printStackTrace();
            }

            return obj;
        }
    }
}
```









