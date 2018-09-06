### MySQL命令备份

* ##### 连续下跌

```
mysql> select * from hist_30m_data where date = '2018-09-05 15:00:00' and  ma5 < ma10 * 0.9 and ma10 < ma20 * 0.9 and low > 10;
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

* ##### 



