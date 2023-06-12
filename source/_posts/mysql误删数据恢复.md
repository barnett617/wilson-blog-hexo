---
title: mysql误删数据恢复
date: 2018-02-01 15:14:45
update: 2018-02-01 15:14:45
categories: 数据库
tags: 
 - mysql
---

对于MySQL误删数据，如何通过二进制日志进行数据查找和恢复

<!--more-->

### 数据恢复（MySQL传统方式）

#### 前置条件

MySQL数据恢复的大前提基于MySQL核心配置文件（Linux下的my.cnf、Windows下的my.ini）中开启了Binary log——MySQL四种日志类型（Error log、General Query log、Binary log和Slow Query log）之一

可通过以下命令查看二进制日志相关配置情况

```
mysql> show variables like '%log_bin%'
```

开启方式为在MySQL配置文件中的[mysqld]组中添加

```
log-bin[=name]
```

或者在启动MySQL服务时添加启动参数--log-bin[=name]

此处name为自定义日志文件名，有以下特点：

1. 是可选项

2. 即使添加了后缀名，也会被忽略

3. 可以设置绝对路径

若不设置，默认项会是datadir/log-basename-bin或datadir/mysql-bin或者datadir/mariadb-bin（后两者出现在你未设置log-basename，然后具体是mysql-bin还是mariadb-bin决定于你使用的MySQL服务器版本），datadir为你在MySQL核心配置文件中设置的数据目录

**官方推荐**配置方式：指定log-basename或指定log-bin的name选项，这么做是为了确保当计算机主机名改变时复制（replication）不会受影响而停止（比如发生于主从备份）

存储二进制日志的目录将会包含一个二进制日志**索引**文件（包含所有二进制日志文件的有序列表）和二进制日志

二进制索引文件若不指定名称，会和二进制日志文件使用相同的name，后缀为index，也可手动指定其名称，通过

```
log-bin-index[=filename]
```

进行配置，这也是可选项

二进制日志文件可通过

```
mysql> show binary logs;
```

进行查看，也可直接进入存储二进制日志文件的目录查看，序号越大的是越新产生的，序号越小的是越老的日志文件

二进制日志文件的产生于以下三种情况：

1. MySQL每次启动时产生一个新的二进制日志文件

2. 或者日志被flush

3. 或者当前日志文件达到文件设定的容量最大值（max_binlog_size）

> max_binlog_size范围为4K-1G，默认为1G（但不限于此），当事务比较小时，binlog在接近1G时会flush，并生成新的binlog，但同个事务不能跨binlog存储，所以当接近1G时如果有一个大事务，则产生的日志记录会记录在当前binlog，并产生大于1G的binlog，当事务执行完之后，才会切换binlog

拥有SUPER权限的客户端可以停用或者重新启用当前会话的二进制日志文件，通过

```
SET sql_log_bin = 0;
```

```
SET sql_log_bin = 1;
```

二进制文件主要目的：

1. 用于复制：二进制文件一般存在于复制宿主机上，作为操作的记录，用于发送给从机（二进制日志很多细节内容都旨在为此服务）。主机向从机发送二进制日志中的操作事件，以使得从机能够执行这些事件保证拥有与主机相同的数据变更

2. 数据恢复：备份文件恢复后，可通过二进制日志文件找到备份的任一个时间点的数据

二进制日志文件共有三种格式

1. statement-based（默认）

2. row-based

3. mix

无论日志文件哪种格式，二进制日志都存储以二进制数据而非纯文本，所以一般的文本编辑器无法直接查看（不过MariaDB有一个命令行工具mysqlbinlog可以将二进制日志处理成纯文本）

二进制日志通过系统变量`binlog_format`决定

可在运行MySQL服务时通过添加以下一种参数进行设置

```
--binlog-format=STATEMENT
```

```
--binlog-format=ROW
```

```
--binlog-format=MIXED
```

或进行全局配置（需要SUPER权限）或进行会话级配置，方式如下：

```
SET GLOBAL binlog_format=ROW;
```

```
SET SESSION binlog_format=MIXED;
```

```
SET binlog_format=STATEMENT;
```

##### 查找二进制日志文件

使用以下命令进行相应查找和查看

1. 查看当前正在写入的binlog
```
mysql> show master status;
```

2. 查看binlog日志文件列表（所有二进制日志文件）
```
mysql> show binary logs;
```

3. 查看指定binlog文件
```
mysql> show binlog events in 'binlog_name';
```

4. 查看第一个binlog
```
mysql> show binlog events
```

##### 使用mysqlbinlog工具

注意点：

1. 不要查看正在写入的binlog

2. 不要使用--force参数强制访问

3. 若binlog格式为行模式，需要加-vv参数（行模式下DML加密存储，使用-v参数进行解析）

###### mysqlbinlog使用

使用mysqlbinlog的方式如下：

```
shell> mysqlbinlog [options] log_file
```

> 当使用mysqlbinlog时，可能会遇到一种情况会提示“unknown variable 'default-character-set=utf8'”，原因是MySQL配置文件中[client]组中配置了default-character-set=utf8，这种情况可通过为mysqlbinlog添加--no-defaults参数解决

如果binlog格式为statement（statement指类似CREATE、ALTER、INSERT、UPDATE、DELETE这样的操作，SELECT和SHOW这样的操作将不会被记录），则mysqlbinlog看到的日志包括SQL statament、执行这条statement的severID、时间戳（timestamp）以及执行这个statement花费了多少时间

如果binlog格式为row，则mysqlbinlog不会包含SQL的statement，取而代之的是显示每一行数据是如何改变的

mysqlbinlog的输出可直接作为对于mysql客户端的输入，以重新执行二进制日志中的statements（用于MySQL服务器宕机时作恢复），操作如下：

```
shell> mysqlbinlog binlog-file | mysql -u root -p
```

也可使用如下命令将mysqlbinlog的输出重定向（redirect）当其他文件中，进行编辑和修改

```
shell> mysqlbinlog -r filename binlog-filename
```

然后将修改后的文件再向MySQL客户端进行输入

```
shell> mysql -u root -p < filename
```

可使用一个数据库连接执行多个日志文件

```
shell> mysqlbinlog file1 file2 ... | mssql -u root -p
```

> 当同时给一个连接执行多个日志文件时，如果多个日志中包含CREATE TEMPORARY TABLE这样的statement，临时表会在MySQL客户端连接终止（terminate）时删掉（drop），所以如果在同一时刻处理多个日志文件，并且其中一个日志文件创建了一个临时表，随后的日志文件连到（refer to）了这个临时表，则会得到“unknown table”错误

如果需要将多个文件合并为一个文件进行编辑，然后，可如下操作：

```
shell> mysqlbinlog master-bin.000001 > /tmp/file.sql
shell> mysqlbinlog master-bin.000002 >> /tmp/file.sql
shell> mysql -u root -p -e "source /tmp/file.sql"
```

直接在命令行通过`show binlog events`查看的binlog结构如下


| Log_name | Pos | Event_type | Server_id | End_log_pos | Info |
| -- | -- | -- | -- | -- | -- |
| master-bin.000001 | 4 | Format_desc | 1 | 120 | Server ver: 5.6.31-log, Binlog ver: 4 |
| master-bin.000001 | 120 | Query | 1 | 209 | BEGIN |
| master-bin.000001 | 209 | Query       |         1 |         354 | use `wms_biz_2`; DELETE FROM `trade_wave_policy` WHERE (`POLICY_ID`='8700007') |
| master-bin.000001 | 354 | Xid         |         1 |         385 | COMMIT /* xid=45 */                                                            |
| master-bin.000001 | 385 | Stop        |         1 |         408 |  -- |

而使用mysqlbinlog导出的文件结构类似：

```
/*!50530 SET @@SESSION.PSEUDO_SLAVE_MODE=1*/;
/*!40019 SET @@session.max_insert_delayed_threads=0*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#180130 19:13:59 server id 1  end_log_pos 120 CRC32 0x870729d1 	Start: binlog v 4, server v 5.6.31-log created 180130 19:13:59 at startup
ROLLBACK/*!*/;
BINLOG '
d1NwWg8BAAAAdAAAAHgAAAAAAAQANS42LjMxLWxvZwAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
AAAAAAAAAAAAAAAAAAB3U3BaEzgNAAgAEgAEBAQEEgAAXAAEGggAAAAICAgCAAAACgoKGRkAAdEp
B4c=
'/*!*/;
# at 120
#180130 19:30:27 server id 1  end_log_pos 209 CRC32 0xad90bdf5 	Query	thread_id=8	exec_time=0	error_code=0
SET TIMESTAMP=1517311827/*!*/;
SET @@session.pseudo_thread_id=8/*!*/;
SET @@session.foreign_key_checks=1, @@session.sql_auto_is_null=0, @@session.unique_checks=1, @@session.autocommit=1/*!*/;
SET @@session.sql_mode=1075838976/*!*/;
SET @@session.auto_increment_increment=1, @@session.auto_increment_offset=1/*!*/;
/*!\C utf8mb4 *//*!*/;
SET @@session.character_set_client=45,@@session.collation_connection=45,@@session.collation_server=33/*!*/;
SET @@session.lc_time_names=0/*!*/;
SET @@session.collation_database=DEFAULT/*!*/;
BEGIN
/*!*/;
# at 209
#180130 19:30:27 server id 1  end_log_pos 354 CRC32 0xa8518d8a 	Query	thread_id=8	exec_time=0	error_code=0
use `wms_biz_2`/*!*/;
SET TIMESTAMP=1517311827/*!*/;
DELETE FROM `trade_wave_policy` WHERE (`POLICY_ID`='8700008')
/*!*/;
# at 354
#180130 19:30:27 server id 1  end_log_pos 385 CRC32 0xc49f56b6 	Xid = 28
COMMIT/*!*/;
# at 385
#180130 19:46:24 server id 1  end_log_pos 474 CRC32 0x6b652f60 	Query	thread_id=11	exec_time=0	error_code=0
SET TIMESTAMP=1517312784/*!*/;
BEGIN
/*!*/;
```

#### 数据恢复（ali-RDS）

（类似于Git或SVN的分支管理，只不过这里是对数据库实例进行克隆分支，进行修改，再合并主干）

1. 进入RDS控制台，选择【备份恢复】，点击克隆实例

2. 选择按时间点进行还原（克隆一份截止到所填时间点的拷贝，即做删除操作之前的时间点）

3. 克隆出一个新实例，找到误删之前的数据，并恢复到主实例上

或通过ali-RDS的DMS进行数据追踪（按条件查询binlog），找到误删操作的SQL进行相应恢复

### 参考链接：

- <a href="https://mariadb.com/kb/en/library/activating-the-binary-log/">官方推荐binlog启用配置</a>

- <a href="https://mariadb.com/kb/en/library/binary-log-formats/">MySQL二进制文件格式</a>

- <a href="https://dev.mysql.com/doc/refman/5.7/en/mysqlbinlog.html">MySQL官方文档binlog章节</a>

- <a href="https://dev.mysql.com/doc/internals/en/binary-log.html">https://dev.mysql.com/doc/internals/en/binary-log.html</a>

- <a href="https://mariadb.com/kb/en/library/mysqlbinlog-options/">mysqlbinlog可选参数</a>

- <a href="https://dev.mysql.com/doc/internals/en/event-header-fields.html">mysqlbinlog输出的文件结构</a>

- <a href="https://bash.cyberciti.biz/guide/Source_command">source命令</a>

- <a href="http://blog.csdn.net/zdy0_2004/article/details/65629247">http://blog.csdn.net/zdy0_2004/article/details/65629247</a>

- <a href="https://help.aliyun.com/knowledge_detail/51989.html">阿里云RDS怎样恢复误删除的数据</a>

- <a href="https://xcoder.in/2015/08/10/mysql-binlog-try/">https://xcoder.in/2015/08/10/mysql-binlog-try/</a>
