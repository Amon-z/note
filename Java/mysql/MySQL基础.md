# MySQL基础

## SQL概述

### 概述

![image-20230420002646476](.\assets\image-20230420002646476.png)DDL(Data Definition Language) ： 数据定义语言，用来定义数据库对象：数据库，表，列等。DDL简单理解就是用来操作数据库，表等

DML(Data Manipulation Language) 数据操作语言，用来对数据库中表的数据进行增删改。DML简单理解就对表中数据进行增删改

DQL(Data Query Language) 数据查询语言，用来查询数据库中表的记录(数据)。DQL简单理解就是对数据进行查询操作。从数据库表中查询到我们想要的数据。

DCL(Data Control Language) 数据控制语言，用来定义数据库的访问权限和安全级别，及创建用户。DML简单理解就是对数据库进行权限控制。比如我让某一个数据库表只能让某一个用户进行操作等。

### 通用语法

- SQL 语句可以单行或多行书写，以分号结尾。
- MySQL 数据库的 SQL 语句不区分大小写，关键字建议使用大写。
- 单行注释: -- 注释内容 或 #注释内容(MySQL 特有) 
- 多行注释: /* 注释 */

### 分类

- DDL(Data Definition Language) ： 数据定义语言，用来定义数据库对象：数据库，表，列等。
- DML(Data Manipulation Language) 数据操作语言，用来对数据库中表的数据进行增删改。
- DQL(Data Query Language) 数据查询语言，用来查询数据库中表的记录(数据)。
- DCL(Data Control Language) 数据控制语言，用来定义数据库的访问权限和安全级别，及创建用户。

## MySQL安装

1. 官网下载解压版的MySQL，解压出来并配置环境变量。

2. 新建`my.ini`配置文件，放入MySQL文件夹根目录。

```ini
[mysql]
default-character-set=utf8

[mysqld]
character-set-server=utf8
default-storage-engine=INNODB
sql_mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

3. 以管理员模式运行cmd，输入

````sh
$ mysqld --initialize-insecure
$ mysqld -install
$ net start mysql # 启动mysql
$ net stop mysql # 停止mysql
$ services.msc   # 设MySQL服务为自动启动
````

4. 修改默认账户密码

```sh
$ mysqladmin -u root password 1234
```

5. MySQL登录和退出

```sh
$ mysql -uroot -p1234
$ mysql -uroot -p123 -h127.0.0.1 -P3306
$ exit
```

6. 卸载MySQL，以管理员模式运行cmd

```sh
$ net stop mysql
$ mysqld -remove mysql
```

删除MySQL目录及相关环境变量。

## DDL

### 操作数据库

```mysql
SHOW DATABASES;                #查询数据库
CREATE DATABASE 数据库名称;      #创建数据库
CREATE DATABASE IF NOT EXISTS 数据库名称;     #如不存在则创建数据库
DROP DATABASE 数据库名称;        #删除数据库
DROP DATABASE IF EXISTS 数据库名称;        #如存在则删除数据库
USE 数据库名称;                  #使用数据库
SELECT DATABASE();             #查看当前使用的数据库
```

### 操作表

```mysql
SHOW TABLES;                 # 查询当前数据库下所有表名称
DESC 表名称;                  # 查询表结构
CREATE TABLE 表名 (          # 创建表
	字段名1  数据类型1,
	字段名2  数据类型2,
	…
	字段名n  数据类型n
);
DROP TABLE 表名;             # 删除表
DROP TABLE IF EXISTS 表名;   # 如存在则删除表
ALTER TABLE 表名 RENAME TO 新的表名;             # 修改表名
ALTER TABLE 表名 ADD 列名 数据类型;               # 添加一列
ALTER TABLE 表名 MODIFY 列名 新数据类型;          # 修改数据类型
ALTER TABLE 表名 CHANGE 列名 新列名 新数据类型;    # 修改列名和数据类型
ALTER TABLE 表名 DROP 列名;                     # 删除列
```

### 数据类型

* 数值

  ```sql
  tinyint : 小整数型，占一个字节。
  int	： 大整数类型，占四个字节。
  double ： 浮点类型。使用格式： 字段名 double(总长度,小数点后保留的位数)
  ```

* 日期

  ```sql
  date ： 日期值。只包含年月日
  	eg ：birthday date
  datetime ： 混合日期和时间值。包含年月日时分秒
  ```

* 字符串

  ```sql
  char ： 定长字符串。
  	优点：存储性能高
  	缺点：浪费空间
  	eg ： name char(10)  如果存的数据字符个数不足10个，也会占10个的空间
  varchar ： 变长字符串。
  	优点：节约空间
  	缺点：存储性能底
  	eg ： name varchar(10) 实际存储的数据字符个数多少就占用多少空间
  ```

> 注意：其他类型参考资料中的《MySQL数据类型.xlsx》

## Navicat使用

### 安装

链接

修改表结构

编写SQL语句并执行

## DML

```mysql
INSERT INTO 表名(列名1,列名2,…) VALUES(值1,值2,…);       #给指定列添加数据
INSERT INTO 表名 VALUES(值1,值2,…);                     #给全部列添加数据
INSERT INTO 表名(列名1,列名2,…) VALUES(值1,值2,…),(值1,值2,…),(值1,值2,…)…;
INSERT INTO 表名 VALUES(值1,值2,…),(值1,值2,…),(值1,值2,…)…;   #批量添加数据

UPDATE 表名 SET 列名1=值1,列名2=值2,… [WHERE 条件] ;      #修改表数据
DELETE FROM 表名 [WHERE 条件] ;                         #删除数据
```

注意：修改语句中如果不加条件，则将所有数据都修改！像上面的语句中的中括号，表示在写sql语句中可以省略这部分。

## ------DQL

```mysql
SELECT      字段列表
FROM        表名列表 
WHERE       条件列表
GROUP BY    分组字段
HAVING      分组后条件
ORDER BY    排序字段
LIMIT       分页限定
```

为了给大家演示查询的语句，我们需要先准备表及一些数据：

```sql
-- 删除stu表
drop table if exists stu;

-- 创建stu表
CREATE TABLE stu (
 id int, -- 编号
 name varchar(20), -- 姓名
 age int, -- 年龄
 sex varchar(5), -- 性别
 address varchar(100), -- 地址
 math double(5,2), -- 数学成绩
 english double(5,2), -- 英语成绩
 hire_date date -- 入学时间
);

-- 添加数据
INSERT INTO stu(id,NAME,age,sex,address,math,english,hire_date) 
VALUES 
(1,'马运',55,'男','杭州',66,78,'1995-09-01'),
(2,'马花疼',45,'女','深圳',98,87,'1998-09-01'),
(3,'马斯克',55,'男','香港',56,77,'1999-09-02'),
(4,'柳白',20,'女','湖南',76,65,'1997-09-05'),
(5,'柳青',20,'男','湖南',86,NULL,'1998-09-01'),
(6,'刘德花',57,'男','香港',99,99,'1998-09-01'),
(7,'张学右',22,'女','香港',99,99,'1998-09-01'),
(8,'德玛西亚',18,'男','南京',56,65,'1994-09-02');
```

### 基础查询

* **查询多个字段**

```sql
SELECT 字段列表 FROM 表名;
SELECT * FROM 表名; -- 查询所有数据
```

* **去除重复记录**

```sql
SELECT DISTINCT 字段列表 FROM 表名;
```

* **起别名**

```sql
AS:   #:也可以省略
```

### 条件查询

```mysql
SELECT 字段列表 FROM 表名 WHERE 条件列表;
```

条件列表可以使用以下运算符

![image-20230420014938746](.\assets\image-20230420014938746.png)

### 模糊查询

>模糊查询使用like关键字，可以使用通配符进行占位:
>
>（1）_ : 代表单个任意字符
>
>（2）% : 代表任意个数字符

### 排序查询

```mysql
SELECT 字段列表 FROM 表名 ORDER BY 排序字段名1 [排序方式1],排序字段名2 [排序方式2] …;
```

上述语句中的排序方式有两种，分别是：

* ASC ： 升序排列 **（默认值）**
* DESC ： 降序排列

> 注意：如果有多个排序条件，当前边的条件值一样时，才会根据第二条件进行排序

### 聚合函数

| 函数名      | 功能                             |
| ----------- | -------------------------------- |
| count(列名) | 统计数量（一般选用不为null的列） |
| max(列名)   | 最大值                           |
| min(列名)   | 最小值                           |
| sum(列名)   | 求和                             |
| avg(列名)   | 平均值                           |

```mysql
SELECT 聚合函数名(列名) FROM 表;
```

>注意：null 值不参与所有聚合函数运算

### 分组查询

```mysql
SELECT 字段列表 FROM 表名 [WHERE 分组前条件限定] GROUP BY 分组字段名 [HAVING 分组后条件过滤];
```

> 注意：分组之后，查询的字段为聚合函数和分组字段，查询其他字段无任何意义

**where 和 having 区别：**

* 执行时机不一样：where 是分组之前进行限定，不满足where条件，则不参与分组，而having是分组之后对结果进行过滤。

* 可判断的条件不一样：where 不能对聚合函数进行判断，having 可以。

### 分页查询

```sql
SELECT 字段列表 FROM 表名 LIMIT  起始索引 , 查询条目数;
```

> 注意： 上述语句中的起始索引是从0开始

# MySQL高级















