# mysql查询优化考点

### 真题

请简述项目中sql语句执行效率的方法，从哪些方面，sql语句如何分析

### 考点分析

#### 考官考点

查找分析速度慢的原因

优化查询过程中的数据访问

优化长难的查询语句

优化特定类型的查询语句

##### 分析sql查询慢的方法

- 记录慢查询日志

- 分析查询日志，不要直接打开慢查询日志进行分析，这样比较浪费时间和精力，可以使用pt-query-digest

语法

    ```
    show_query_log
    show_query_log_file
    long_query_time
    ```

###### 使用show profile
set profiling = 1开启，服务器上执行的所有语句会检测消耗的时间，存到临时表中

show profiles 查询执行过的sql语句

show profile for query 临时表id 查询指定一条的sql语句

###### 使用show status

show status会返回一些sql语句执行的计数器，show global status查看服务器级别是所有计数

有时候根据这些计数，可以猜测哪些操作代价较高或者消耗时间多

###### 使用show processlist

观察是否有大量线程处于不正常的状态或者特征

###### 使用explain

分析单调的sql语句


###### 使用desc  

分析单条sql语句

##### 优化查询过程中的数据访问

访问数据太多 导致查询条件下降

确定应用程序是否在检索大量超过需要的数据，可能是太多行或列

确定mysql服务器是否在分析大量不必要的数据行

###### 避免使用如下的sql语句

查询不需要的记录，使用limit解决

查询不需要的列，多表关联返回全部列，指定列名

总是取出全部列，select * 会让优化器无法完成索引覆盖扫描的优化

重复查询多次相同的数据，尽量使用缓存

###### 是否在扫描额外的记录

使用explain来分析，如果发现查询需要扫描大量的数据，但只返回少数的行，可以通过如下技巧去优化

- 使用索引覆盖扫描，把所有用的列都放到索引中，这样存储引擎不需要回表获取对应的行就可以返回结果

- 改变数据库和表的结构，修改数据表范式

- 重写sql语句，让优化器可以以更优的方式执行查询

###### 一个复杂的查询好还是多个简单的查询好

mysql内部每秒能扫描内存中上百万行数据，相比之下，响应数据给客户端就要慢得多

使用尽可能少的查询是好的，但是有时将一个大的查询分解为多个小的查询是很有必要的

###### 切分查询

将一个大的查询分为多个小的相同的查询

一次性删除1000 万条的数据要比一次删除1万，暂停一会的方案更加损耗服务器开销

###### 分解关联查询

可以将一条关联的语句分解为多条sql来执行

让缓存的效率更高

执行单个查询可以减少锁的竞争

在应用层做关联可以更容易对数据库进行拆分，查询效率会有大幅度提升

较少冗余记录查询

##### 优化特定类型的查询语句

###### 优化count()查询

1. count(\*)中的* 会忽略所有的列，直接统计所有列数，因此不要使用count(列名)

2. myisam中没有任何where条件的count（\*）分长快

3. 当有where条件，myisam的count统计不一定比其他引擎快

4. 可以使用explain查询近似值，用近似值代替count(\*)

5. 增加汇总表

6. 使用缓存

###### 优化关联查询

1. 确定on或者using子句的列上有索引

2. 确保group by 和 order by 中只有一个表中的列，这样mysql才能使用索引

###### 优化子查询

1. 尽可能使用关联查询来代替

###### 优化group by 和distinct

1. 这两种查询可以使用索引来优化，是最有效优化方法

2. 关联查询中，使用标识列进行分组的效率高

3. 如果不要order by ,进行group by 时使用order by null,mysql不会再进行文件排序

4. with rollup超级聚合，可以挪到应用程序处理

###### 优化limit 分页

1. limit 偏移量大的时候，查询效率会低

2. 可以记录查询的最大id，下次查询是直接根据id来查询

###### 优化union查询

union all 效率高于union

#### 解题方法

对于此类考题，先说明如何定位低效的sql语句，然后再根据sql语句可能低效的原因做排查，先从索引着手，如果索引没有问题，考虑以上几个方面，数据访问的问题，长难查询语句的问题还是一些特定类型优化的问题，逐一回答

### 一网打尽

sql语句优化的方法

1. 找出查询慢的sql

    - 使用mysql慢日志
    - 使用show status
    - 使用show profile
    - 使用show processList
    - 使用explain逐条分析sql性能

2. 慢sql优化
    - 数据访问问题
    - 长难句问题
    - 特定查询语句
        count
        limit
        group by
        union
        关联查询
        子查询