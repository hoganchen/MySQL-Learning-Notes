# MySQL索引优化

查看索引

show index from hist\_5m\_data;



MySQL EXPLAIN命令是查询性能优化不可缺少的一部分，EXPLAIN 显示了 MySQL 如何使用索引来处理 SELECT 语句以及连接表。可以帮助选择更好的索引和写出更优化的查询语句

mysql&gt; explain select \* from hist\_day\_data where code = '600000';

+----+-------------+---------------+------------+------+---------------+---------+---------+-------+------+----------+-------+

\| id \| select\_type \| table         \| partitions \| type \| possible\_keys \| key     \| key\_len \| ref   \| rows \| filtered \| Extra \|

+----+-------------+---------------+------------+------+---------------+---------+---------+-------+------+----------+-------+

\|  1 \| SIMPLE      \| hist\_day\_data \| NULL       \| ref  \| PRIMARY       \| PRIMARY \| 3       \| const \|  963 \|   100.00 \| NULL  \|

+----+-------------+---------------+------------+------+---------------+---------+---------+-------+------+----------+-------+

1 row in set, 1 warning \(0.57 sec\)



mysql&gt; explain select \* from hist\_day\_data where date = '2018-09-04' and code = '600000';

+----+-------------+---------------+------------+-------+---------------+---------+---------+-------------+------+----------+-------+

\| id \| select\_type \| table         \| partitions \| type  \| possible\_keys \| key     \| key\_len \| ref         \| rows \| filtered \| Extra \|

+----+-------------+---------------+------------+-------+---------------+---------+---------+-------------+------+----------+-------+

\|  1 \| SIMPLE      \| hist\_day\_data \| NULL       \| const \| PRIMARY       \| PRIMARY \| 6       \| const,const \|    1 \|   100.00 \| NULL  \|

+----+-------------+---------------+------------+-------+---------------+---------+---------+-------------+------+----------+-------+

1 row in set, 1 warning \(0.07 sec\)



mysql&gt; explain select \* from hist\_day\_data where date = '2018-09-04';

+----+-------------+---------------+------------+------+---------------+------+---------+------+---------+----------+-------------+

\| id \| select\_type \| table         \| partitions \| type \| possible\_keys \| key  \| key\_len \| ref  \| rows    \| filtered \| Extra       \|

+----+-------------+---------------+------------+------+---------------+------+---------+------+---------+----------+-------------+

\|  1 \| SIMPLE      \| hist\_day\_data \| NULL       \| ALL  \| NULL          \| NULL \| NULL    \| NULL \| 1886392 \|    10.00 \| Using where \|

+----+-------------+---------------+------------+------+---------------+------+---------+------+---------+----------+-------------+

1 row in set, 1 warning \(0.00 sec\)



mysql&gt; 





复合主键在MySQL中的性能缺点

https://codeday.me/bug/20170916/72408.html



INSERT和UPDATE性能变化不大：对于\(INT\)和\(INT，INT\)键几乎相同。



复合PRIMARY KEY的SELECT性能取决于许多因素。



如果您的表是InnoDB，那么该表隐式地聚集在PRIMARY KEY值上。



这意味着如果两个值都包含键，那么对这两个值的搜索将更快;不需要额外的键查找。



假设你的查询是这样的：



SELECT  \*

FROM    mytable

WHERE   col1 = @value1

        AND col2 = @value2



并且表布局是这样的：



CREATE TABLE mytable \(

        col1 INT NOT NULL,

        col2 INT NOT NULL,

        data VARCHAR\(200\) NOT NULL,

        PRIMARY KEY pk\_mytable \(col1, col2\)

\) ENGINE=InnoDB



，引擎将仅需要在表本身中查找确切的键值。



如果您使用自动增量字段作为假ID：



CREATE TABLE mytable \(

        id INT NOT NULL AUTO\_INCREMENT PRIMARY KEY,

        col1 INT NOT NULL,

        col2 INT NOT NULL,

        data VARCHAR\(200\) NOT NULL,

        UNIQUE KEY ix\_mytable\_col1\_col2 \(col1, col2\)

\) ENGINE=InnoDB



，则引擎将需要首先查找索引ix\_mytable\_col1\_col2中的值\(col1，col2\)，从索引\(id的值\)检索行指针，并通过表中的id进行另一次查找。



但是，对于MyISAM表，这没有什么区别，因为MyISAM表是堆组织的，并且行指针只是文件偏移量。



在这两种情况下，将创建相同的索引\(对于PRIMARY KEY或UNIQUE KEY\)，并且将以相同的方式使用。





从带有限制的MySQL表中选择平均值

https://ask.helplib.com/sql/post\_975428

https://codeday.me/bug/20180131/128535.html

https://ask.helplib.com/excel/post\_6498338



我试图获得最低 5个定价项目的平均值，按附加到它们的用户名分组。 但是，下面的查询给出了每个用户\( 当然价格是多少\)的平均价格，但我只想要一个答案。



我认为这就是你想要的：



SELECT AVG\(items.price\)

 FROM \(SELECT t.price

 FROM TABLE t

 WHERE t.price&gt; '0' 

 AND t.item\_id = '$id'

 ORDER BY t.price

 LIMIT 5\) items



它将返回 5最低价格的平均值- 一个单一的答案。





MySQL索引原理及慢查询优化

https://tech.meituan.com/mysql\_index.html



https://www.zhihu.com/question/27603761

https://www.kancloud.cn/db-design/mysql-dba/596722

https://www.kancloud.cn/thinkphp/mysql-faq/47451

http://seanlook.com/2016/05/13/mysql-innodb-primary\_key/

http://seanlook.com/2016/05/11/mysql-dev-principle-ec/

http://www.ywnds.com/?p=8735





建索引的几大原则



1.最左前缀匹配原则，非常重要的原则，mysql会一直向右匹配直到遇到范围查询\(&gt;、&lt;、between、like\)就停止匹配，比如a = 1 and b = 2 and c &gt; 3 and d = 4 如果建立\(a,b,c,d\)顺序的索引，d是用不到索引的，如果建立\(a,b,d,c\)的索引则都可以用到，a,b,d的顺序可以任意调整。

2.=和in可以乱序，比如a = 1 and b = 2 and c = 3 建立\(a,b,c\)索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式

3.尽量选择区分度高的列作为索引,区分度的公式是count\(distinct col\)/count\(\*\)，表示字段不重复的比例，比例越大我们扫描的记录数越少，唯一键的区分度是1，而一些状态、性别字段可能在大数据面前区分度就是0，那可能有人会问，这个比例有什么经验值吗？使用场景不同，这个值也很难确定，一般需要join的字段我们都要求是0.1以上，即平均1条扫描10条记录

4.索引列不能参与计算，保持列“干净”，比如from\_unixtime\(create\_time\) = ’2014-05-29’就不能使用到索引，原因很简单，b+树中存的都是数据表中的字段值，但进行检索时，需要把所有元素都应用函数才能比较，显然成本太大。所以语句应该写成create\_time = unix\_timestamp\(’2014-05-29’\);

5.尽量的扩展索引，不要新建索引。比如表中已经有a的索引，现在要加\(a,b\)的索引，那么只需要修改原来的索引即可

itBook allows you to organize your book into chapters, each chapter is stored in a separate file like this one.

