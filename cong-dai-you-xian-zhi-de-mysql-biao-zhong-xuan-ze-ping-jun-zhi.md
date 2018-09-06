### 从带有限制的MySQL表中选择平均值

我试图获得最低 5个定价项目的平均值，按附加到它们的用户名分组。 但是，下面的查询给出了每个用户\( 当然价格是多少\)的平均价格，但我只想要一个答案。

```
SELECT AVG(price) 
 FROM table 
 WHERE price> '0' && item_id = '$id' 
GROUP BY username 
ORDER BY price ASC 
 LIMIT 5
```

我认为这就是你想要的：

```
SELECT AVG(items.price)
 FROM (SELECT t.price
 FROM TABLE t
 WHERE t.price> '0' 
 AND t.item_id = '$id'
 ORDER BY t.price
 LIMIT 5) items
```

它将返回 5最低价格的平均值- 一个唯一的答案。

#### 参考链接

https://ask.helplib.com/sql/post\_975428

https://ask.helplib.com/excel/post\_6498338

https://codeday.me/bug/20180131/128535.html

