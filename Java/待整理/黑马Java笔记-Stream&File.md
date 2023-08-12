# 黑马Java笔记-Stream&File

## Stream流

### 1.1 Stream

- 直接阅读代码的字面意思即可完美展示无关逻辑方式的语义。
- Stream流把真正的函数式编程风格引入到Java中，代码简洁。
- 三类方法：获取Stream流、中间方法、终结方法。

- 生成方式
  - Collection体系集合使用默认方法stream()生成流， default Stream<E> stream()

  - Map体系集合把Map转成Set集合，间接的生成流

  - 数组通过Arrays中的静态方法stream生成流

  - 同种数据类型的多个数据通过Stream接口的静态方法of(T... values)生成流

- 代码演示

```java
//Collection体系的集合可以使用默认方法stream()生成流
List<String> list = new ArrayList<String>();
Stream<String> listStream = list.stream();
Set<String> set = new HashSet<String>();
Stream<String> setStream = set.stream();
//Map体系的集合间接的生成流
Map<String,Integer> map = new HashMap<String, Integer>();
Stream<String> keyStream = map.keySet().stream();
Stream<Integer> valueStream = map.values().stream();
Stream<Map.Entry<String, Integer>> entryStream = map.entrySet().stream();
//数组可以通过Arrays中的静态方法stream生成流
String[] strArray = {"hello","world","java"};
Stream<String> strArrayStream = Arrays.stream(strArray);
//同种数据类型的多个数据可以通过Stream接口的静态方法of(T... values)生成流
Stream<String> strArrayStream2 = Stream.of("hello", "world", "java");
Stream<Integer> intStream = Stream.of(10, 20, 30);
```

### 1.2 中间操作方法

```java
Stream<T> filter(Predicate predicate)	用于对流中的数据进行过滤
Stream<T> limit(long maxSize)	返回此流中的元素组成的流，截取前指定参数个数的数据
Stream<T> skip(long n)			跳过指定参数个数的数据，返回由该流的剩余元素组成的流
static <T> Stream<T> concat(Stream a, Stream b)		合并a和b两个流为一个流
Stream<T> distinct()	   返回由该流的不同元素（根据Object.equals(Object) ）组成的流
```

### 1.3 终结操作方法

```java
void forEach(Consumer action)			对此流的每个元素执行操作
long count()							返回此流中的元素数
```

### 1.4 Stream流的收集操作

```java
R collect(Collector collector)			把结果收集到集合中
    
public static <T> Collector toList()	把元素收集到List集合中
public static <T> Collector toSet()		把元素收集到Set集合中
public static Collector toMap(Function keyMapper,Function valueMapper)	把元素收集到Map集合中
```

## File类

### 1.1 概述与使用

- File类是文件和目录路径名的抽象表示
- 文件和目录是可以通过File封装成对象的
- 对于File而言,其封装的并不是一个真正存在的文件,仅仅是一个路径名而已.它可以是存在的,也可以是不存在的.将来是要通过具体的操作把这个路径的内容转换为具体存在的
- 绝对路径和相对路径
  - 绝对路径，完整的路径，从盘符开始
  - 相对路径，简化的路径，相对于当前项目根目录的路径
- 构造方法

```java
File(String pathname)	通过将给定的路径名字符串转换为抽象路径名来创建新的 File实例
File(String parent, String child)	从父路径名字符串和子路径名字符串创建新的 File实例
File(File parent, String child)	从父抽象路径名和子路径名字符串创建新的 File实例
```

### 1.2 File类创建与删除功能

```java
public boolean createNewFile() 	当具有该名称的文件不存在时，创建一个新空文件
public boolean mkdir()			创建由此抽象路径名命名的目录
public boolean mkdirs()			创建由此抽象路径名命名的目录，包括任何必需但不存在的父目录
public boolean delete()			删除由此抽象路径名表示的文件或目录
```

### 1.3 File类判断和获取功能

```java
public boolean isDirectory()	测试此抽象路径名表示的File是否为目录
public boolean isFile()	测试此抽象路径名表示的File是否为文件
public boolean exists()	测试此抽象路径名表示的File是否存在

public String getAbsolutePath()	返回此抽象路径名的绝对路径名字符串
public String getPath()	将此抽象路径名转换为路径名字符串
public String getName()	返回由此抽象路径名表示的文件或目录的名称
public File[] listFiles()	返回此抽象路径名表示的目录中的文件和目录的File对象数组
```
