---
title: 关于mysql中max函数和groupby联合使用的坑
date: 2017-10-31 20:50:43
update: 2017-10-31 21:29:49
categories: 数据库
tags: [mysql]
---

### 背景

关于朋友随手抛出的一段SQL，发现MySQL中关于max()和group by联合使用中的一个坑，特此整理。

<!--more-->

![](http://trigolds.com/yh1.png)
YH:老铁们，这段hql对不对啊

我扫了一眼，总觉得看着别扭，自己试着去掉字符串拼接，还原出SQL来看，依然感觉不对，然后自己试着写了查询，在本地建个表，造了些数据，用简化后的SQL做测试时，
当我定睛检查以下这句SQL

```
select predictId, max(evaluateDate) evalDate, productId from productcashpredict group by productId;
```


抛出一个疑问，MySQL是从后往前执行，先分组再求分组结果中evaluateDate最大的记录呢？还是先找出evaluateDate的最大记录，再分组呢？

网上查了查，发现，都不是！这里有个坑！如果直接这么结合max和group by使用，查出的结果，除了求max的字段和分组条件productId字段，其他字段的值都是错的！

会是这样：![](http://trigolds.com/yh4.png)

首先我在本地验证了一下是不是的确如此

```mysql
desc productcashpredict;

predictId	int(11)	NO	PRI		auto_increment
evaluateDate	datetime	YES			on update CURRENT_TIMESTAMP
other	varchar(255)	YES			
productId	int(11)	YES

select * from productcashpredict

+-----------+---------------------+-------+-----------+
| predictId | evaluateDate        | other | productId |
+-----------+---------------------+-------+-----------+
|         1 | 2017-10-31 18:14:37 | NULL  |     10001 |
|         2 | 2017-10-31 18:14:45 | NULL  |     10002 |
|         3 | 2017-10-31 18:14:59 | NULL  |     10002 |
|         4 | 2017-10-31 18:15:09 | NULL  |     10003 |
|         5 | 2017-10-31 18:15:22 | NULL  |     10001 |
+-----------+---------------------+-------+-----------+


select predictId, max(evaluateDate) evalDate, productId from productcashpredict group by productId;

+-----------+---------------------+-----------+
| predictId | evalDate            | productId |
+-----------+---------------------+-----------+
|         1 | 2017-10-31 18:15:22 |     10001 |
|         2 | 2017-10-31 18:14:59 |     10002 |
|         4 | 2017-10-31 18:15:09 |     10003 |
+-----------+---------------------+-----------+

```

直接这样查的确是错的，看predictId可以看出

![](http://trigolds.com/yh3.png)

我们可以看出来，MySQL其实是把以下两种查询揉在了一起，结果的确是按照productId进行分组的，查询结果也是从取自分组后，排序则是默认按照主键排序，但evalDate也的确取的是每一个productId组内最大的那一项，但这样揉起来，数据就不对了。

```
select * from productcashpredict group by productId;

1	2017-10-31 18:14:37		10001
2	2017-10-31 18:14:45		10002
4	2017-10-31 18:15:09		10003

select max(evaluateDate) evalDate, productId from productcashpredict;

2017-10-31 18:15:22	10001

```

既然这样，那总得有解决方案，我又问了抛问者YH，他们实际的使用场景是怎么查的，他甩出一张图。
我看了半天，很是理解不了，至少觉得逻辑不甚清晰，我开始质疑这样查询的结果是否正确，于是又去寻求max和group by联合使用的正确写法，得到下面两种方案：

于是我又建了一张表，来模拟真实场景

```
desc product_cash_predict;

id	int(11)	NO	PRI		auto_increment
add_time	datetime	YES			on update CURRENT_TIMESTAMP
deleted	varchar(255)	YES			
update_time	datetime	YES			on update CURRENT_TIMESTAMP
version	varchar(255)	YES			
evaluated_date	datetime	YES			on update CURRENT_TIMESTAMP
other	varchar(255)	YES			
product_id	int(11)	YES

SELECT * FROM `product_cash_predict`;

+----+----------+---------+-------------+---------+---------------------+-------+------------+
| id | add_time | deleted | update_time | version | evaluated_date      | other | product_id |
+----+----------+---------+-------------+---------+---------------------+-------+------------+
|  1 | NULL     | NULL    | NULL        | NULL    | 2017-10-31 18:14:37 | NULL  |      10001 |
|  2 | NULL     | NULL    | NULL        | NULL    | 2017-10-31 18:14:45 | NULL  |      10002 |
|  3 | NULL     | NULL    | NULL        | NULL    | 2017-10-31 18:14:59 | NULL  |      10002 |
|  4 | NULL     | NULL    | NULL        | NULL    | 2017-10-31 18:15:09 | NULL  |      10003 |
|  5 | NULL     | NULL    | NULL        | NULL    | 2017-10-31 18:15:22 | NULL  |      10001 |
+----+----------+---------+-------------+---------+---------------------+-------+------------+
```

先按YH的业务场景查询方式查一下：

```
SELECT
	p2.*
FROM
	(
		SELECT
			max(evaluated_date) evaluated_date,
			product_id
		FROM
			product_cash_predict
		GROUP BY
			product_id
	) p1
LEFT JOIN product_cash_predict p2 ON p1.product_id = p2.product_id
AND p1.evaluated_date = p2.evaluated_date

-- 0.034s
--
+----+----------+---------+-------------+---------+---------------------+-------+------------+
| id | add_time | deleted | update_time | version | evaluated_date      | other | product_id |
+----+----------+---------+-------------+---------+---------------------+-------+------------+
|  3 | NULL     | NULL    | NULL        | NULL    | 2017-10-31 18:14:59 | NULL  |      10002 |
|  4 | NULL     | NULL    | NULL        | NULL    | 2017-10-31 18:15:09 | NULL  |      10003 |
|  5 | NULL     | NULL    | NULL        | NULL    | 2017-10-31 18:15:22 | NULL  |      10001 |
+----+----------+---------+-------------+---------+---------------------+-------+------------+
```

那么这个结果对不对呢

先来看一下错误的查询方式：

```
select id, add_time, deleted, update_time, version, max(evaluated_date) evalDate, other, product_id from product_cash_predict group by product_id;
1					2017-10-31 18:15:22		10001
2					2017-10-31 18:14:59		10002
4					2017-10-31 18:15:09		10003

```

由于数据量少，上面查询的错误肉眼可以用识别

```

-- 解决方案一：先排序再分组

select * from product_cash_predict ORDER BY evaluated_date desc

5					2017-10-31 18:15:22		10001
4					2017-10-31 18:15:09		10003
3					2017-10-31 18:14:59		10002
2					2017-10-31 18:14:45		10002
1					2017-10-31 18:14:37		10001

select * from (select * from product_cash_predict ORDER BY evaluated_date desc) group by product_id;
--  Every derived table must have its own alias（衍生表需要有其自己的别名）

select * from (select * from product_cash_predict ORDER BY evaluated_date desc) as result group by product_id;

-- 正确结果
5					2017-10-31 18:15:22		10001
3					2017-10-31 18:14:59		10002
4					2017-10-31 18:15:09		10003
-- 0.025s
-- 0.025s
-- 0.027s

```


```
-- 解决方案二：看上去很费解
select * from product_cash_predict p where p.evaluated_date
=
(select max(evaluated_date) from product_cash_predict where p.product_id = product_id)
group by product_id;

5					2017-10-31 18:15:22		10001
3					2017-10-31 18:14:59		10002
4					2017-10-31 18:15:09		10003

-- 0.028s
-- 0.019s
-- 0.025s


```

最后，也就是YH实际业务场景的处理方式再来回顾分析一波


```
-- 解决方案三：自连接，根据max结合group by查出最大日期和分组条件product_id，再自连接查出该product_id对应的其他字段

-- 只查最大日期和分组条件product_id，若查其他字段则为不准的数据（group by取分组第一条）
select max(evaluated_date) evalDate, product_id from product_cash_predict GROUP BY product_id

2017-10-31 18:15:22	10001
2017-10-31 18:14:59	10002
2017-10-31 18:15:09	10003

-- 把查出的结果作为p1集
select p1.* from (
	select max(evaluated_date) evalDate, product_id from product_cash_predict GROUP BY product_id
) p1;

-- 然后从根据此结果自连接后查出的结果中取真是的记录值
select p2.* from (
	select max(evaluated_date) evalDate, product_id from product_cash_predict GROUP BY product_id
) p1 LEFT JOIN product_cash_predict p2 on p1.product_id = p2.product_id and p1.evalDate = p2.evaluated_date;

3					2017-10-31 18:14:59		10002
4					2017-10-31 18:15:09		10003
5					2017-10-31 18:15:22		10001
```

### 性能比较

```
explain select * from (select * from product_cash_predict ORDER BY evaluated_date desc) as result group by product_id;

+----+-------------+----------------------+------+---------------+------+---------+------+------+---------------------------------+
| id | select_type | table                | type | possible_keys | key  | key_len | ref  | rows | Extra                           |
+----+-------------+----------------------+------+---------------+------+---------+------+------+---------------------------------+
|  1 | PRIMARY     | <derived2>           | ALL  | NULL          | NULL | NULL    | NULL |    5 | Using temporary; Using filesort |
|  2 | DERIVED     | product_cash_predict | ALL  | NULL          | NULL | NULL    | NULL |    5 | Using filesort                  |
+----+-------------+----------------------+------+---------------+------+---------+------+------+---------------------------------+

explain select * from product_cash_predict p where p.evaluated_date =
(select max(evaluated_date) from product_cash_predict where p.product_id = product_id)
group by product_id;

+----+--------------------+----------------------+------+---------------+------+---------+------+------+----------------------------------------------+
| id | select_type        | table                | type | possible_keys | key  | key_len | ref  | rows | Extra                                        |
+----+--------------------+----------------------+------+---------------+------+---------+------+------+----------------------------------------------+
|  1 | PRIMARY            | p                    | ALL  | NULL          | NULL | NULL    | NULL |    5 | Using where; Using temporary; Using filesort |
|  2 | DEPENDENT SUBQUERY | product_cash_predict | ALL  | NULL          | NULL | NULL    | NULL |    5 | Using where                                  |
+----+--------------------+----------------------+------+---------------+------+---------+------+------+----------------------------------------------+

explain select p2.* from (
	select max(evaluated_date) evalDate, product_id from product_cash_predict GROUP BY product_id
) p1 LEFT JOIN product_cash_predict p2 on p1.product_id = p2.product_id and p1.evalDate = p2.evaluated_date;

+----+-------------+----------------------+------+---------------+------+---------+------+------+----------------------------------------------------+
| id | select_type | table                | type | possible_keys | key  | key_len | ref  | rows | Extra                                              |
+----+-------------+----------------------+------+---------------+------+---------+------+------+----------------------------------------------------+
|  1 | PRIMARY     | <derived2>           | ALL  | NULL          | NULL | NULL    | NULL |    5 | NULL                                               |
|  1 | PRIMARY     | p2                   | ALL  | NULL          | NULL | NULL    | NULL |    5 | Using where; Using join buffer (Block Nested Loop) |
|  2 | DERIVED     | product_cash_predict | ALL  | NULL          | NULL | NULL    | NULL |    5 | Using temporary; Using filesort                    |
+----+-------------+----------------------+------+---------------+------+---------+------+------+----------------------------------------------------+

```

### END

致谢：问题提出者，同时也是本文校对者@YH

总结：虽然可能这只是SQL查询中的一个小知识点，但不经分析，直接使用，可能会给业务带来不必要的坑，正所谓磨刀不误砍柴工，对常用技术的深入理解应该成为一个技术人的日常习惯。
