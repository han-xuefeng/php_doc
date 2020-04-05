# mysql的sql语句编写考点

### 回顾真题
有A(id,sex,par,c1,c2),B(id,age,ci,c2)两张表，其中A.id与b.id关联，现在要求写出一条sql语句，将B中的age>50的记录的c1,c2更新到A表统一记录中的c1,c2字段中

##### 思考

###### 考官考点

mysql的关联update语句

###### 眼延伸

mysql的关联查询语句

#### 关联更新

1. 关联
    ```
    update A,B set A.c1 = b.c1,A.c2=B.c2 where A.id = B.id
    ```

2. 内连接

    ```
    update A inner join B on A.id = B.id set A.c1=B.c1,A.c2=B.c2 where .....
    ```
#### 六种关联查询

交叉连接cross join、内连接inner join、外连接left join/right join、联合查询union/union all、全连接full join

##### 交叉连接

```
    select * from a,b,
    或者
    select *from a cross join b cross join c
```



没有任何的关联条件，结果是笛卡尔积，结果集会很大，没有意义，很少使用

##### 内连接inner

```
    select * from a,b where a.id = b.id

    select * from a inner join b on a.id = b.id
```

多表中同时符合某种条件的数据记录的集合

###### 内连接分为三类

- 等值连接：

    on a.id = b.id

- 不等值连接：

    ona.id > b.id

- 自连接

    select* from a t1 inner join a t2 on t1.id = t2.pid

inner join 可以缩写为 join

##### 外连接

- 左外连接：left outer join ,已左表为主，先查询左表，按照on后的关联条件匹配右表，没有匹配到的用null填充，可以简写为left join

- 右外连接：right outer join ,已右表为主，先查询右表，按照on后的关联条件匹配左表，没有匹配到的用null填充，可以简写为right join

##### 联合查询

```
    select * from A union select * from B union ....
```

把多个结果集集中在一起，union 前的结果为基准，需要注意的是联合查询的列数要相等，相同的记录行会合并

如果使用union all，不会合并重复的记录行

##### 全连接

mysql不支持全连接

可以使用left join 和 union 和right join 联合使用

```
    select * from A left join B on A.id = B.id union select * from A right join B on A.id = B.id
```

##### 嵌套查询

用一条Sql语句的结果作为另一条Sql语句的条件

```
    select * from A where id in （select id from B）
```

#### 解题方法

根据考题要搞清楚表的结构和多表之间的关系，根据想要的结果思考使用哪种关联方式，通常把要查询的列先写出来，然后分析这些列都属于哪些表，才考虑使用关联查询

### 一网打尽
#### 真题

为了记录足球比赛的结果，设计表如下

参赛队伍表：team

字段|类型|描述
--|:--:|--:
teamid|int|主键
teamName|varchar(20)|队伍名称

比赛结果表

字段|类型|描述
--|:--:|--:
matchid|int|主键
hostTeamId|int|主队id
guestTeamId|int|客队id
matchResult|varchar(20)|比赛结果
matchTime|date|比赛日期

其中，match赛程表中的hostTeamId与guestTeamId都和team表中teamid关联，查询2006-6-1 到2006-7-1之间举行的所有比赛，并且用一下形势列出：拜仁 2:0 不莱玫 2006-6-21

###### 解答
```
    select t1.teamName,m.matchResult,t2.teamName,m.matchTime from match m left join team t1 on t1.teamid = m.hostTeamId left join team t2 left join t2.teamid = m.guestTeamId where matchTime between 2006-6-1 and 2006-7-1
```
