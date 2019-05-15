\#\# MySQL索引详解



&lt;ol&gt;



\#\#\#\#\# &lt;li&gt; 表设计中为什么建议使用自增列作为主键

https://www.zhihu.com/question/27603761

https://yq.aliyun.com/articles/677729



默认楼主使用的是InnoDB存储引擎尽量使用业务无关的自增列作为主键，主要原因：1. InnoDB数据是按照主键聚簇的，数据在物理上按照主键大小顺序存储，使用其他列或者组合无法保证顺序插入，随机IO（SSD的话影响不大）导致插入性能下降2.所有二级索引都存储了主键的，采用二级索引查询，首先找到的主键，然后通过主键定位数据，如果直接使用组合字段作为主键，会导致二级索引占用空间较大，bufferpool中存储的记录数较少，影响性能，而自增列只占4或者8个字节，代价非常小



作者：Feng Guangpu

链接：https://www.zhihu.com/question/27603761/answer/37294758

来源：知乎

著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。





\#\#\#\#\#\# 业务主键与逻辑主键的选择



这之前首先解释两个概念：业务主键与逻辑主键，以设计一个User表为例，有UserId，UserName，CreateDate等等一些属性



业务主键：把具有实际含义的字段作为主键，通常情况下可以把UserId作为业务主键，主键就暗含了非空+唯一性，一个表中每个UserId是不能为空且唯一的，UserId作为主键并没有什么不妥当的。



通常来说，业务主键在一个表中的地位很明显，也很直观，比如UserId，订单ID，CustomerId，登录名等等。



逻辑主键：采用与业务无关的唯一性的字段做主键，或称之为“伪主键”

自增列或者GUID都可以实现所谓的逻辑主键，这里暂且不不讨论GUID（自增与GUID也是一个很大的话题），在User表中增加一个自增列作为主键，因为自增列是也符合主键的特性，也即非空且唯一。



这个字段的第一个特点是与任何业务逻辑无关，第二个特点是对业务来说是不可见的，比如设计一张用户表，在满足存储所有用户信息的条件下，该字段作为一个独立的列存在，而不描述任何业务含义。



以上就是所谓的业务主键和逻辑主键，实话说，仅仅从表面上看，后者并没有什么明显的优势，甚至可以说，如果仅仅就这一张表来说的话，有点冗余的感觉。



但是实际应用中，一个应用是具体一系列复杂的表来支撑的，表与表之间存在着一系列的关联关系，比如1对1或者1对多的关系，此时，自增列的作用就可以提现出来了。



\#\#\#\#\#\# 业务主键存在的问题



首先说业务主键存在的弊端，业务主键必然要与业务逻辑挂钩，这就意味着业务主键可能是一个或者多个字段的结合，甚至对于每一个业务主键字段都有要求，比如订单号要求以DH0000000000X开头，或者通常情况下的UserId，比如博客园的登录Id，是介于一定长度之间的字符与数字的组合



这样存在一下几个弊端：



1. 当前表的主键与其他表的存在关联关系的时候，必然要存储当前这个表的业务主键信息，甚至不止一个字段，这样无疑增加了表与表之间关联的复杂性与存储空间。

  同理，在表之间join的时候，必然要用基于业务主键的各个条件连接，业务增加SQL的复杂性和降低SQL的执行效率。

2. 既然是业务主键，也存在潜在的修改的可能性，有人说主键还会修改，不可能吧，其实这种情况还真不少，比如呢？

博客园的登录名，作为一个类似于UserId的东西，可以认为是一个业务主键，其特点就是非空+唯一的，但是这个用户名就是可以修改的，如果其他跟用户信息相关的表存储了用户名，用户名有修改之后，要达到数据的一致性，要修改一系列存储了用户名相关的表。



  再比如，如果用邮箱作为用户名进行注册，比如雅虎邮箱关闭之后，注册的各种宝宝账号，如果相关表中直接存储的这个业务主键，也即直接存储的这个邮箱，在需要给用户发送邮件的地方存一个邮箱信息字段，那么修改个人邮箱的时候，要涉及多少张表的修改？



另外，在SQLServer中，在Update主键的时候，实际上执行行的是一个delete和insert操作，也就是说会先删除要更新的那条记录，再插入一条记录。



这也是一个代价相对较大的操作。





\#\#\#\#\# &lt;li&gt; MySQL性能优化建议

1. 首先,MySQL\(与所有DBMS一样\)具有特定的硬件建议。在学习和研究MySQL时,使用任何旧的计算机作为服务器都可以。但对用于生产的服务器来说,应该坚持遵循这些硬件建议。



2. 一般来说,关键的生产DBMS应该运行在自己的专用服务器上。



3. MySQL是用一系列的默认设置预先配置的,从这些设置开始通常是很好的。但过一段时间后你可能需要调整内存分配、缓冲区大小等 。\(为查看当前设置, 可使用SHOW VARIABLES; 和SHOW STATUS;\)



4. MySQL一个多用户多线程的DBMS,换言之,它经常同时执行多个任务。如果这些任务中的某一个执行缓慢,则所有请求都会执行缓慢。如果你遇到显著的性能不良,可使用 SHOW PROCESSLIST显示所有活动进程\(以及它们的线程ID和执行时间\)。你还可以用KILL 命令终结某个特定的进程\(使用这个命令需要作为管理员登录\)。



5. 总是有不止一种方法编写同一条 SELECT 语句。应该试验联结、并、子查询等,找出最佳的方法。



6. 使用 EXPLAIN 语句让MySQL解释它将如何执行一条 SELECT 语句。



7. 一般来说,存储过程执行得比一条一条地执行其中的各条MySQL语句快。



8. 应该总是使用正确的数据类型。



9. 决不要检索比需求还要多的数据。换言之,不要用 SELECT \* \(除非你真正需要每个列\)。



10. 有的操作\(包括 INSERT \)支持一个可选的 DELAYED 关键字,如果使用它,将把控制立即返回给调用程序,并且一旦有可能就实际执行该操作。



11. 在导入数据时,应该关闭自动提交。你可能还想删除索引\(包括FULLTEXT 索引\),然后在导入完成后再重建它们。



12. 必须索引数据库表以改善数据检索的性能。确定索引什么不是一件微不足道的任务,需要分析使用的 SELECT 语句以找出重复的WHERE 和 ORDER BY 子句。如果一个简单的 WHERE 子句返回结果所花的时间太长,则可以断定其中使用的列\(或几个列\)就是需要索引的对象。



13. 你的 SELECT 语句中有一系列复杂的 OR 条件吗?通过使用多条SELECT 语句和连接它们的 UNION 语句,你能看到极大的性能改进。



14. 索引改善数据检索的性能,但损害数据插入、删除和更新的性能。如果你有一些表,它们收集数据且不经常被搜索,则在有必要之前不要索引它们。\(索引可根据需要添加和删除。\)



15. LIKE 很慢。一般来说,最好是使用 FULLTEXT 而不是 LIKE 。



16. 数据库是不断变化的实体。一组优化良好的表一会儿后可能就面目全非了。由于表的使用和内容的更改,理想的优化和配置也会改变。



17. 最重要的规则就是,每条规则在某些条件下都会被打破。



\#\#\#\#\# &lt;li&gt; MySQL索引

https://www.runoob.com/w3cnote/mysql-index.html

https://www.runoob.com/mysql/mysql-index.html

https://www.w3cschool.cn/mysql/mysql-index.html



https://www.cnblogs.com/orchidbaby/p/7704407.html

https://www.cnblogs.com/luyucheng/p/6289714.html

http://www.cnblogs.com/renfanzi/p/5742650.html

http://www.cnblogs.com/ggjucheng/archive/2012/11/04/2754128.html

https://www.cnblogs.com/bypp/p/7755307.html

https://zhuanlan.zhihu.com/p/29118331

https://www.cnblogs.com/chenshishuo/p/5030029.html

https://www.cnblogs.com/lihuiyong/p/5623191.html

https://www.cnblogs.com/whgk/p/6179612.html



\#\#\#\#\#\# 索引的类型



1. UNIQUE唯一索引



 - 不可以出现相同的值，可以有NULL值。

 - 创建唯一索引的目的不是为了提高访问速度，而只是为了避免数据出现重复。唯一索引可以有多个但索引列的值必须唯一，索引列的值允许有空值。如果能确定某个数据列将只包含彼此各不相同的值，在为这个数据列创建索引的时候就应该使用关键字UNIQUE



2. INDEX普通索引



 - 允许出现相同的索引内容。

 - 仅加速查询



3. PRIMARY KEY主键索引



 - 不允许出现相同的值，且不能为NULL值，一个表只能有一个primary\_key索引。



4. fulltext index 全文索引

 - 对文本的内容进行分词，进行搜索



上述三种索引都是针对列的值发挥作用，但全文索引，可以针对值中的某个单词，比如一篇文章中的某个词，然而并没有什么卵用，因为只有myisam以及英文支持，并且效率让人不敢恭维，但是可以用coreseek和xunsearch等第三方应用来完成这个需求。



\#\#\#\#\#\# 索引的创建



1. ALTER TABLE



 适用于表创建完毕之后再添加。



 ALTER TABLE 表名 ADD 索引类型 \(unique,primary key,fulltext,index\)\\[索引名\]\(字段名\)



 ALTER TABLE \`table\_name\` ADD INDEX \`index\_name\` \(\`column\_list\`\) -- 索引名，可要可不要；如果不要，当前的索引名就是该字段名。

 ALTER TABLE \`table\_name\` ADD UNIQUE \(\`column\_list\`\)

 ALTER TABLE \`table\_name\` ADD PRIMARY KEY \(\`column\_list\`\)

 ALTER TABLE \`table\_name\` ADD FULLTEXT KEY \(\`column\_list\`\)



2. CREATE INDEX



 CREATE INDEX可对表增加普通索引或UNIQUE索引。



 --例：只能添加这两种索引

CREATE INDEX index\_name ON table\_name \(column\_list\)

CREATE UNIQUE INDEX index\_name ON table\_name \(column\_list\)



 另外，还可以在建表时添加：

\`\`\`

CREATE TABLE \`test1\` \(

  \`id\` smallint\(5\) UNSIGNED AUTO\_INCREMENT NOT NULL, -- 注意，下面创建了主键索引，这里就不用创建了

  \`username\` varchar\(64\) NOT NULL COMMENT '用户名',

  \`nickname\` varchar\(50\) NOT NULL COMMENT '昵称/姓名',

  \`intro\` text,

  PRIMARY KEY \(\`id\`\),

  UNIQUE KEY \`unique1\` \(\`username\`\), -- 索引名称，可要可不要，不要就是和列名一样

  KEY \`index1\` \(\`nickname\`\),

  FULLTEXT KEY \`intro\` \(\`intro\`\)

\) ENGINE=MyISAM AUTO\_INCREMENT=4 DEFAULT CHARSET=utf8 COMMENT='后台用户表';

\`\`\`



3. 索引的删除



 DROP INDEX \`index\_name\` ON \`talbe\_name\`

ALTER TABLE \`table\_name\` DROP INDEX \`index\_name\`

-- 这两句都是等价的,都是删除掉table\_name中的索引index\_name;



 ALTER TABLE \`table\_name\` DROP PRIMARY KEY -- 删除主键索引，注意主键索引只能用这种方式删除



4. 索引的查看



 show index from tablename;



5. 索引的更改



 更改个毛线,删掉重建一个既可



\#\#\#\#\#\# 组合索引与前缀索引



注意，这两种称呼是对建立索引技巧的一种称呼，并非索引的类型。



1. 组合索引

 MySQL单列索引和组合索引究竟有何区别呢？



 为了形象地对比两者，先建一个表：

\`\`\` mysql

CREATE TABLE \`myIndex\` \(

  \`i\_testID\` INT NOT NULL AUTO\_INCREMENT,

  \`vc\_Name\` VARCHAR\(50\) NOT NULL,

  \`vc\_City\` VARCHAR\(50\) NOT NULL,

  \`i\_Age\` INT NOT NULL,

  \`i\_SchoolID\` INT NOT NULL,

  PRIMARY KEY \(\`i\_testID\`\)

\);

\`\`\`



 假设表内已有1000条数据，在这 10000 条记录里面 7 上 8 下地分布了 5 条 vc\_Name=”erquan” 的记录，只不过 city,age,school 的组合各不相同。来看这条 T-SQL：



 \`\`\`

SELECT \`i\_testID\` FROM \`myIndex\` WHERE \`vc\_Name\`='erquan' AND \`vc\_City\`='郑州' AND \`i\_Age\`=25; -- 关联搜索;

\`\`\`



 首先考虑建MySQL单列索引：



 在 vc\_Name 列上建立了索引。执行 T-SQL 时，MYSQL 很快将目标锁定在了 vc\_Name=erquan 的 5 条记录上，取出来放到一中间结果集。在这个结果集里，先排除掉 vc\_City 不等于”郑州”的记录，再排除 i\_Age 不等于 25 的记录，最后筛选出唯一的符合条件的记录。虽然在 vc\_Name 上建立了索引，查询时MYSQL不用扫描整张表，效率有所提高，但离我们的要求还有一定的距离。同样的,在 vc\_City 和 i\_Age 分别建立的MySQL单列索引的效率相似。



 为了进一步榨取 MySQL 的效率，就要考虑建立组合索引。就是将 vc\_Name,vc\_City,i\_Age 建到一个索引里：



 \`\`\`

ALTER TABLE \`myIndex\` ADD INDEX \`name\_city\_age\` \(vc\_Name\(10\),vc\_City,i\_Age\);

\`\`\`



 建表时，vc\_Name 长度为 50，这里为什么用 10 呢？这就是下文要说到的前缀索引，因为一般情况下名字的长度不会超过 10，这样会加速索引查询速度，还会减少索引文件的大小，提高 INSERT 的更新速度。



 执行 T-SQL 时，MySQL 无须扫描任何记录就到找到唯一的记录！



 如果分别在 vc\_Name,vc\_City,i\_Age 上建立单列索引，让该表有 3 个单列索引，查询时和上述的组合索引效率一样吗？答案是大不一样，远远低于我们的组合索引。虽然此时有了三个索引，但 MySQL 只能用到其中的那个它认为似乎是最有效率的单列索引，另外两个是用不到的，也就是说还是一个全表扫描的过程。



 建立这样的组合索引，其实是相当于分别建立了：



    vc\_Name,vc\_City,i\_Age

    vc\_Name,vc\_City

    vc\_Name



 这样的三个组合索引！为什么没有 vc\_City,i\_Age 等这样的组合索引呢？这是因为 mysql 组合索引 “最左前缀” 的结果。简单的理解就是只从最左面的开始组合。并不是只要包含这三列的查询都会用到该组合索引，下面的几个 T-SQL 会用到：



 \`\`\`

SELECT \* FROM myIndex WHREE vc\_Name=”erquan” AND vc\_City=”郑州” SELECT \* FROM myIndex WHREE vc\_Name=”erquan”

\`\`\`



 而下面几个则不会用到：

\`\`\`

SELECT \* FROM myIndex WHREE i\_Age=20 AND vc\_City=”郑州” SELECT \* FROM myIndex WHREE vc\_City=”郑州”

\`\`\`

 也就是，name\_city\_age\(vc\_Name\(10\),vc\_City,i\_Age\) 从左到右进行索引，如果没有左前索引Mysql不执行索引查询。



2. 前缀索引



 如果索引列长度过长，这种列索引时将会产生很大的索引文件，不便于操作，可以使用前缀索引方式进行索引前缀索引应该控制在一个合适的点，控制在0.31黄金值即可（大于这个值就可以创建）。



 SELECT COUNT\(DISTINCT\(LEFT\(\`title\`,10\)\)\)/COUNT\(\*\) FROM Arctic; — 这个值大于0.31就可以创建前缀索引，Distinct去重复 ALTER TABLE \`user\` ADD INDEX \`uname\`\(title\(10\)\); — 增加前缀索引SQL，将人名的索引建立在10，这样可以减少索引文件大小，加快索引查询速度。



\#\#\#\#\#\# 创建索引的技巧



1. 维度高的列创建索引。

2. 数据列中不重复值出现的个数，这个数量越高，维度就越高。

3. 如数据表中存在8行数据a,b ,c,d,a,b,c,d这个表的维度为4。

4. 要为维度高的列创建索引，如性别和年龄，那年龄的维度就高于性别。

5. 性别这样的列不适合创建索引，因为维度过低。

6. 对 where,on,group by,order by 中出现的列使用索引。

7. 对较小的数据列使用索引，这样会使索引文件更小，同时内存中也可以装载更多的索引键。

8. 为较长的字符串使用前缀索引。

9. 不要过多创建索引，除了增加额外的磁盘空间外，对于DML操作的速度影响很大，因为其每增删改一次就得从新建立索引。

10. 使用组合索引，可以减少文件索引大小，在使用时速度要优于多个单列索引。



\#\#\#\#\#\# 索引的弊端



不要盲目的创建索引，只为查询操作频繁的列创建索引，创建索引会使查询操作变得更加快速，但是会降低增加、删除、更新操作的速度，因为执行这些操作的同时会对索引文件进行重新排序或更新。



但是，在互联网应用中，查询的语句远远大于DML的语句，甚至可以占到80%~90%，所以也不要太在意，只是在大数据导入时，可以先删除索引，再批量插入数据，最后再添加索引。



\#\#\#\#\# &lt;li&gt; MySQL索引优化

https://www.cnblogs.com/jianmingyuan/p/6740090.html

https://yq.aliyun.com/articles/661447

https://tech.meituan.com/2014/06/30/mysql-index.html



\#\#\#\#\# &lt;li&gt; MySQL索引的原理

http://blog.codinglabs.org/articles/theory-of-mysql-index.html

https://www.cnblogs.com/boothsun/p/8970952.html

https://www.cnblogs.com/bypp/p/7755307.html

https://zhuanlan.zhihu.com/p/49947103

https://zhuanlan.zhihu.com/p/35811482



https://www.cnblogs.com/shijingxiang/articles/4743324.html

https://www.cnblogs.com/tgycoder/p/5410057.html

https://blog.csdn.net/tongdanping/article/details/79878302

https://blog.csdn.net/waeceo/article/details/78702584

https://blog.csdn.net/u012978884/article/details/52416997

https://blog.csdn.net/u013967628/article/details/84305511





\#\#\#\#\# &lt;li&gt; MySQL主键与索引

https://www.cnblogs.com/iwangzhch/p/3990381.html

https://www.cnblogs.com/shijianchuzhenzhi/p/6383117.html

https://zhuanlan.zhihu.com/p/29367933



