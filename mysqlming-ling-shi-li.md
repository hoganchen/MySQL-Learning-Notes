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
```

* ##### 查询某一字段非重复次数

[https://www.crifan.com/mysql\_check\_all\_unique\_count/](https://www.crifan.com/mysql_check_all_unique_count/) 

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


mysql> select count(DISTINCT code) as code_count, count(*) as line_count from hist_extend_day_data;
+------------+------------+
| code_count | line_count |
+------------+------------+
|        334 |     245859 |
+------------+------------+
1 row in set (0.33 sec)
```

* ##### 
* ##### 
* ##### 
* ##### 
* ##### 
* ##### 
* ##### 
* ##### 
* ##### 
* ##### 
* ##### 



