### MySQL命令示例

* ##### 查看索引

```
show index from hist_5m_data;
```

* ##### explain命令

[https://www.kancloud.cn/thinkphp/mysql-design-optimalize/39319](https://www.kancloud.cn/thinkphp/mysql-design-optimalize/39319)

EXPLAIN命令是查询性能优化不可缺少的一部分，EXPLAIN 显示了 MySQL 如何使用索引来处理 SELECT 语句以及连接表。可以帮助选择更好的索引和写出更优化的查询语句

```
mysql> explain select * from hist_day_data where code = '600000';
+----+-------------+---------------+------------+------+---------------+---------+---------+-------+------+----------+-------+
| id | select_type | table         | partitions | type | possible_keys | key     | key_len | ref   | rows | filtered | Extra |
+----+-------------+---------------+------------+------+---------------+---------+---------+-------+------+----------+-------+
|  1 | SIMPLE      | hist_day_data | NULL       | ref  | PRIMARY       | PRIMARY | 3       | const |  963 |   100.00 | NULL  |
+----+-------------+---------------+------------+------+---------------+---------+---------+-------+------+----------+-------+
1 row in set, 1 warning (0.57 sec)
mysql> explain select * from hist_day_data where date = '2018-09-04' and code = '600000';
+----+-------------+---------------+------------+-------+---------------+---------+---------+-------------+------+----------+-------+
| id | select_type | table         | partitions | type  | possible_keys | key     | key_len | ref         | rows | filtered | Extra |
+----+-------------+---------------+------------+-------+---------------+---------+---------+-------------+------+----------+-------+
|  1 | SIMPLE      | hist_day_data | NULL       | const | PRIMARY       | PRIMARY | 6       | const,const |    1 |   100.00 | NULL  |
+----+-------------+---------------+------------+-------+---------------+---------+---------+-------------+------+----------+-------+
1 row in set, 1 warning (0.07 sec)
mysql> explain select * from hist_day_data where date = '2018-09-04';
+----+-------------+---------------+------------+------+---------------+------+---------+------+---------+----------+-------------+
| id | select_type | table         | partitions | type | possible_keys | key  | key_len | ref  | rows    | filtered | Extra       |
+----+-------------+---------------+------------+------+---------------+------+---------+------+---------+----------+-------------+
|  1 | SIMPLE      | hist_day_data | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 1886392 |    10.00 | Using where |
+----+-------------+---------------+------------+------+---------------+------+---------+------+---------+----------+-------------+
1 row in set, 1 warning (0.00 sec)
mysql>
mysql> explain select * from hist_30m_data where date = '2018-09-05 15:00:00' and  ma5 < ma10 * 0.9 and ma10 < ma20 * 0.9 and low > 10;
+----+-------------+---------------+------------+------+---------------+------+---------+------+---------+----------+-------------+
| id | select_type | table         | partitions | type | possible_keys | key  | key_len | ref  | rows    | filtered | Extra       |
+----+-------------+---------------+------------+------+---------------+------+---------+------+---------+----------+-------------+
|  1 | SIMPLE      | hist_30m_data | NULL       | ALL  | NULL          | NULL | NULL    | NULL | 1238824 |     0.37 | Using where |
+----+-------------+---------------+------------+------+---------------+------+---------+------+---------+----------+-------------+
1 row in set, 1 warning (0.00 sec)

mysql> show warnings;
```

* ##### 查看警告信息

```
mysql> show warnings;
```

* ##### help命令

[https://www.ilanni.com/?p=8157](https://www.ilanni.com/?p=8157)

```
mysql> help;
mysql> help contents;
mysql> help table maintenance;
mysql> help check table;
mysql> help create;
mysql> help create index;
mysql> help create table;
```

* ##### 创建索引

```
alter table hist_5m_data add index date_index(date);
alter table hist_15m_data add index date_index(date);
alter table hist_30m_data add index date_index(date);
alter table hist_60m_data add index date_index(date);
alter table hist_day_data add index date_index(date);
alter table hist_week_data add index date_index(date);
alter table hist_month_data add index date_index(date);
```

* ##### 查看表结构

```
mysql> describe hist_30m_data;
```

* ##### 查看表创建命令

```
mysql> show create table hist_30m_data;
```

* ##### 最左前缀索引选择经验法则（高性能MySQL, P197）

```
mysql> select count(distinct date)/count(*), count(distinct code)/count(*), count(*) from hist_30m_data;
+-------------------------------+-------------------------------+----------+
| count(distinct date)/count(*) | count(distinct code)/count(*) | count(*) |
+-------------------------------+-------------------------------+----------+
|                        0.0015 |                        0.0025 |  1397377 |
+-------------------------------+-------------------------------+----------+
1 row in set (1.04 sec)
```

* ##### 查看表的条目数

[https://zhuanlan.zhihu.com/p/28397595](https://zhuanlan.zhihu.com/p/28397595)

[https://dev.mysql.com/doc/refman/5.7/en/group-by-functions.html\#function\_count](https://dev.mysql.com/doc/refman/5.7/en/group-by-functions.html#function_count)

```
InnoDB handles SELECT COUNT(*) and SELECT COUNT(1) operations in the same way. 
There is no performance difference. 

select count(*) from hist_5m_data;
select count(*) from hist_15m_data;
select count(*) from hist_30m_data;
select count(*) from hist_60m_data;
select count(*) from hist_day_data;
select count(*) from hist_week_data;
select count(*) from hist_month_data;

select count(1) from hist_day_data;
select count(1) from hist_week_data;
select count(1) from hist_month_data;
```

* ##### 日期加减操作

[https://www.yiibai.com/mysql/today.html](https://www.yiibai.com/mysql/today.html)

[http://www.w3school.com.cn/sql/sql\_dates.asp](http://www.w3school.com.cn/sql/sql_dates.asp)

[http://www.w3school.com.cn/sql/func\_date\_sub.asp](http://www.w3school.com.cn/sql/func_date_sub.asp)

[http://wiki.jikexueyuan.com/project/mysql/useful-functions/time-functions.html](http://wiki.jikexueyuan.com/project/mysql/useful-functions/time-functions.html)

```
mysql> select now();
+---------------------+
| now()               |
+---------------------+
| 2018-12-07 16:30:23 |
+---------------------+
1 row in set (0.02 sec)

mysql> select curdate();
+------------+
| curdate()  |
+------------+
| 2018-12-07 |
+------------+
1 row in set (0.00 sec)

mysql> select date_sub(curdate(), interval 2 year);
+--------------------------------------+
| date_sub(curdate(), interval 2 year) |
+--------------------------------------+
| 2016-12-07                           |
+--------------------------------------+
1 row in set (0.00 sec)

mysql> select date_sub(curdate(), interval 2 month);
+---------------------------------------+
| date_sub(curdate(), interval 2 month) |
+---------------------------------------+
| 2018-10-07                            |
+---------------------------------------+
1 row in set (0.02 sec)

mysql> select date_sub(curdate(), interval 2 day);
+-------------------------------------+
| date_sub(curdate(), interval 2 day) |
+-------------------------------------+
| 2018-12-05                          |
+-------------------------------------+
1 row in set (0.00 sec)

mysql> select date_add(curdate(), interval 2 year);
+--------------------------------------+
| date_add(curdate(), interval 2 year) |
+--------------------------------------+
| 2020-12-07                           |
+--------------------------------------+
1 row in set (0.00 sec)

mysql> select date_add(curdate(), interval 2 month);
+---------------------------------------+
| date_add(curdate(), interval 2 month) |
+---------------------------------------+
| 2019-02-07                            |
+---------------------------------------+
1 row in set (0.01 sec)

mysql> select date_add(curdate(), interval 2 day);
+-------------------------------------+
| date_add(curdate(), interval 2 day) |
+-------------------------------------+
| 2018-12-09                          |
+-------------------------------------+
1 row in set (0.00 sec)

mysql> select curdate() - 365;
+-----------------+
| curdate() - 365 |
+-----------------+
|        20180842 |
+-----------------+
1 row in set (0.00 sec)

mysql> select concat(date_sub(curdate(), interval 1 day), ' 15:00:00');
+----------------------------------------------------------+
| concat(date_sub(curdate(), interval 1 day), ' 15:00:00') |
+----------------------------------------------------------+
| 2018-12-06 15:00:00                                      |
+----------------------------------------------------------+
1 row in set (0.00 sec)
```

* ##### 查询某一字段中重复的个数并且按照次数排序

[https://blog.csdn.net/xqhlsjslcy/article/details/53389222](https://blog.csdn.net/xqhlsjslcy/article/details/53389222) 

```
mysql> select code, count(*) as code_count from hist_extend_day_data group by code order by code_count desc;
+--------+------------+
| code   | code_count |
+--------+------------+
| 000001 |       1157 |
| 000762 |       1156 |
| 000880 |       1156 |
| 002561 |       1156 |
| 000402 |       1156 |
| 300321 |       1156 |
| 600616 |       1155 |
| 600004 |       1155 |
| 300357 |       1155 |
......

mysql> select code, count(1) as code_count from hist_extend_day_data group by code order by code_count desc;
+--------+------------+
| code   | code_count |
+--------+------------+
| 000001 |       1157 |
| 000402 |       1156 |
| 000762 |       1156 |
| 002232 |       1156 |
| 300321 |       1156 |
| 002271 |       1156 |
| 002561 |       1156 |
| 000880 |       1156 |
......
```

* ##### 查询某一字段非重复次数

[https://www.crifan.com/mysql\_check\_all\_unique\_count/](https://www.crifan.com/mysql_check_all_unique_count/) 

[https://www.w3cschool.cn/mysql/mysql-handling-duplicates.html](https://www.w3cschool.cn/mysql/mysql-handling-duplicates.html)

[https://www.runoob.com/mysql/mysql-handling-duplicates.html](https://www.runoob.com/mysql/mysql-handling-duplicates.html)

```
mysql> select DISTINCT code from hist_extend_day_data;
+--------+
| code   |
+--------+
| 000516 |
| 000662 |
| 002049 |
| 002122 |
| 002747 |
......

mysql> select code from hist_extend_day_data group by code having count(1) > 1;
+--------+
| code   |
+--------+
| 000001 |
| 000002 |
| 000004 |
| 000005 |
| 000046 |
| 000048 |
| 000049 |
| 000050 |
......

mysql> select count(DISTINCT code) as code_count, count(*) as line_count from hist_extend_day_data;
+------------+------------+
| code_count | line_count |
+------------+------------+
|        334 |     245859 |
+------------+------------+
1 row in set (0.33 sec)
```

* ##### MySQL复制表

[https://www.runoob.com/mysql/mysql-clone-tables.html](https://www.runoob.com/mysql/mysql-clone-tables.html)

[https://blog.csdn.net/xiao190128/article/details/54890367](https://blog.csdn.net/xiao190128/article/details/54890367)

第一、只复制表结构到新表

```
create table 新表 select * from 旧表 where 1=2
或者
create table 新表 like 旧表
```

第二、复制表结构及数据到新表

```
create table新表 select * from 旧表
```

另一种完整复制表的方法:

```
CREATE TABLE targetTable LIKE sourceTable;
INSERT INTO targetTable SELECT * FROM sourceTable;
```

其他:

可以拷贝一个表中其中的一些字段:

```
CREATE TABLE newadmin AS
(
    SELECT username, password FROM admin
)
```

可以将新建的表的字段改名:

```
CREATE TABLE newadmin AS
(
    SELECT id, username AS uname, password AS pass FROM admin
)
```

可以拷贝一部分数据:

```
CREATE TABLE newadmin AS
(
    SELECT * FROM admin WHERE LEFT(username,1) = 's'
)
```

可以在创建表的同时定义表中的字段信息:

```
CREATE TABLE newadmin
(
    id INTEGER NOT NULL AUTO_INCREMENT PRIMARY KEY
)
AS
(
    SELECT * FROM admin
)
```

命令备份

    CREATE TABLE `hist_day_data_with_index` (
      `id` bigint NOT NULL PRIMARY KEY AUTO_INCREMENT,
      `date` date NOT NULL,
      `code` char(6) NOT NULL,
      `open` float NOT NULL,
      `high` float NOT NULL,
      `close` float NOT NULL,
      `low` float NOT NULL,
      `volume` float NOT NULL,
      `price_change` float NOT NULL,
      `p_change` float NOT NULL,
      `ma5` float NOT NULL,
      `ma10` float NOT NULL,
      `ma20` float NOT NULL,
      `v_ma5` float NOT NULL,
      `v_ma10` float NOT NULL,
      `v_ma20` float NOT NULL,
      `turnover` float NOT NULL DEFAULT '0',
      UNIQUE `code_date_index` (`code`,`date`),
      INDEX `date_index` (`date`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    CREATE TABLE `hist_day_data_with_index` (
      `id` bigint NOT NULL AUTO_INCREMENT,
      `date` date NOT NULL,
      `code` char(6) NOT NULL,
      `open` float NOT NULL,
      `high` float NOT NULL,
      `close` float NOT NULL,
      `low` float NOT NULL,
      `volume` float NOT NULL,
      `price_change` float NOT NULL,
      `p_change` float NOT NULL,
      `ma5` float NOT NULL,
      `ma10` float NOT NULL,
      `ma20` float NOT NULL,
      `v_ma5` float NOT NULL,
      `v_ma10` float NOT NULL,
      `v_ma20` float NOT NULL,
      `turnover` float NOT NULL DEFAULT '0',
      PRIMARY KEY (`id`),
      UNIQUE `code_date_index` (`code`,`date`),
      INDEX `date_index` (`date`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;


    insert into hist_day_data_with_index (date, code, open, high, close, low, volume, price_change, p_change, ma5, ma10, ma20, v_ma5, v_ma10, v_ma20, turnover) select * from hist_day_data;

    CREATE TABLE `personal_info_table` (
      `id` bigint NOT NULL AUTO_INCREMENT,
      `name` vchar(12) NOT NULL,
      `age` tinyint NOT NULL,
      `origin` vchar(4) NOT NULL,
      `home_addr` vchar(64) NOT NULL,
      `personal_id` char(18) not NULL,
      PRIMARY KEY (`id`),
      UNIQUE `name_id_index` (`name`,`personal_id`),
      INDEX `origin_index` (`origin`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    CREATE TABLE `personal_info_table` (
      `id` bigint NOT NULL AUTO_INCREMENT,
      `name` vchar(12) NOT NULL,
      `age` tinyint NOT NULL,
      `origin` vchar(4) NOT NULL,
      `home_addr` vchar(64) NOT NULL,
      `personal_id` char(18) not NULL,
      PRIMARY KEY (`id`),
      UNIQUE KEY `name_id_index` (`name`,`personal_id`),
      INDEX `origin_index` (`origin`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    CREATE TABLE `personal_info_table` (
      `id` bigint NOT NULL AUTO_INCREMENT,
      `name` vchar(12) NOT NULL,
      `age` tinyint NOT NULL,
      `origin` vchar(4) NOT NULL,
      `home_addr` vchar(64) NOT NULL,
      `personal_id` char(18) not NULL,
      PRIMARY KEY (`id`),
      UNIQUE INDEX `name_id_index` (`name`,`personal_id`),
      INDEX `origin_index` (`origin`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    CREATE TABLE `personal_info_table` (
      `id` bigint NOT NULL AUTO_INCREMENT,
      `name` vchar(12) NOT NULL,
      `age` tinyint NOT NULL,
      `origin` vchar(4) NOT NULL,
      `home_addr` vchar(64) NOT NULL,
      `personal_id` char(18) not NULL,
      PRIMARY KEY (`id`),
      UNIQUE `name_id_index` (`name`,`personal_id`),
      KEY `origin_index` (`origin`)
    ) ENGINE=InnoDB DEFAULT CHARSET=utf8;

    以上4条创建personal_info_table表的语句是一样的，在MySQL中，KEY和INDEX都表示的是索引，实际使用可以互换。

##### MySQL生成随机测试数据

[https://www.cnblogs.com/tmdba/p/6444855.html](https://www.cnblogs.com/tmdba/p/6444855.html)

[https://blog.csdn.net/qq\_16946803/article/details/81870174](https://blog.csdn.net/qq_16946803/article/details/81870174)

[https://blog.csdn.net/dennis211/article/details/78076399](https://blog.csdn.net/dennis211/article/details/78076399)

[https://blog.csdn.net/qq\_36608163/article/details/81504314](https://blog.csdn.net/qq_36608163/article/details/81504314)

[https://blog.csdn.net/lightofmiracle/article/details/74004511](https://blog.csdn.net/lightofmiracle/article/details/74004511)

##### MySQL随机读数据

http://ourmysql.com/archives/524

https://www.zhihu.com/question/29824101

* ##### 
* ##### 
* ##### 
* ##### 
* ##### 
* ##### 
* ##### 
* ##### 



