### MySQL命令示例

* ##### 查看索引

```
show index from hist_5m_data;
```

* ##### explain命令

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
mysql> alter table hist_30m_data add index date_index(date);
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

* ##### 



