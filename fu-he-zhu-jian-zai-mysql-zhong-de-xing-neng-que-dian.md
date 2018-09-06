### 复合主键在MySQL中的性能缺点

INSERT和UPDATE性能变化不大：对于\(INT\)和\(INT，INT\)键几乎相同。

复合PRIMARY KEY的SELECT性能取决于许多因素。

如果您的表是InnoDB，那么该表隐式地聚集在PRIMARY KEY值上。

这意味着如果两个值都包含键，那么对这两个值的搜索将更快;不需要额外的键查找。

假设你的查询是这样的：

```
SELECT  *
FROM    mytable
WHERE   col1 = @value1
        AND col2 = @value2
```

并且表布局是这样的：

```
CREATE TABLE mytable (
        col1 INT NOT NULL,
        col2 INT NOT NULL,
        data VARCHAR(200) NOT NULL,
        PRIMARY KEY pk_mytable (col1, col2)
) ENGINE=InnoDB
```

引擎将仅需要在表本身中查找确切的键值。

如果您使用自动增量字段作为假ID：

```
CREATE TABLE mytable (
        id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
        col1 INT NOT NULL,
        col2 INT NOT NULL,
        data VARCHAR(200) NOT NULL,
        UNIQUE KEY ix_mytable_col1_col2 (col1, col2)
) ENGINE=InnoDB
```

则引擎将需要首先查找索引ix\_mytable\_col1\_col2中的值\(col1，col2\)，从索引\(id的值\)检索行指针，并通过表中的id进行另一次查找。

但是，对于MyISAM表，这没有什么区别，因为MyISAM表是堆组织的，并且行指针只是文件偏移量。

在这两种情况下，将创建相同的索引\(对于PRIMARY KEY或UNIQUE KEY\)，并且将以相同的方式使用。

#### 参考链接

https://codeday.me/bug/20170916/72408.html

https://stackoverrun.com/cn/q/278457

https://oomake.com/question/278819%E2%80%8B

[http://stackoverflow.com/questions/1460465/composite-primary-key-performance-drawback-in-mysql](http://stackoverflow.com/questions/1460465/composite-primary-key-performance-drawback-in-mysql)

