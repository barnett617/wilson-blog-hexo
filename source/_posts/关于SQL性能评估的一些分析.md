---
title: 关于SQL性能评估的一些分析
date: 2017-10-31 22:44:35
update: 2017-11-3 17:02:24
categories: 数据库
tags: [mysql]
---

继<a href="https://magi.getshell.cn/2017/10/31/%E5%85%B3%E4%BA%8Emysql%E4%B8%ADmax%E5%87%BD%E6%95%B0%E5%92%8Cgroupby%E8%81%94%E5%90%88%E4%BD%BF%E7%94%A8%E7%9A%84%E5%9D%91/#more">《关于mysql中max函数和groupby联合使用的坑》</a>后进一步关于SQL性能的探究

<!--more-->

<style>
table th:first-of-type {
    width: 100px;
}
</style>

| 类型 | 解释 |
| -- | ------------------ |
| id | select查询的序列号 |
| select_type | select查询的类型，主要是区别普通查询和联合查询、子查询之类的复杂查询 |
| table | 输出的行所引用的表 |
| type | 联合查询所使用的类型 |
| possible_keys | MySQL能使用哪个索引在该表中找到行 |
| key | MySQL实际决定使用的键 |
| key_len | MySQL决定使用的键长 |
| ref | 哪个字段或常数与key一起被使用 |
| rows | mysql要遍历多少数据才能找到，在innodb上是不准确的 |
| Extra |  - |

实例解释

```sql
mysql> desc t3;
+-------+--------------+------+-----+---------+----------------+
| Field | Type         | Null | Key | Default | Extra          |
+-------+--------------+------+-----+---------+----------------+
| id    | int(11)      | NO   | PRI | NULL    | auto_increment |
| other | varchar(255) | YES  |     | NULL    |                |
+-------+--------------+------+-----+---------+----------------+
2 rows in set
```

```SQL
mysql> select * from (select * from (select * from t3 where id = 3952602) a) b;
+---------+-------+
| id      | other |
+---------+-------+
| 3952602 | sth   |
+---------+-------+
1 row in set
```

```SQL
mysql> explain select * from (select * from (select * from t3 where id = 3952602) a) b;
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
| id | select_type | table      | type   | possible_keys     | key     | key_len | ref   | rows | Extra |
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
|  1 | PRIMARY     | <derived2> | system | NULL              | NULL    | NULL    | NULL  |    1 | NULL  |
|  2 | DERIVED     | <derived3> | system | NULL              | NULL    | NULL    | NULL  |    1 | NULL  |
|  3 | DERIVED     | t3         | const  | PRIMARY,idx_t3_id | PRIMARY | 4       | const |    1 | NULL  |
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
3 rows in set
```

```SQL
mysql> show index from t3;
+-------+------------+-----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| Table | Non_unique | Key_name  | Seq_in_index | Column_name | Collation | Cardinality | Sub_part | Packed | Null | Index_type | Comment | Index_comment |
+-------+------------+-----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
| t3    |          0 | PRIMARY   |            1 | id          | A         |           1 | NULL     | NULL   |      | BTREE      |         |               |
| t3    |          1 | idx_t3_id |            1 | id          | A         |           1 | NULL     | NULL   |      | BTREE      |         |               |
+-------+------------+-----------+--------------+-------------+-----------+-------------+----------+--------+------+------------+---------+---------------+
2 rows in set
```

#### id

- 从里往外执行，从id为3往上执行

- 同时作为一个解释序号，比如id为2中的table中的derived3就是指id为3的那个DERIVED（衍生表）

#### select_type

SIMPLE | PRIMARY | UNION | DEPENDENT UNION | UNION RESULT | SUBQUERY | DEPENDENT SUBQUERY | DERIVED | 共7种

逐个分析

###### SIMPLE

简单查询，即不使用UNION或子查询

```SQL
mysql> explain select * from t3 where id = 3952602;
+----+-------------+-------+-------+-------------------+---------+---------+-------+------+-------+
| id | select_type | table | type  | possible_keys     | key     | key_len | ref   | rows | Extra |
+----+-------------+-------+-------+-------------------+---------+---------+-------+------+-------+
|  1 | SIMPLE      | t3    | const | PRIMARY,idx_t3_id | PRIMARY | 4       | const |    1 | NULL  |
+----+-------------+-------+-------+-------------------+---------+---------+-------+------+-------+
1 row in set
```

####### PRIMARY

最外层的主查询

```SQL

mysql> explain select * from (select * from t3 where id = 3952602) a;
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
| id | select_type | table      | type   | possible_keys     | key     | key_len | ref   | rows | Extra |
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
|  1 | PRIMARY     | <derived2> | system | NULL              | NULL    | NULL    | NULL  |    1 | NULL  |
|  2 | DERIVED     | t3         | const  | PRIMARY,idx_t3_id | PRIMARY | 4       | const |    1 | NULL  |
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
2 rows in set
```

###### UNION

union中的第二个或者后面的select语句

###### UNION RESULT

```SQL
mysql> select * from t3 where id = 3952602 union all select * from t3;
+---------+-------+
| id      | other |
+---------+-------+
| 3952602 | sth   |
| 3952602 | sth   |
+---------+-------+
2 rows in set
```

```SQL
mysql> explain select * from t3 where id = 3952602 union all select * from t3;
+------+--------------+------------+-------+-------------------+---------+---------+-------+------+-----------------+
| id   | select_type  | table      | type  | possible_keys     | key     | key_len | ref   | rows | Extra           |
+------+--------------+------------+-------+-------------------+---------+---------+-------+------+-----------------+
|    1 | PRIMARY      | t3         | const | PRIMARY,idx_t3_id | PRIMARY | 4       | const |    1 | NULL            |
|    2 | UNION        | t3         | ALL   | NULL              | NULL    | NULL    | NULL  |    1 | NULL            |
| NULL | UNION RESULT | <union1,2> | ALL   | NULL              | NULL    | NULL    | NULL  | NULL | Using temporary |
+------+--------------+------------+-------+-------------------+---------+---------+-------+------+-----------------+
3 rows in set
```

###### DEPENDENT UNION

union的结果

```SQL
mysql> select * from t3 where id in (select id from t3 where id = 3952602 union all select id
 from t3);
+---------+-------+
| id      | other |
+---------+-------+
| 3952602 | sth   |
+---------+-------+
1 row in set
```

```SQL
mysql> explain select * from t3 where id in (select id from t3 where id = 3952602 union all select id from t3);
+------+--------------------+------------+--------+-------------------+---------+---------+-------+------+-----------------+
| id   | select_type        | table      | type   | possible_keys     | key     | key_len | ref   | rows | Extra           |
+------+--------------------+------------+--------+-------------------+---------+---------+-------+------+-----------------+
|    1 | PRIMARY            | t3         | ALL    | NULL              | NULL    | NULL    | NULL  |    1 | Using where     |
|    2 | DEPENDENT SUBQUERY | t3         | const  | PRIMARY,idx_t3_id | PRIMARY | 4       | const |    1 | Using index     |
|    3 | DEPENDENT UNION    | t3         | eq_ref | PRIMARY,idx_t3_id | PRIMARY | 4       | func  |    1 | Using index     |
| NULL | UNION RESULT       | <union2,3> | ALL    | NULL              | NULL    | NULL    | NULL  | NULL | Using temporary |
+------+--------------------+------------+--------+-------------------+---------+---------+-------+------+-----------------+
4 rows in set
```


###### SUBQUERY

子查询的第一个select

```SQL
mysql> select * from t3 where id = (select id from t3 where id = 3952602);
+---------+-------+
| id      | other |
+---------+-------+
| 3952602 | sth   |
+---------+-------+
1 row in set
```

```SQL
mysql> explain select * from t3 where id = (select id from t3 where id = 3952602);
+----+-------------+-------+-------+-------------------+---------+---------+-------+------+-------------+
| id | select_type | table | type  | possible_keys     | key     | key_len | ref   | rows | Extra       |
+----+-------------+-------+-------+-------------------+---------+---------+-------+------+-------------+
|  1 | PRIMARY     | t3    | const | PRIMARY,idx_t3_id | PRIMARY | 4       | const |    1 | NULL        |
|  2 | SUBQUERY    | t3    | const | PRIMARY,idx_t3_id | PRIMARY | 4       | const |    1 | Using index |
+----+-------------+-------+-------+-------------------+---------+---------+-------+------+-------------+
2 rows in set
```

###### DEPENDENT SUBQUERY

子查询的第一个查询，取决于外面的查询

```SQL
mysql> explain select * from t3 where id in (select id from t3 where id = 3952602);
+----+-------------+-------+-------+-------------------+---------+---------+-------+------+-------------+
| id | select_type | table | type  | possible_keys     | key     | key_len | ref   | rows | Extra       |
+----+-------------+-------+-------+-------------------+---------+---------+-------+------+-------------+
|  1 | SIMPLE      | t3    | const | PRIMARY,idx_t3_id | PRIMARY | 4       | const |    1 | NULL        |
|  1 | SIMPLE      | t3    | const | PRIMARY,idx_t3_id | PRIMARY | 4       | const |    1 | Using index |
+----+-------------+-------+-------+-------------------+---------+---------+-------+------+-------------+
2 rows in set
```

###### DERIVED

派生表的select（from子句的子查询）

```SQL
mysql> select * from (select * from t3 where id = 3952602);
1248 - Every derived table must have its own alias


mysql> select * from (select * from t3 where id = 3952602) a;
+---------+-------+
| id      | other |
+---------+-------+
| 3952602 | sth   |
+---------+-------+
1 row in set

```
```sql

mysql> 	explain select * from (select * from t3 where id = 3952602) a;
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
| id | select_type | table      | type   | possible_keys     | key     | key_len | ref   | rows | Extra |
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
|  1 | PRIMARY     | <derived2> | system | NULL              | NULL    | NULL    | NULL  |    1 | NULL  |
|  2 | DERIVED     | t3         | const  | PRIMARY,idx_t3_id | PRIMARY | 4       | const |    1 | NULL  |
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
2 rows in set
```
#### table

该行数据是关于哪张表的

```sql
mysql> explain select * from (select * from (select * from t3 where id = 3952602) a) b;
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
| id | select_type | table      | type   | possible_keys     | key     | key_len | ref   | rows | Extra |
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
|  1 | PRIMARY     | <derived2> | system | NULL              | NULL    | NULL    | NULL  |    1 | NULL  |
|  2 | DERIVED     | <derived3> | system | NULL              | NULL    | NULL    | NULL  |    1 | NULL  |
|  3 | DERIVED     | t3         | const  | PRIMARY,idx_t3_id | PRIMARY | 4       | const |    1 | NULL  |
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
3 rows in set
```

#### type
重要指标，查询类型/访问类型

从好到坏依次是：

**system > const > eq_ref > ref > fulltext > ref_or_null > index_merge > unique_subquery > index_subquery > range > index > ALL**

一般来说，得保证查询至少达到range级别，最好能达到ref

###### null

MySQL在优化过程中分解语句，执行时甚至不用访问表或索引

###### system

system是const连接类型的一个特例，即当查询的表中仅有一行满足条件

```sql
mysql> explain select * from (select * from t3 where id = 3952602) a;
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
| id | select_type | table      | type   | possible_keys     | key     | key_len | ref   | rows | Extra |
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
|  1 | PRIMARY     | <derived2> | system | NULL              | NULL    | NULL    | NULL  |    1 | NULL  |
|  2 | DERIVED     | t3         | const  | PRIMARY,idx_t3_id | PRIMARY | 4       | const |    1 | NULL  |
+----+-------------+------------+--------+-------------------+---------+---------+-------+------+-------+
2 rows in set
```

###### const

表最多有一个匹配行，它将在查询开始时被读取。因为仅有一行，在这行的列值可被优化器剩余部分认为是常数。const表很快，因为只读取一次

```sql
mysql> explain select * from t3 where id = 3952602;
+----+-------------+-------+-------+-------------------+---------+---------+-------+------+-------+
| id | select_type | table | type  | possible_keys     | key     | key_len | ref   | rows | Extra |
+----+-------------+-------+-------+-------------------+---------+---------+-------+------+-------+
|  1 | SIMPLE      | t3    | const | PRIMARY,idx_t3_id | PRIMARY | 4       | const |    1 | NULL  |
+----+-------------+-------+-------+-------------------+---------+---------+-------+------+-------+
1 row in set
```

###### eq_ref

唯一性索引扫描，对于每个索引键，表中只有一条记录与之匹配。常见于主键或唯一索引扫描

对于每个来自前面的表的行组合，从该表中读取一行。是除了const最好的连接类型，用在一个索引的所有部分被连接使用并且索引是UNIQUE或PRIMARY KEY

```sql
mysql> explain select * from t3,t4 where t3.id = t4.accountId;
+----+-------------+-------+--------+-------------------+---------+---------+-----------------+------+-------+
| id | select_type | table | type   | possible_keys     | key     | key_len | ref             | rows | Extra |
+----+-------------+-------+--------+-------------------+---------+---------+-----------------+------+-------+
|  1 | SIMPLE      | t3    | ALL    | PRIMARY,idx_t3_id | NULL    | NULL    | NULL            |    1 | NULL  |
|  1 | SIMPLE      | t4    | eq_ref | PRIMARY           | PRIMARY | 4       | localtest.t3.id |    1 | NULL  |
+----+-------------+-------+--------+-------------------+---------+---------+-----------------+------+-------+
2 rows in set
```

###### ref

非唯一性索引扫描，返回匹配某个单独值的所有行。常见于使用非唯一索引及唯一索引的非唯一前缀进行的查找

###### ref_or_null

该类型如同ref，但是添加了MySQL可以专门搜索包含null值的行

###### index_merge

该连接类型表示使用了索引合并优化方法。这种情况下，key列包含了使用的索引清单，key_len包含了使用的索引最长的关键元素

###### unique_subquery

子查询中的返回结果字段组合是主键或唯一索引，可以完全替换子查询，效率更高

###### index_subquery

类似于unique_subquery，可以替换IN子查询，但只适合子查询中的返回结果字段组合是非唯一索引

###### range

只检索给定范围的行，使用一个索引来选择行。

key显示使用了哪个索引，key_len包含所使用索引的最长关键元素。

在该类型中的ref列为null

```sql
mysql> explain select * from t3 where id = 3952602 or id = 3952603;
+----+-------------+-------+-------+-------------------+---------+---------+------+------+-------------+
| id | select_type | table | type  | possible_keys     | key     | key_len | ref  | rows | Extra       |
+----+-------------+-------+-------+-------------------+---------+---------+------+------+-------------+
|  1 | SIMPLE      | t3    | range | PRIMARY,idx_t3_id | PRIMARY | 4       | NULL |    2 | Using where |
+----+-------------+-------+-------+-------------------+---------+---------+------+------+-------------+
1 row in set
```

###### index

该连接类型与ALL相同，但只有索引树被扫描。通常比ALL快，因为索引文件通常比数据文件小

###### ALL

对于每个来自于先前的表的行组合，进行完整的表扫描。

通常可以增加更多的索引而不要使用ALL

#### possible_keys

指出MySQL能使用哪个索引在该表中找到行

如果是空的，则表示没有相关的索引。这时要提高性能，可通过检验WHERE子句，看是否它引用某些列或适合索引的列来提高查询性能。

#### key

MySQL实际决定使用的键（索引）

若没有选择索引，值是null

若想强制MySQL使用或忽视possible_keys中的索引，查询中可用FORCE INDEX、USE INDEX、IGNORE INDEX

#### key_len

MySQL决定使用的键长度，在不损失精确性的情况下，长度越短越好。特别注意这个值可以得出一个多重主键里mysql实际使用了哪一部分

#### ref

表示MySQL使用哪个列或常数与key一起从表中选择行

#### rows

MySQL认为其执行查询时必须检查的行数

#### extra

包含MySQL解决查询的详细信息

###### distinct

一旦找到与行相联合匹配的行就不再搜索了

###### not exists

MySQL优化了left join，一旦找到了匹配left join标准的行，就不再搜索了

###### range checked for each Record(index map:#)

没有找到理想的索引，这是使用索引的最慢连接之一

###### using filesort

出现这种情况需要优化，因为MySQL需要进行额外的步骤来发现如何对返回的行排序。它根据连接类型以及存储排序键值和匹配条件的全部行的行指针来排序全部行。

###### using temporary

MySQL需要建立一个临时表来存储结果，通常发生在对不同的列集进行order by而不是group by上。

如果此信息显示Using filesort或者Using temporary的话会很吃力，WHERE和ORDER BY的索引经常无法兼顾，如果按照WHERE来确定索引，那么在ORDER BY时，就必然会引起Using filesort，这就要看是先过滤再排序划算，还是先排序再过滤划算。

###### using index

不读数据文件，只从索引文件获取数据

###### Only index

这意味着信息只用索引树中的信息检索出的，这比扫描整个表要快。

###### where used

就是使用上了where限制。

如果是impossible where 表示用不着where，一般就是没查出来啥。


### END

这篇博客写了好几天，中间断断续续，东拼西凑，内容有待改进。参考了很多，但都找不到完整详细的解释，关于参考的实例都进行了本地验证，特此整理

主要参考链接：http://www.cnitblog.com/aliyiyi08/archive/2008/09/09/48878.html
