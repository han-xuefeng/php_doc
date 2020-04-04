# MySQL基础知识考点

### 一.回顾真题

#### 考题
请写出下面MySQL数据类型表达的意义（int(0)、char(16)、varchar(16)、datetime、text）

##### 思考
###### 考官考点
mysql数据类型

###### 延伸
mysql基础操作

mysql存储引擎

mysql锁机制

mysql事务处理 存储过程 触发器

#### mysql数据类型
##### 整数类型
tinyint smallint mediumini int bigint

属性：unsigend

长度：可以为整数类型指定宽度，例如：int(11)对大多数应用是没有意义的，她不会限制值的合法范围，只会影响显示字符的个数

##### 实数类型
float double decimal

decimal可以存储比bigint还大的整数；可用于存储精确的小数

float和double类型支持使用标准的浮点进行近似计算

##### 字符串类型
varchar,char,text,blob

varchar 类型用于存储可变长度字符创，它比定长型更节省空间

varchar使用1或2个额外字节记录字符串的长度，列长小于255字节使用1个字节表示，否则用2个

varchar长度，如果存储内容超出指定长度，会被截断

char是定长的，根据定义的字符串长度分配足够的空间

char会根据需要采用空格进行填充以方便比较

char适合存储很短的字符串，或者所有的值都接近同一个长度

char长度，超出设定的长度，会被截断

对于经常变更的数据，char比varchar更好，char不容易产生碎片

对于非常短的列，char比varchar在存储空间上更有效率

只分配真正的空间，更长的列会消耗更多的内存

尽量避免使用blob/text类型，查询会使用临时表，导致严重的性能开销

##### 枚举
有时候可以使用枚举代替常用的字符串类型

把不重复的集合存储为一个预定于的集合

列表值非常紧凑，把列表值压缩到一个或两个字节

内部存储的是整数

尽量避免使用数字作为枚举的常量，容易混乱

排序是按照内部存储的整数进行排序

枚举表会使表大小大大减小

##### 日期和时间类型
timestamp,datetime

尽量使用timestamp,比datetime空间效率高

用整数保存时间戳的格式通常不方便处理

如果需要存储微秒，可以使用bigint存储

##### 列属性
auto_increment,default,not null,zerofill

#### 回顾真题
请写出下面MySQL数据类型表达的意义（int(0)、char(16)、varchar(16)、datetime、text）

int(0) 指定int类型的显示宽度为0

char(16) 指定定长的字符串类型，长度为16

varchar(16) 指定可变长度的字符串，最大长度为16

datetime 指定时间日期的类型

text 指定字符串类型，用来存储大文本数据

### 延伸考点
#### mysql基础操作
##### 常见操作
mysql连接和关闭：mysql -h -u -p -P

其他：\G \c \q \s \h \d

#### mysql表引擎
##### innodb表引擎
默认事务性引擎，最重要最广泛的存储引擎，性能非常优秀

数据存储在共享表空间，可以通过配置分开

对主键查询的性能高于其他类型的存储引擎

内部做了很多优化，从磁盘读取数据时自动在内存构建hash索引

插入数据时自动构建插入缓冲区

通过一些机制和工具支持真正的热备份

支持奔溃后的安全恢复

支持行级锁，支持外键

##### myisam表引擎

5.1.版本前，myisam是默认的存储引擎

拥有全文索引，压缩，空间函数

不支持事务和行级锁，不支持崩溃后的安全恢复

是表级锁

表存储在两个文件，myd（存的数据）和myi（存的索引）

设计简单，某些场景下性能很好

不支持外键

##### 其他表引擎
csv,archive,blackhole,merory

建议优先选择innnodb

#### mysql锁机制
##### 基础概念
表锁是日常开发当中常见的问题，因此也是面试当中最常见的考察点，当多个查询同一时刻进行数据修改时，就会产生并发控制的问题。

共享锁和排他锁（读锁和写锁）

###### 读锁
共享的，不堵塞，多用户可以同时读取一个资源，互不干扰
###### 写锁
排他的，一个写锁会阻塞其他的写锁和读锁，这样可以只允许一个人进行写入，防止其他用户读取正在写入的资源
###### 锁粒度
表锁，系统性能开销最表，会锁定整张表，myisam表引擎使用表锁

行锁，最大程度支持并发处理，但是也带来了最大开销，innodb实现行级锁

#### mysql事务处理 存储过程 触发器
##### 事务处理
mysql提供事务处理的表引擎 innodb

服务器层不管事务，由下层的引擎实现，所以同一个事务中，使用多种存储引擎不靠谱

在非事务的表上执行事务操作，mysql不会发出提醒，也不会报错

##### 存储过程
为以后的使用而保存一条或多条mysql语句的集合

存储过程就是有业务逻辑和流程的集合

可以在存储过程中，删除数据，更新数据，创建表等等

###### 使用场景
通过把处理封装在容易使用的单元中，简化复杂化的操作

保证数据的一执行

简化对变动的管理

##### 触发器
提供给程序员和数据分析员来保证数据完整性的一种方法，它是与表事件相关的特殊的存储过程

###### 使用场景
可以通过数据库的相关表实现联极更改

实时监控某张表中的某个字段的更改而需要作出的处理

某些业务编号的生成

滥用会造成数据库及应用程序的维护困难

### 一网打尽
#### 真题
说明表存储引擎innodb和myisam的区别

innodb支持事务，myisam不支持事务

innodb支持外键，myisam不支持外键

innodb是行级锁，myisam表级锁

innodb数据存储在共享表中间，myisam数据是存储在myd和myi文件

innodb查询自动在内存中构建hash索引，插入会构建插入缓冲区