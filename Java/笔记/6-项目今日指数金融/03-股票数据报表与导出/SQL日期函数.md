# 1.获取日期时间函数

## 1.1 获取当前日期时间

~~~sql
SELECT NOW(); # 2022-01-06 22:37:45
~~~

## 1.2 获取当前日期
~~~sql
SELECT CURRENT_DATE(); # 2022-01-06
~~~

## 1.3 获取当前时间
~~~sql
SELECT CURRENT_TIME(); # 22:39:04
~~~

# 2.日期格式化★★★

## 2.1 日期转指定格式字符串 

~~~sql
SELECT DATE_FORMAT('2022-01-08 22:23:01', '%Y%m%d%H%i%s');# 20220108222301
说明：
%W 星期名字(Sunday……Saturday) 
%D 有英语前缀的月份的日期(1st, 2nd, 3rd, 等等。)
%Y 年, 数字, 4 位 ★★★
%y 年, 数字, 2 位
%a 缩写的星期名字(Sun……Sat)
%d 月份中的天数, 数字(00……31) ★★★
%e 月份中的天数, 数字(0……31)
%m 月, 数字(01……12) ★★★ month
%c 月, 数字(1……12)
%b 缩写的月份名字(Jan……Dec)
%j 一年中的天数(001……366)
%H 小时(00……23)★★★
%k 小时(0……23)
%h 小时(01……12) 
%I 小时(01……12)
%l 小时(1……12)
%i 分钟, 数字(00……59) ★★★ minite
%r 时间,12 小时(hh:mm:ss [AP]M)
%T 时间,24 小时(hh:mm:ss)
%S 秒(00……59)
%s 秒(00……59) ★★★
%p AM或PM
%w 一个星期中的天数(0=Sunday ……6=Saturday )
%U 星期(0……52), 这里星期天是星期的第一天,查询指定日期属于当前年份的第几个周 ★★★★
%u 星期(0……52), 这里星期一是星期的第一天
~~~

示例代码：

~~~sql
# 日期格式化
select date_format(now(),'%Y%m%d%H%i%s');
# 获取当前是星期几
select date_format(now(),'%Y%m%W');
# 查看当前属于一年中的第几个周 以周末作为一个循环
select date_format(now(),'%Y%U');
select date_format('20220108090109','%Y%U');
~~~

## 2.2 字符串转日期

~~~sql
# 日期格式与表达式格式一致即可
SELECT STR_TO_DATE('06/01/2022', '%m/%d/%Y'); # 2022-06-01
SELECT STR_TO_DATE('20220108090109', '%Y%m%d%H%i%s'); # 2022-01-08 09:01:09
~~~

# 3.日期间隔

## 3.1 增加日期间隔 ★★★

~~~sql
# 间隔单位可以是DAY MONTH MINUTE WEEK YEAR SECOND HOUR
SELECT DATE_ADD(NOW(),INTERVAL 2 DAY); # 2022-01-07 22:46:39
SELECT DATE_ADD(NOW(),INTERVAL 2 MONTH); # 2022-02-06 22:47:17
SELECT DATE_ADD('2022-02-06 22:47:17',INTERVAL 2 MONTH); # 2022-04-06 22:47:17
~~~

## 3.2 减去一个时间间隔★★★
~~~sql
SELECT DATE_SUB(NOW(),INTERVAL 3 DAY); # 2022-01-03 22:49:24
SELECT DATE_SUB('2022-02-06 22:47:17',INTERVAL 2 MONTH); # 2021-12-06 22:47:17
~~~

## 3.3 日期相差天数（天）
~~~sql
select datediff('2022-01-06','2021-12-28'); -- 9
~~~

## 3.4 相差时间（小时）
~~~sql
select timediff('2022-01-06 08:08:08', '2021-12-28 09:00:00'); -- 08:08:08
~~~

# 4.星期操作

## 4.1 返回日期date的星期索引

~~~sql
# 返回日期date的星期索引(1=星期天，2=星期一, ……7=星期六)。这些索引值对应于ODBC标准。
SELECT DAYOFWEEK(NOW())-1;

# 返回date的星期索引(0=星期一，1=星期二, ……6= 星期天)
SELECT WEEKDAY(NOW())+1
~~~

# 5.其它操作

~~~sql
# 获取日
SELECT DAYOFMONTH(NOW());# 6
# 获取月份
SELECT MONTH(NOW());# 1
# 获取星期几
SELECT DAYNAME(NOW());# Thursday
# 获取第几季度
SELECT QUARTER(NOW());# 2022/1/6 --> 1
~~~
