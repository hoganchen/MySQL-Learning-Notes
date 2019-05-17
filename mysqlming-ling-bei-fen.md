### MySQL命令备份

* ##### 查询命令

```
select * from hist_30m_data where date = '2018-09-05 15:00:00' and  ma5 < ma10 * 0.9 and ma10 < ma20 * 0.9 and low > 10;

select * from hist_day_data where date = '2018-08-24' and  ma5 < ma10 * 0.9 and low > 15;

select * from hist_day_data where date = '2018-09-03' and  volume < v_ma5 * 0.8 and v_ma5 < v_ma10 * 0.8 and low > 30;

select * from hist_60m_data where date = '2018-09-03 15:00:00' and  ma5 < ma10 * 0.95 and ma10 < ma20 * 0.95 and low > 10;

select code from hist_15m_data where date >= '2018-09-05 09:45:00' and date <= '2018-09-05 15:00:00' and  ma5 < ma10 * 0.95 and ma10 < ma20 * 0.95 group by code;
```

* ##### 查询某列最大值所在的行

[https://blog.csdn.net/lanyang123456/article/details/61647140](https://blog.csdn.net/lanyang123456/article/details/61647140)

[http://database.51cto.com/art/201009/225462\_all.htm](http://database.51cto.com/art/201009/225462_all.htm)

[https://wiki.jikexueyuan.com/project/mysql/useful-functions/min.html](https://wiki.jikexueyuan.com/project/mysql/useful-functions/min.html)

[https://blog.csdn.net/jgmydsai/article/details/18358115](https://blog.csdn.net/jgmydsai/article/details/18358115)

[https://codeday.me/bug/20170218/1297.html](https://codeday.me/bug/20170218/1297.html)

[https://stackoverflow.com/questions/55953765/how-to-select-the-row-where-the-date-is-null-or-max-date-for-that-group](https://stackoverflow.com/questions/55953765/how-to-select-the-row-where-the-date-is-null-or-max-date-for-that-group)

[https://stackoverflow.com/questions/tagged/greatest-n-per-group](https://stackoverflow.com/questions/tagged/greatest-n-per-group)

```
select * from hist_extend_day_data where (code, volume) in (select code, max(volume) from hist_extend_day_data group by code) and date > '2019-05-01';
```

* ##### create\_table\_cmd

```
~/anaconda3/bin/pip install tushare
~/anaconda3/bin/conda install -c anaconda mysql-connector-python


alter user 'root'@'localhost' identified by 'rO0t@stck5ql*';

create database stock default character set=utf8;
create user stck identified by 'stck&sql';
grant all on stock.* to stck;


create table code_data
(
  date date not null,
  code mediumint(6) zerofill not null,
  name varchar(6) not null,
  primary key (code)
) engine=innodb default charset=utf8;


create table today_code_data
(
  date date not null,
  code mediumint(6) zerofill not null,
  name varchar(6) not null,
  primary key (code)
) engine=innodb default charset=utf8;


create table basics_data
(
  date date not null,
  code mediumint(6) zerofill not null,
  name varchar(6) not null,
  industry varchar(6) not null,
  area varchar (4) not null,
  pe float not null,
  outstanding float not null,
  totals float not null,
  totalAssets float not null,
  liquidAssets float not null,
  fixedAssets float not null,
  reserved float not null,
  reservedPerShare float not null,
  esp float not null,
  bvps float not null,
  pb float not null,
  timeToMarket int not null,
  undp float not null,
  perundp float not null,
  rev float not null,
  profit float not null,
  gpr float not null,
  npr float not null,
  holders int not null,
  primary key (code)
) engine=innodb default charset=utf8;


create table k_day_data
(
  date date not null,
  code mediumint(6) zerofill not null,
  open float not null,
  close float not null,
  high float not null,
  low float not null,
  volume bigint not null,
  primary key (code, date)
) engine=innodb default charset=utf8;


create table k_week_data like k_day_data;
create table k_month_data like k_day_data;


'''
create table k_5m_data
(
  datetime datetime not null,
  code mediumint(6) zerofill not null,
  open float not null,
  close float not null,
  high float not null,
  low float not null,
  volume bigint not null,
  primary key (code, datetime)
) engine=innodb default charset=utf8;
'''

create table k_5m_data
(
  date datetime not null,
  code mediumint(6) zerofill not null,
  open float not null,
  close float not null,
  high float not null,
  low float not null,
  volume bigint not null,
  primary key (code, date)
) engine=innodb default charset=utf8;


create table k_15m_data like k_5m_data;
create table k_30m_data like k_5m_data;
create table k_60m_data like k_5m_data;


create table hist_day_data
(
  date date not null,
  code mediumint(6) zerofill not null,
  open float not null,
  high float not null,
  close float not null,
  low float not null,
  volume float not null,
  price_change float not null,
  p_change float not null,
  ma5 float not null,
  ma10 float not null,
  ma20 float not null,
  v_ma5 float not null,
  v_ma10 float not null,
  v_ma20 float not null,
  turnover float not null,
  primary key (code, date)
) engine=innodb default charset=utf8;


create table hist_week_data like hist_day_data;
create table hist_month_data like hist_day_data;


'''
create table hist_5m_data
(
  datetime datetime not null,
  code mediumint(6) zerofill not null,
  open float not null,
  high float not null,
  close float not null,
  low float not null,
  volume float not null,
  price_change float not null,
  p_change float not null,
  ma5 float not null,
  ma10 float not null,
  ma20 float not null,
  v_ma5 float not null,
  v_ma10 float not null,
  v_ma20 float not null,
  turnover float not null,
  primary key (code, datetime)
) engine=innodb default charset=utf8;
'''

create table hist_5m_data
(
  date datetime not null,
  code mediumint(6) zerofill not null,
  open float not null,
  high float not null,
  close float not null,
  low float not null,
  volume float not null,
  price_change float not null,
  p_change float not null,
  ma5 float not null,
  ma10 float not null,
  ma20 float not null,
  v_ma5 float not null,
  v_ma10 float not null,
  v_ma20 float not null,
  turnover float not null,
  primary key (code, date)
) engine=innodb default charset=utf8;


create table hist_15m_data like hist_5m_data;
create table hist_30m_data like hist_5m_data;
create table hist_60m_data like hist_5m_data;


create table h_data
(
  date date not null,
  code mediumint(6) zerofill not null,
  open float not null,
  high float not null,
  close float not null,
  low float not null,
  volume bigint not null,
  amount bigint not null,
  primary key (code, date)
) engine=innodb default charset=utf8;


create table trade_day_data
(
  date date not null,
  code mediumint(6) zerofill not null,
  name varchar(6) not null,
  changepercent float not null,
  trade float not null,
  open float not null,
  high float not null,
  low float not null,
  settlement float not null,
  volume bigint not null,
  turnoverratio double not null,
  amount bigint not null,
  per double not null,
  pb double not null,
  mktcap double not null,
  nmc double not null,
  primary key (code, date)
) engine=innodb default charset=utf8;


create table trade_minute_data
(
  datetime datetime not null,
  code mediumint(6) zerofill not null,
  name varchar(6) not null,
  changepercent float not null,
  trade float not null,
  open float not null,
  high float not null,
  low float not null,
  settlement float not null,
  volume bigint not null,
  turnoverratio double not null,
  amount bigint not null,
  per double not null,
  pb double not null,
  mktcap double not null,
  nmc double not null,
  primary key (code, datetime)
) engine=innodb default charset=utf8;


create table industry_data
(
  date date not null,
  code mediumint(6) zerofill not null,
  name varchar(6) not null,
  c_name varchar(6) not null
) engine=innodb default charset=utf8;


create table concept_data
(
  date date not null,
  code mediumint(6) zerofill not null,
  name varchar(6) not null,
  c_name varchar(6) not null
) engine=innodb default charset=utf8;


create table area_data
(
  date date not null,
  code mediumint(6) zerofill not null,
  name varchar(6) not null,
  area varchar(6) not null
) engine=innodb default charset=utf8;


MySQL数据库备份
hogan@ubuntu:/tmp$ mysqlpump -uroot -p'rO0t@stck5ql*' -h127.0.0.1 --single-transaction --default-character-set=utf8 --default-parallelism=5 -B stock | gzip > ./stck_db_hp.sql.gz

hogan@ubuntu:/tmp$ gzip -d stck_db.sql.gz

hogan@ubuntu:/tmp$ mysql -u root -p'rO0t@stck5ql*' < /tmp/stck_db.sql


更新索引(修改主键索引，最左前缀匹配原则)
alter table k_5m_data drop primary key;
alter table k_15m_data drop primary key;
alter table k_30m_data drop primary key;
alter table k_60m_data drop primary key;

alter table k_5m_data add primary key(code, datetime);
alter table k_15m_data add primary key(code, datetime);
alter table k_30m_data add primary key(code, datetime);
alter table k_60m_data add primary key(code, datetime);

alter table k_day_data drop primary key;
alter table k_week_data drop primary key;
alter table k_month_data drop primary key;

alter table k_day_data add primary key(code, date);
alter table k_week_data add primary key(code, date);
alter table k_month_data add primary key(code, date);


alter table hist_5m_data drop primary key;
alter table hist_15m_data drop primary key;
alter table hist_30m_data drop primary key;
alter table hist_60m_data drop primary key;

alter table hist_5m_data add primary key(code, datetime);
alter table hist_15m_data add primary key(code, datetime);
alter table hist_30m_data add primary key(code, datetime);
alter table hist_60m_data add primary key(code, datetime);

alter table hist_day_data drop primary key;
alter table hist_week_data drop primary key;
alter table hist_month_data drop primary key;

alter table hist_day_data add primary key(code, date);
alter table hist_week_data add primary key(code, date);
alter table hist_month_data add primary key(code, date);


更新字段类型
alter table basics_data modify area varchar(4);
alter table area_data modify area varchar(6);

alter table basics_data modify industry varchar(6);
alter table basics_data modify area varchar(4);


更改字段名称和类型(更改列名datetime为date)
alter table hist_5m_data change datetime date datetime not null;
alter table hist_15m_data change datetime date datetime not null;
alter table hist_30m_data change datetime date datetime not null;
alter table hist_60m_data change datetime date datetime not null;


清空表
truncate table hist_5m_data;
truncate table hist_15m_data;
truncate table hist_30m_data;
truncate table hist_60m_data;


添加索引
alter table hist_5m_data add index date_index(date);
alter table hist_15m_data add index date_index(date);
alter table hist_30m_data add index date_index(date);
alter table hist_60m_data add index date_index(date);
alter table hist_day_data add index date_index(date);
alter table hist_week_data add index date_index(date);
alter table hist_month_data add index date_index(date);


升级tushare
condapip install --upgrade tushare
```

* ##### mysql setup

```
chenlianghong@chenlianghong-Latitude-E5270:/etc/apt$ mysql -u root -p
Enter password:
ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
chenlianghong@chenlianghong-Latitude-E5270:/etc/apt$ mysql -u root -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 5
Server version: 5.7.18 MySQL Community Server (GPL)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
mysql>
mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
+--------------------+
4 rows in set (0.21 sec)

mysql> create database stock default character set=utf8;
Query OK, 1 row affected (0.10 sec)

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| stock              |
| sys                |
+--------------------+
5 rows in set (0.00 sec)

mysql> create user stck identified by 'stck&sql';
Query OK, 0 rows affected (0.27 sec)

mysql> grant all on stock.* to stck;
Query OK, 0 rows affected (0.02 sec)

mysql> exit;
Bye
chenlianghong@chenlianghong-Latitude-E5270:/etc/apt$ mysql -u stck -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 6
Server version: 5.7.18 MySQL Community Server (GPL)

Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| stock              |
+--------------------+
2 rows in set (0.00 sec)

mysql> use stock;
Database changed
mysql>
mysql>
mysql> create table trade_data
    -> (
    ->   datetime datetime not null,
    ->   code mediumint(6) zerofill not null,
    ->   name varchar(6) not null,
    ->   changepercent float not null,
    ->   trade float not null,
    ->   open float not null,
    ->   high float not null,
    ->   low float not null,
    ->   settlement float not null,
    ->   volume bigint not null,
    ->   turnoverratio double not null,
    ->   amount bigint not null,
    ->   per double not null,
    ->   pb double not null,
    ->   mktcap double not null,
    ->   nmc double not null,
    ->   primary key (code, datetime)
    -> ) engine=innodb default charset=utf8;
Query OK, 0 rows affected (1.23 sec)

mysql> create table code_data (
    ->   code mediumint(6) unsigned zerofill NOT NULL,
    ->   name varchar(6) NOT NULL,
    ->   PRIMARY KEY (code)
    -> ) ENGINE=InnoDB DEFAULT CHARSET=utf8;
Query OK, 0 rows affected (0.50 sec)

mysql>
mysql>
mysql>
mysql> show tables;
+-----------------+
| Tables_in_stock |
+-----------------+
| code_data       |
| trade_data      |
+-----------------+
2 rows in set (0.05 sec)

mysql> describe code_data;
+-------+--------------------------------+------+-----+---------+-------+
| Field | Type                           | Null | Key | Default | Extra |
+-------+--------------------------------+------+-----+---------+-------+
| code  | mediumint(6) unsigned zerofill | NO   | PRI | NULL    |       |
| name  | varchar(6)                     | NO   |     | NULL    |       |
+-------+--------------------------------+------+-----+---------+-------+
2 rows in set (0.11 sec)

mysql> describe trade_data;
+---------------+--------------------------------+------+-----+---------+-------+
| Field         | Type                           | Null | Key | Default | Extra |
+---------------+--------------------------------+------+-----+---------+-------+
| datetime      | datetime                       | NO   | PRI | NULL    |       |
| code          | mediumint(6) unsigned zerofill | NO   | PRI | NULL    |       |
| name          | varchar(6)                     | NO   |     | NULL    |       |
| changepercent | float                          | NO   |     | NULL    |       |
| trade         | float                          | NO   |     | NULL    |       |
| open          | float                          | NO   |     | NULL    |       |
| high          | float                          | NO   |     | NULL    |       |
| low           | float                          | NO   |     | NULL    |       |
| settlement    | float                          | NO   |     | NULL    |       |
| volume        | bigint(20)                     | NO   |     | NULL    |       |
| turnoverratio | double                         | NO   |     | NULL    |       |
| amount        | bigint(20)                     | NO   |     | NULL    |       |
| per           | double                         | NO   |     | NULL    |       |
| pb            | double                         | NO   |     | NULL    |       |
| mktcap        | double                         | NO   |     | NULL    |       |
| nmc           | double                         | NO   |     | NULL    |       |
+---------------+--------------------------------+------+-----+---------+-------+
16 rows in set (0.01 sec)

mysql> create table hist_data
    -> (
    ->   date date not null,
    ->   code mediumint(6) zerofill not null,
    ->   open float not null,
    ->   high float not null,
    ->   close float not null,
    ->   low float not null,
    ->   volume float not null,
    ->   price_change float not null,
    ->   p_change float not null,
    ->   ma5 float not null,
    ->   ma10 float not null,
    ->   ma20 float not null,
    ->   v_ma5 float not null,
    ->   v_ma10 float not null,
    ->   v_ma20 float not null,
    ->   turnover float not null,
    ->   primary key (code, date)
    -> ) engine=innodb default charset=utf8;
Query OK, 0 rows affected (0.42 sec)

mysql> create table k_data
    -> (
    ->   date date not null,
    ->   code mediumint(6) zerofill not null,
    ->   open float not null,
    ->   close float not null,
    ->   high float not null,
    ->   low float not null,
    ->   volume bigint not null,
    ->   primary key (code, date)
    -> ) engine=innodb default charset=utf8;
Query OK, 0 rows affected (0.39 sec)

mysql> show tables;
+-----------------+
| Tables_in_stock |
+-----------------+
| code_data       |
| hist_data       |
| k_data          |
| trade_data      |
+-----------------+
4 rows in set (0.00 sec)

mysql>


create table trade_data
(
  datetime datetime not null,
  code mediumint(6) zerofill not null,
  name varchar(6) not null,
  changepercent float not null,
  trade float not null,
  open float not null,
  high float not null,
  low float not null,
  settlement float not null,
  volume bigint not null,
  turnoverratio double not null,
  amount bigint not null,
  per double not null,
  pb double not null,
  mktcap double not null,
  nmc double not null,
  primary key (code, datetime)
) engine=innodb default charset=utf8;


create table code_data (
  code mediumint(6) unsigned zerofill NOT NULL,
  name varchar(6) NOT NULL,
  PRIMARY KEY (code)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;


create table hist_data
(
  date date not null,
  code mediumint(6) zerofill not null,
  open float not null,
  high float not null,
  close float not null,
  low float not null,
  volume float not null,
  price_change float not null,
  p_change float not null,
  ma5 float not null,
  ma10 float not null,
  ma20 float not null,
  v_ma5 float not null,
  v_ma10 float not null,
  v_ma20 float not null,
  turnover float not null,
  primary key (code, date)
) engine=innodb default charset=utf8;


create table k_data
(
  date date not null,
  code mediumint(6) zerofill not null,
  open float not null,
  close float not null,
  high float not null,
  low float not null,
  volume bigint not null,
  primary key (code, date)
) engine=innodb default charset=utf8;


conda install -c anaconda mysql-connector-python
conda install -c anaconda sqlalchemy
pip install tushare
```

* ##### 



