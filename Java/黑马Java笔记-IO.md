# IO

## 概述

- IO流介绍
  - IO：输入/输出(Input/Output)
  - 流：是一种抽象概念,是对数据传输的总称.也就是说数据在设备间的传输称为流,流的本质是数据传输
  - IO流就是用来处理设备间数据传输问题的.常见的应用: 文件复制; 文件上传; 文件下载
- IO流的分类
  - 按照数据类型来分
    - 字节流
    - 字符流
  - 按照数据的流向
    - 输入流：读数据
    - 输出流：写数据

- IO流的使用场景
  - 如果操作的是纯文本文件,优先使用字符流
  - 如果操作的是图片、视频、音频等二进制文件，优先使用字节流
  - 如果不确定文件类型,优先使用字节流，字节流是万能的流

## 字节流

### 1.1 字节流写数据

- 字节流抽象基类

  - InputStream：这个抽象类是表示字节输入流的所有类的超类
  - OutputStream：这个抽象类是表示字节输出流的所有类的超类
  - 子类名特点：子类名称都是以其父类名作为子类名的后缀
- 字节输出流

  - FileOutputStream(String name)：创建文件输出流以指定的名称写入文件

```java
public class FileOutputStreamDemo03 {
    public static void main(String[] args) throws IOException {
		FileOutputStream fos = new FileOutputStream("myByteStream\\fos.txt");
		fos.write(97);
		fos.close();
    }
}
```

### 1.2 字节流写数据的三种方式

```java
void   write(int b)    将指定的字节写入此文件输出流,一次写一个字节数据
void   write(byte[] b) 将b.length字节从指定的数组写入此文件输出流,一次写一个字节数组数据
void   write(byte[] b, int off, int len)
将len字节从指定的数组偏移off开始写入此文件输出流,一次写一个字节数组的部分数据
```

### 1.3 字节流写数据实现换行和追加写入

- 换行

  - windows:\r\n
  - linux:\n
  - mac:\r

- 追加写入

  - public FileOutputStream(String name,boolean append)
  - 第二个参数为true ，则字节将写入文件的末尾而不是开头

### 1.4 字节流读写数据加异常处理

```java
try{
	可能出现异常的代码;
}catch(异常类名 变量名){
	异常的处理代码;
}finally{
	执行所有清除操作;
    被finally控制的语句一定会执行，除非JVM退出;
}
```

### 1.5 字节流读数据(一次读一个字节数据)

FileInputStream(String name)：通过打开与实际文件的连接来创建一个FileInputStream,该文件由文件系统中的路径名name命名

```java
FileInputStream fis = new FileInputStream("myByteStream\\fos.txt");
int by;
while ((by=fis.read())!=-1) {
    System.out.print((char)by);
}
fis.close();
```

### 1.6 字节流复制文件

```java
FileInputStream fis = new FileInputStream("E:\\itcast\\窗里窗外.txt");
FileOutputStream fos = new FileOutputStream("myByteStream\\窗里窗外.txt");
int by;
while ((by=fis.read())!=-1) {
    fos.write(by);
}
fos.close();
fis.close();
```

### 1.7 字节流读数据(一次读一个字节数组数据)

- 一次读一个字节数组的方法

  - public int read(byte[] b)：从输入流读取最多b.length个字节的数据
  - 返回的是读入缓冲区的总字节数,也就是实际的读取字节个数
- 示例代码

```java
FileInputStream fis = new FileInputStream("myByteStream\\fos.txt");
byte[] bys = new byte[1024]; //1024及其整数倍
int len;
while ((len=fis.read(bys))!=-1) {
    System.out.print(new String(bys,0,len));
}
fis.close();
```

### 1.8 字节流复制文件

```java
FileInputStream fis = new FileInputStream("E:\\itcast\\mn.jpg");
FileOutputStream fos = new FileOutputStream("myByteStream\\mn.jpg");
byte[] bys = new byte[1024];
int len;
while ((len=fis.read(bys))!=-1) {
    fos.write(bys,0,len);
}
fos.close();
fis.close();
```

## 字节缓冲流

### 1.1 字节缓冲流构造方法

- 字节缓冲流介绍

  - BufferOutputStream：该类实现缓冲输出流。通过设置这样的输出流，应用程序可以向底层输出流写入字节，而不必为写入的每个字节导致底层系统的调用
  - BufferedInputStream：创建BufferedInputStream将创建一个内部缓冲区数组。当从流中读取或跳过字节时，内部缓冲区将根据需要从所包含的输入流中重新填充，一次很多字节
- 构造方法

```java
BufferedOutputStream(OutputStream out)         	创建字节缓冲输出流对象
BufferedInputStream(InputStream in)          	创建字节缓冲输入流对象
```

### 1.2 字节缓冲流复制视频

```java
//字节缓冲流一次读写一个字节
BufferedInputStream bis = new BufferedInputStream(new FileInputStream("E:\\itcast\\字节流复制图片.mp4"));
BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("myByteStream\\字节流复制图片.avi"));
int by;
while ((by=bis.read())!=-1) {
    bos.write(by);
}
bos.close();
bis.close();

//字节缓冲流一次读写一个字节数组
BufferedInputStream bis = new BufferedInputStream(new FileInputStream("E:\\itcast\\字节流复制图片.avi"));
BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("myByteStream\\字节流复制图片.avi"));
byte[] bys = new byte[1024];
int len;
while ((len=bis.read(bys))!=-1) {
    bos.write(bys,0,len);
}
bos.close();
bis.close();
```

## 字符流

### 1.1 为什么会出现字符流

- 字符流的介绍：由于字节流操作中文不是特别的方便，所以Java就提供字符流，字符流 = 字节流 + 编码表

- 中文的字节存储方式：用字节流复制文本文件时，文本文件也会有中文，但是没有问题，原因是最终底层操作会自动进行字节拼接成中文，汉字在存储的时候，无论选择哪种编码存储，第一个字节都是负数


### 1.2 编码表

- 字符集是一个系统支持的所有字符的集合，包括各国家文字、标点符号、图形符号、数字等。计算机要准确的存储和识别各种字符集符号，就需要进行字符编码，一套字符集必然至少有一套字符编码。常见字符集有ASCII字符集、GBXXX字符集、Unicode字符集等

- ASCII字符集，是基于拉丁字母的一套电脑编码系统，用于显示现代英语，主要包括控制字符(回车键、退格、换行键等)和可显示字符(英文大小写字符、阿拉伯数字和西文符号) 

  基本的ASCII字符集，使用7位表示一个字符，共128字符。ASCII的扩展字符集使用8位表示一个字符，共256字符，方便支持欧洲常用字符。是一个系统支持的所有字符的集合，包括各国家文字、标点符号、图形符号、数字等

- GBXXX字符集，GBK：最常用的中文码表。是在GB2312标准基础上的扩展规范，使用了双字节编码方案，共收录了21003个汉字，完全兼容GB2312标准，同时支持繁体汉字以及日韩汉字等

- Unicode字符集，UTF-8编码：可以用来表示Unicode标准中任意字符，它是电子邮件、网页及其他存储或传送文字的应用 中，优先采用的编码。互联网工程工作小组（IETF）要求所有互联网协议都必须支持UTF-8编码。它使用一至四个字节为每个字符编码

  编码规则： 

  128个US-ASCII字符，只需一个字节编码

  拉丁文等字符，需要二个字节编码

  大部分常用字（含中文），使用三个字节编码

  其他极少使用的Unicode辅助字符，使用四字节编码

### 1.3 字符串中的编码解码问题

```java
byte[] getBytes()	使用平台的默认字符集将该 String编码为一系列字节
byte[] getBytes(String charsetName)	 使用指定的字符集将该 String编码为一系列字节
String(byte[] bytes)   使用平台的默认字符集解码指定的字节数组来创建字符串
String(byte[] bytes, String charsetName)  通过指定的字符集解码指定的字节数组来创建字符串
```

代码演示

```java
//定义一个字符串
String s = "中国";
byte[] bys = s.getBytes(); //[-28, -72, -83, -27, -101, -67]
byte[] bys = s.getBytes("UTF-8"); //[-28, -72, -83, -27, -101, -67]
System.out.println(Arrays.toString(bys));

String ss = new String(bys);
String ss = new String(bys,"UTF-8");
System.out.println(ss);
```

### 1.4 字符流写数据

- 父类

  - Writer: 用于写入字符流的抽象父类


  - FileWriter: 用于写入字符流的常用子

- 构造方法

```java
FileWriter(File file)	根据给定的 File 对象构造一个 FileWriter 对象
FileWriter(File file, boolean append)	根据给定的 File 对象构造一个 FileWriter 对象
FileWriter(String fileName)	 根据给定的文件名构造一个 FileWriter 对象
FileWriter(String fileName, boolean append)	 根据给定的文件名以及指示是否附加写入数据的 boolean值来构造FileWriter对象
```

- 成员方法

```java
void write(int c)	                        写一个字符
void write(char[] cbuf)	                    写入一个字符数组
void write(char[] cbuf, int off, int len)	写入字符数组的一部分
void write(String str)	                    写一个字符串
void write(String str, int off, int len)	写一个字符串的一部分
```

- 刷新和关闭方法

```java
flush()	  刷新流，之后还可以继续写数据
close()	  关闭流，释放资源，但是在关闭之前会先刷新流。一旦关闭，就不能再写数据
```

### 1.5 字符流读数据

- 父类

  - Reader: 用于读取字符流的抽象父类


  - FileReader: 用于读取字符流的常用子类

- 构造方法

```java
FileReader(File file)	在给定从中读取数据的 File 的情况下创建一个新 FileReader
FileReader(String fileName)	在给定从中读取数据的文件名的情况下创建一个新 FileReader
```

- 成员方法

```java
int read()	一次读一个字符数据
int read(char[] cbuf)	一次读一个字符数组数据
```

### 1.6 字符缓冲流

- 介绍
  - BufferedWriter：将文本写入字符输出流，缓冲字符，以提供单个字符，数组和字符串的高效写入，可以指定缓冲区大小，或者可以接受默认大小。默认值足够大，可用于大多数用途
  - BufferedReader：从字符输入流读取文本，缓冲字符，以提供字符，数组和行的高效读取，可以指定缓冲区大小，或者可以使用默认大小。 默认值足够大，可用于大多数用途

- 构造方法

```java
BufferedWriter(Writer out)	创建字符缓冲输出流对象
BufferedReader(Reader in)	创建字符缓冲输入流对象
```

### 1.7 字符缓冲流特有功能

```java
BufferedWriter：
void newLine()	写一行行分隔符，行分隔符字符串由系统属性定义
BufferedReader:
String readLine() 读一行文字，含行的内容的字符串，不包括任何行终止字符，若流的结尾已经到达，则为null
```

### 1.8 字符流用户注册案例

### 1.9 字符缓冲流操作文件中数据排序案例

### 1.10 小结

![01_IO流小结](http://md.xyzt.cc/md/01_IO%E6%B5%81%E5%B0%8F%E7%BB%93.png)

## 转换流

### 1.1 字符流中和编码解码问题相关的两个类









































































































