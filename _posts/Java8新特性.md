## 允许接口有默认方法实现

新增`default`关键字，为接口声明非抽象方法，这样实现接口，不必实现所有抽象方法。

```java
interface Formula {
    double calculate(int a);
    
    default double sqrt(int a) {
        return Math.sqrt(a);
    }
}

Formula formula = new Formula() {
    @Override
    public double calculate(int a) {
        return sqrt(a*100);
    }
}

formula.calculate(100);//100.0
formula.sqrt(16);//4.0
```



## Lambda表达式

函数式表达式

```java
List<String> name = Arrays.asList("peter","anna","mike", "xenia");
//常规写法
Collections.sort(name, new Comparator<String>() {
    @Override
    public int compare(String o1, String o2) {
        return o2.compareTo(o1);
    }
});

//Lambda表达式
Collections.sort(name, (o1, o2)->o2.compareTo(o1));
//Collections.sort(name, Comparator.reverseOrder());
```

### 函数式接口

`@FunctionalInterface`标记的接口均为函数式接口，可以用lambda表达式实现

```java
@FunctionalInterface
interface Converter<F, T> {
    T convert(F form);
}

Converter<String, String> converter = (from)->Integer.valueOf(from);
Integer converted = converter.convert("123");
```

### 方法和构造函数的引用

对于方法和构造函数，可以用`::`来调用

```java
Converter<String, Integer> converter1 = Integer::valueOf;
System.out.println(converter1.convert("2123"));
```



### lambda的范围

- 访问lambda表达式外部的final局部变量

- 访问成员变量和静态变量

- 无法访问默认的接口方法

  ```java
  Formula formula = (a)-> sqrt(a*100);//无法编译
  ```

### 内置函数式接口

- Predicate：布尔类型的函数，用于处理复杂的逻辑动词
- Function：可以将多个函数串在一起
- Suppliers：产生一个给定类型的结果
- Consumer：代表在一个输入参数上需要进行的操作
- Comparator：用于比较
- Optional：是一个精巧的工具接口，用来防止NullPointerException产生

> 具体使用参考这个连接：<https://wizardforcel.gitbooks.io/modern-java/content/ch1.html>



## Stream

Stream表示某一种元素的序列，在这些元素上进行各种操作

### 操作

#### Filter

Filter接收一个Predicate接口类型的变量，并将所有流对象中元素进行过滤。该操作属于中间操作，会返回流，允许继续操作。

#### Sorted

Sorted同样也是中间操作，能够返回一个排过序的流对象的视图。流对象中的元素会默认按照自然顺序进行排序，可以通过指定Comparator改变排序顺序。

#### Map

属于中间操作，通过给定方法将流中每个元素转换到对应的另一个对象上。

#### Match

匹配操作有多种不同的类型，用来判断某一种规则是否与流中对象相互吻合。属于终结操作，只返回一个boolean类型的结果。

#### Count

属于终结操作，作用返回一个数值，表示当前流中对象的包含的元素数量。

#### Reduce

属于终结操作，通过特定方法对元素进行削减操作，将结果存放到Optional变量里



### Parallel Streams

```java
public static void main(String[] args) {

    int max = 1000000;
    List<String> values = new ArrayList<>(max);
    for (int i = 0; i < max; i++) {
        UUID uuid = UUID.randomUUID();
        values.add(uuid.toString());
    }
    //顺序执行
    long t0 = System.nanoTime();
    long count = values.stream().sorted().count();
    System.out.println(count);
    long t1 = System.nanoTime();
    long millis = TimeUnit.NANOSECONDS.toMillis(t1-t0);
    System.out.println(String.format("sequential sort took: %d ms", millis));//571ms

    long t2 = System.nanoTime();
    long count2 = values.parallelStream().sorted().count();
    System.out.println(count2);
    long t3 = System.nanoTime();
    long millis2 = TimeUnit.NANOSECONDS.toMillis(t3-t2);
    System.out.println(String.format("Parallel sort took: %d ms", millis2));//253ms
}
```

并行排序时间要比串行排序时间快一半。





## Map新操作

- putIfAbsent：添加元素
- computeIfPresent
- computeIfAbsent
- merge
- remove
- getOrDefault

```java
Map<Integer, String> map = new HashMap<>();
for (int i = 0; i < 10; i++) {
    map.putIfAbsent(i, "val"+i);
}

map.forEach((id, val) -> System.out.println(val));

map.computeIfPresent(3, (num, val)->val+num);
System.out.println(map.get(3));// val33

map.remove(3, "val33");
System.out.println(map.get(3));

map.merge(9, "concat", (value, newValue)->value.concat(newValue));
System.out.println(map.get(9));

map.getOrDefault(42, "not found");  // not found

map.remove(3, "val3");
map.get(3);             // val33

map.remove(3, "val33");
map.get(3);             // null
```

