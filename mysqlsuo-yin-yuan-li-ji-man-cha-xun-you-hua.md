### MySQL索引原理及慢查询优化

#### 建索引的几大原则

1.最左前缀匹配原则，非常重要的原则，mysql会一直向右匹配直到遇到范围查询\(&gt;、&lt;、between、like\)就停止匹配，比如a = 1 and b = 2 and c &gt; 3 and d = 4 如果建立\(a,b,c,d\)顺序的索引，d是用不到索引的，如果建立\(a,b,d,c\)的索引则都可以用到，a,b,d的顺序可以任意调整。

2.=和in可以乱序，比如a = 1 and b = 2 and c = 3 建立\(a,b,c\)索引可以任意顺序，mysql的查询优化器会帮你优化成索引可以识别的形式

3.尽量选择区分度高的列作为索引,区分度的公式是count\(distinct col\)/count\(\*\)，表示字段不重复的比例，比例越大我们扫描的记录数越少，唯一键的区分度是1，而一些状态、性别字段可能在大数据面前区分度就是0，那可能有人会问，这个比例有什么经验值吗？使用场景不同，这个值也很难确定，一般需要join的字段我们都要求是0.1以上，即平均1条扫描10条记录

4.索引列不能参与计算，保持列“干净”，比如from\_unixtime\(create\_time\) = ’2014-05-29’就不能使用到索引，原因很简单，b+树中存的都是数据表中的字段值，但进行检索时，需要把所有元素都应用函数才能比较，显然成本太大。所以语句应该写成create\_time = unix\_timestamp\(’2014-05-29’\);

5.尽量的扩展索引，不要新建索引。比如表中已经有a的索引，现在要加\(a,b\)的索引，那么只需要修改原来的索引即可

#### 参考链接

[https://www.zhihu.com/question/27603761](https://www.zhihu.com/question/27603761)

[https://www.kancloud.cn/db-design/mysql-dba/596722](https://www.kancloud.cn/db-design/mysql-dba/596722)

[https://www.kancloud.cn/thinkphp/mysql-faq/47451](https://www.kancloud.cn/thinkphp/mysql-faq/47451)

[http://seanlook.com/2016/05/13/mysql-innodb-primary\_key/](http://seanlook.com/2016/05/13/mysql-innodb-primary_key/)

[http://seanlook.com/2016/05/11/mysql-dev-principle-ec/](http://seanlook.com/2016/05/11/mysql-dev-principle-ec/)

[http://www.ywnds.com/?p=8735](http://www.ywnds.com/?p=8735)
