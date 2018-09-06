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

    mysql> help

    For information about MySQL products and services, visit:
       http://www.mysql.com/
    For developer information, including the MySQL Reference Manual, visit:
       http://dev.mysql.com/
    To buy MySQL Enterprise support, training, or other products, visit:
       https://shop.mysql.com/

    List of all MySQL commands:
    Note that all text commands must be first on line and end with ';'
    ?         (\?) Synonym for `help'.
    clear     (\c) Clear the current input statement.
    connect   (\r) Reconnect to the server. Optional arguments are db and host.
    delimiter (\d) Set statement delimiter.
    edit      (\e) Edit command with $EDITOR.
    ego       (\G) Send command to mysql server, display result vertically.
    exit      (\q) Exit mysql. Same as quit.
    go        (\g) Send command to mysql server.
    help      (\h) Display this help.
    nopager   (\n) Disable pager, print to stdout.
    notee     (\t) Don't write into outfile.
    pager     (\P) Set PAGER [to_pager]. Print the query results via PAGER.
    print     (\p) Print current command.
    prompt    (\R) Change your mysql prompt.
    quit      (\q) Quit mysql.
    rehash    (\#) Rebuild completion hash.
    source    (\.) Execute an SQL script file. Takes a file name as an argument.
    status    (\s) Get status information from the server.
    system    (\!) Execute a system shell command.
    tee       (\T) Set outfile [to_outfile]. Append everything into given outfile.
    use       (\u) Use another database. Takes database name as argument.
    charset   (\C) Switch to another charset. Might be needed for processing binlog with multi-byte charsets.
    warnings  (\W) Show warnings after every statement.
    nowarning (\w) Don't show warnings after every statement.
    resetconnection(\x) Clean session context.

    For server side help, type 'help contents'

    mysql> help centents;

    Nothing found
    Please try to run 'help contents' for a list of all accessible topics

    mysql> help contents;
    You asked for help about help category: "Contents"
    For more information, type 'help <item>', where <item> is one of the following
    categories:
       Account Management
       Administration
       Compound Statements
       Data Definition
       Data Manipulation
       Data Types
       Functions
       Functions and Modifiers for Use with GROUP BY
       Geographic Features
       Help Metadata
       Language Structure
       Plugins
       Procedures
       Storage Engines
       Table Maintenance
       Transactions
       User-Defined Functions
       Utility

    mysql> help table maintenance;
    You asked for help about help category: "Table Maintenance"
    For more information, type 'help <item>', where <item> is one of the following
    topics:
       ANALYZE TABLE
       CHECK TABLE
       CHECKSUM TABLE
       OPTIMIZE TABLE
       REPAIR TABLE

    mysql> help check table;
    Name: 'CHECK TABLE'
    Description:
    Syntax:
    CHECK TABLE tbl_name [, tbl_name] ... [option] ...

    option = {
        FOR UPGRADE
      | QUICK
      | FAST
      | MEDIUM
      | EXTENDED
      | CHANGED
    }

    CHECK TABLE checks a table or tables for errors. CHECK TABLE works for
    InnoDB, MyISAM, ARCHIVE, and CSV tables. For MyISAM tables, the key
    statistics are updated as well.

    Before running CHECK TABLE on InnoDB tables, see
    http://dev.mysql.com/doc/refman/5.7/en/check-table.html#check-table-inn
    odb.

    To check a table, you must have some privilege for it.

    CHECK TABLE can also check views for problems, such as tables that are
    referenced in the view definition that no longer exist.

    CHECK TABLE is supported for partitioned tables, and you can use ALTER
    TABLE ... CHECK PARTITION to check one or more partitions; for more
    information, see [HELP ALTER TABLE], and
    http://dev.mysql.com/doc/refman/5.7/en/partitioning-maintenance.html.

    In MySQL 5.7.1, gtid_next must be set to AUTOMATIC before issuing this
    statement. This restriction does not apply in MySQL 5.7.2 or later.
    (Bug #16062608, Bug #16715809, Bug #69045)

    CHECK TABLE ignores generated virtual columns that are not indexed.

    URL: http://dev.mysql.com/doc/refman/5.7/en/check-table.html

* ##### 



