[TOC]
# MySQL必知必会

## 第一章 了解SQL
数据库：保存有组织的数据的容器（通常是一个文件或一组文件）

> 就本书而言数据库是一个以某种有组织的方式存储的数据集合。理解数据库的一种最简单的办法是将其想象为一个文件柜。此文件柜是一个存放数据的物理位置，不管数据是什么以及如何组织的。

表: 某种特定类型数据的结构化清单

模式(schema)：关于数据库和表的布局及特性的信息

列(column):表中的一个字段。所有的表都是由一个或多个列组成的。

数据类型(datatype):所容许的数据的类型。每个表列都有相应的数据类型，它限制（或容许）该列中存储的数据

行(row):表中的一个记录

主键(primary key):一列（或一组列），其值能够唯一区分表中每个行
* 应该总是定义主键
* 主键需要满足条件
    * 任意两行都不具有相同的主键值
    * 每个行都必须具有一个主键值（主键列不允许NULL值）

主键的最好习惯
* 不更新主键列中的值
* 不重用主键列的值
* 不在主键列中使用可能会更改的值

## 第二章 MySQL简介
为什么有那么多的公司和开发人员使用MySQL？
* 成本——MySQL是开放源代码的，一般可以免费使用（甚至可以 免费修改）。
* 性能——MySQL执行很快（非常快）。 
* 可信赖——某些非常重要和声望很高的公司、站点使用MySQL。
* 简单——MySQL很容易安装和使用。

MySQL命令行实用程序
* 命令输入在mysql>之后； 
* 命令用;或\g结束，换句话说，仅按Enter不执行命令； 
* 输入help或\h获得帮助，也可以输入更多的文本获得特定命令的 帮助（如，输入help select获得使用SELECT语句的帮助）；
* 输入quit或exit退出命令行实用程序。 

## 第三章 使用MySQL




## 第7章

### IN操作符
`IN`操作符用来指定条件范围，范围中的每个条件都可以进行匹配。`IN`取合法值的由逗号分隔的清单，全都括在圆括号中。IN操作符完成与OR相同的功能。

为什么要使用IN操作符？其优点具体如下。
* 在使用长的合法选项清单时，IN操作符的语法更清楚且更直观。 
*  在使用IN时，计算的次序更容易管理（因为使用的操作符更少）。 
*  IN操作符一般比OR操作符清单执行更快。 
*  IN的最大优点是可以包含其他SELECT语句，使得能够更动态地建立WHERE子句。

类似语句： NOT IN

## 第8章 用通配符进行过滤
### LIKE
`LIKE`操作符通常用于基于模式查询选择数据。以正确的方式使用`LIKE`运算符对于增加/减少查询性能至关重要。
`LIKE`操作符允许您根据指定的模式从表中查询选择数据。 因此，`LIKE`运算符通常用在`SELECT`语句的`WHERE`子句中。
`MySQL`提供两个通配符，用于与LIKE运算符一起使用，它们分别是：百分比符号 `%`和下划线`_`

* `%`通配符:在搜索串中，`%`表示任何字符出现任意次数。
* 下划线（`_`）通配符: `_`只匹配单个字符而不是多个字符。


使用通配符要记住的技巧。
* 不要过度使用通配符。如果其他操作符能达到相同的目的，应该使用其他操作符。
* 在确实需要使用通配符时，除非绝对有必要，否则不要把它们用在搜索模式的开始处。把通配符置于搜索模式的开始处，搜索起来是最慢的。
* 仔细注意通配符的位置。如果放错地方，可能不会返回想要的数据。

## 第9章 用正则表达式进行搜索
MySQL中的正则表达式匹配（自版本 
3.23.4后）不区分大小写（即，大写和小写都匹配）。为区分大 小写， 可使用 `BINARY` 关键字， 如 
`WHERE prod_name REGEXP BINARY 'JetPack .000'`


注：
MySQL的LIKE、REGEXP 都是不区分大小写的。如果想区分的话，可以在建表的时候将字段名设置为Binary属性
```sql
alter  table `conan_english_dialog`.`writing_material` change name name varchar(255) BINARY NOT NULL DEFAULT '' COMMENT '素材名称';
```
也可以在查询SQL上写上BINARY

```sql
SELECT * FROM address WHERE district LIKE BINARY  '%m%'
```



## 第10章 创建计算字段
### 计算字段的目的
存储在表中的数据都不是应用程序所需要的。 我们需要直接从数据库中检索出【转换、计算或格式化过】的数据；而不是检索出数据，然后再在客户机应用程序或报告程序中重新格式化。

计算字段并不实际存在于数据库表中。计算字段是运行时在`SELECT`语句内创建的。


### Concat()
Concat() 拼接串， 即把多个串连接起来形成一个较长的串。 Concat()需要一个或多个指定的串，各个串之间用逗号分隔。

### Trim函数
* RTrim():去掉串右边的空格）
* LTrim():（去掉串左边的空格）
* Trim():（去掉串左右两边的空格）

```sql
select concat(RTrim(vend_name), '(', RTrim(vend_country), ')') from vendors order by vend_name;
```

### 使用别名
* 别名（alias）是一个字段或值的替换名。别名用`AS`关键字赋予.
* 别名有时也称为导出列（derived column）


### MySQL算数运算符
* `+` 
* `-` 
* `*` 
* `/`

## 第11章 使用数据处理函数
### 常用的文本处理函数
* `Left()`: 返回串左边的字符
* `Length()`: 返回串的长度
* `Locate()`: 找出串的一个子串
* `Lower()`: 将串转换为小写
* `LTrim()`: 去掉串左边的空格
* `Right()`: 返回串右边的字符
* `RTrim()`: 去掉串右边的空格
* `SubString()`: 返回子串的字符
* `Upper()`:将串转化为大写

### 日期和时间处理函数
用的比较多的就是MySQL中的now()函数，返回当前日期和时间(注：返回的不是时间戳)
![-w895](https://it-learn-1259257911.cos.ap-beijing.myqcloud.com/2019/09/30/15697604308690.jpg)

`unix_timestamp()`: 返回当前时间的时间戳（单位为秒）
`UNIX_TIMESTAMP(date)`:若用date 来调用 UNIX_TIMESTAMP()，它会将参数值以'1970-01-01 00:00:00' GMT后的**秒数**的形式返回。date 可以是一个 DATE **字符串**、一个 DATETIME字符串、一个 TIMESTAMP或一个当地时间的YYMMDD 或YYYMMDD格式的数字。

![-w806](https://it-learn-1259257911.cos.ap-beijing.myqcloud.com/2019/09/30/15697597212114.jpg)

## 第12章 汇总数据
* `AVG()` :返回某列的平均值
    * 忽略列值为NULL的行
* `COUNT()`:返回某列的行数
    * 如果指定列名，则指定列的值为空的行被COUNT()函数忽略
    * 如果COUNT（）函数中用的是星号`*`,则不忽略
* `MAX()`：返回某列的最大值
    * 忽略列值为NULL的行
* `MIN()`：返回某列的最小值
    * 忽略列值为NULL的行
* `SUM()`：返回某列值之和
    * 忽略列值为NULL的行

## 第13章 分组数据
 * GROUP BY
 * HAVING

### GROUP BY
 
`GROUP BY` 字句必须出现在`WHERE`字句之后，`ORDER BY`字句之前。

### HAVING
`WHERE`过滤指定的是行而不是分组。`HAVING`非常类似于`WHERE`。`WHERE`过滤行，而`HAVING`过滤分组。

```sql
SELECT cust_id, count(*) AS orders FROM orders GROUP BY cust_id HAVING COUNT(*) >=2
```

>HAVING和WHERE的差别:这里有另一种理解方法，WHERE在数据分组前进行过滤。HAVING在数据分组后进行过滤。这是一个重
要的区别，WHERE排除的行不包括在分组中。这可能会改变计算值，从而影响HAVING子句中基于这些值过滤掉的分组。

### 分组与排序

| ORDER BY | GROUP BY |
| --- | --- |
| 排序产生的输出 | 分组行。但输出可能不是分组的顺序 |
| 任意列都可以使用（甚至非选择的列也可与使用） | 只可能使用选择列或表达式列，而且必须使用每个选择列表达式 |
| 不一定需要 | 如果与聚集函数一起使用列（或表达式），则必须使用 |

### SELECT 子句顺序

| 字句 | 说明 | 是否必须使用 |
| --- | --- | --- |
| SELECT | 要返回的列或表达式 | 是 |
| FROM | 从中检索数据的表 | 仅在从表选择数据时使用 |
| WHERE | 行级过滤 | 否 |
| GROUP BY | 分组说明 | 仅在按组计算聚集时使用 |
| HAVING | 组级过滤 | 否 |
| ORDER BY | 输出排序顺序 | 否 |
| LIMIT | 要检索的行数 | 否 |

## 第14章 使用子查询
**使用场景**
子查询最常用的使用是在WHERE字句的IN操作符中，以及用来填充计算列。

### 利用子查询进行过滤
![-w1263](https://it-learn-1259257911.cos.ap-beijing.myqcloud.com/2019/09/30/15697724425209.jpg)

### 作为计算字段使用子查询
![-w1732](https://it-learn-1259257911.cos.ap-beijing.myqcloud.com/2019/09/30/15697731520749.jpg)

需要注意的是，子查询中的`WHERE`字句使用了完全限定列名。
```sql
WHERE orders.cust_id = customers.cust_id
```
这种类型的子查询称为**相关子查询**。任何时候只要列名可能有多义性，就必须使用这种语法（表明和列名由一个句点分隔）。

## 第15章 联结表
> 相同数据出现多次绝不是一件好事，此因素是关系数据库设计的基础。关系表的设计就是要保证把信息分解成多个表，一类数据一个表。各表通过某些常用的值（即关系设计中的关系互相关联。

外键： 外键为某个表中的一列，它包含另一个表的主键值，定义了两个表之间的关系。

### 什么是联结？
* 联结是一种机制，用来在一条SELECT语句中关联表，因此称之为联结。使用特殊的语法，可以联结多个表返回一组输出，联结在运行时关联表中正确的行。

创建连接
```sql
SELECT vend_name, prod_name, prod_price FROM vendors, products where vendors.vend_id = products.vend_id ORDER BY vend_name, prod_name;
```
### 内部连接
内部连接又称为等值连接（equijoin），它基于两个表之间的相等测试。
上述的SQL也可以写成如下形式：

```sql
SELECT vend_name, prod_name, prod_price FROM vendors INNER JOIN products ON vendors.vend_id = products.vend_id;
```
在使用这种语法时，联结条件用特定的`ON`子句而不是`WHERE`子句给出。传递给`ON`的实际条件与传递给`WHERE`的相同。

>使用哪种语法:ANSI SQL规范**首选`INNER JOIN`语法**。此外，尽管使用WHERE子句定义联结的确比较简单，但是使用明确的联结语法能够确保不会忘记联结条件，有时候这样做也能影响性能。

>MySQL在运行时关联指定的每个表以处理联结。 这种处理可能是非常耗费资源的，因此应该仔细，不要联结不必要的表。**联结的表越多，性能下降越厉害。 **

## 第16章 创建高级联结
### 使用表别名
别名除了用于**列名**和**计算字段**外，SQL还允许给表名起别名。这样做有两个主要理由：
* 缩短SQL语句
* 允许在单条SELECT语句中多次使用相同的表

表别名只在查询执行中使用。与列别名不一样，表别名不返回到客户机。

### 自联结
子查询
```sql
SELECT prod_id, prod_name FROM products WHERE vend_id = (
    SELECT vend_id FROM products WHERE prod_id = 'DTNTR'
);
```

自联结
```sql
SELECT p1.prod_id, p1.prod_name FROM products AS p1, products AS p2 WHERE p1.vend_id = p2.vend_id AND p2.prod_id = 'DTNTR';
```
> 用自联结而不用子查询 :自联结通常作为外部语句用来替代从相同表中检索数据时使用的子查询语句。虽然最终的结果是相同的，但有时候处理联结远比处理子查询快得多。应该试一下两种方法，以确定哪一种的性能更好 

### 自然联结
无论何时对表进行联结，应该至少有一个列出现在不止一个表中（被 联结的列）。标准的联结（前一章中介绍的内部联结）返回所有数据，甚至相同的列多次出现。自然联结排除多次出现，使每个列只返回一次。怎样完成这项工作呢？答案是，系统不完成这项工作，由你自己完成它。自然联结是这样一种联结，其中你只能选择那些唯一的列。这一般是通过对表使用通配符（SELECT *），对所有其他表的列使用明确的子集来完成的。下面举一个例子
```sql
SELECT c.*, o.order_num, o.order_date,oi.prod_id, o1.quantity, o2.item_price 
FROM customers AS c, orders as o, orderitems as oi 
WHERE c.cust_id = o.cust_id
AND oi.order_num = o.order_num
AND prod_id = 'FB';
```

### 外部联结
背景：
许多联结将一个表中的行与另一个表中的行相关联。但有时候会需要包含没有关联行的那些行。例如：计算平均销售规模，包括那些至今尚未下订单的客户。

**内部连接**
```sql
SELECT customes.cust_id, orders.order_num FROM customers INNER JOIN orders ON customers.cust_id = orders.cust_id;
```

外部联结
```sql
SELECT customes.cust_id, orders.order_num FROM customers LEFT OUTER JOIN orders ON customers.cust_id = orders.cust_id;
```

与内部联结关联两个表中的行不同的是，**外部联结还包括没有关联行的行**。在使用`OUTER JOIN`语法时，必须使用`RIGHT`或`LEFT`关键字指定包括其所有行的表.
上面的例子使用`LEFT OUTER JOIN`从FROM子句的左边表（customers表）中选择**所有行**。为了从右边的表中选择所 有行，应该使用`RIGHT OUTER JOIN`


>`left outer join`就是以左表当做基础，同时取右表和左表相同的行.即`LEFT OUTER JOIN`的功能就是将LEFT左边的表中的所有记录全部保留

## 第17章 组合查询
### 组合查询（复合查询）概念：
MySQL允许执行多个查询（多条SELECT语句），并将结果作为单个查询结果集返回。这些组合查询通常称为并（union）或复合查询（compound query）

### 创建组合查询
可用`UNION`操作符来组合数条`SQL`查询。利用`UNION`，可给出多条`SELECT`语句，将它们的结果组合成单个结果集。

`UNION`的使用很简单。所需做的只是给出每条`SELECT`语句，在各条语句之间放上关键字`UNION`。

![-w1228](https://it-learn-1259257911.cos.ap-beijing.myqcloud.com/2019/10/02/15700270933777.jpg)


### UNION规则
* UNION必须由两条或两条以上的SELECT语句组成，语句之间用关键字UNION分隔
* UNION中的每个查询必须包含相同的列、表达式或聚集函数（不过各个列不需要以相同的次序列出）。
* 列数据类型必须兼容：类型不必完全相同，但必须是DBMS可以隐含地转换的类型（例如，不同的数值类型或不同的日期类型）。

### 包含或取消重复的行
`UNION`默认从查询结果集中自动去除重复的行。如果想返回所有匹配行，可使用`UNION ALL`而不是`UNION`

### 对组合查询结果排序
`SELECT`语句的输出用`ORDER BY`子句排序。在用`UNION`组合查询时，只能使用一条`ORDER BY`子句，它必须出现在最后一条`SELECT`语句之后。对于结果集，不存在用一种方式排序一部分，而又用另一种方式排序另一部分的情况，因此**不允许使用多条`ORDER BY`子句。**

## 第18章 全文本搜索
* MyISAM支持全文本搜索，而InnoDB不支持。

LIKE通配符匹配和正则表达式匹配的限制：
* 性能-- 通配符和正则表达式匹配通常要求MySQL尝试匹配表中所有行。由于被搜索行数不断增加，这些搜索可能非常耗时。
* 明确控制-- 使用通配符和正则表达式匹配，很难（而且并不总是能）明确地控制匹配什么和不匹配什么
* 智能化的结果—— 虽然基于通配符和正则表达式的搜索提供了非常灵活的搜索，但它们都不能提供一种智能化的选择结果的方法。

### 使用全文本搜索
为了进行全文本搜索，必须索引被搜索的列，而且要随着数据的改变不断地重新索引。在对表列进行适当设计后，MySQL会自动进行所有的索引和重新索引。
在索引之后，`SELECT`可与`Match()`和`Against()`一起使用以实际执行搜索。

## 第19章 插入数据
### 插入完整的行
```sql
INSERT INTO Customers 
VALUES(...)
```
>虽然这种语法很简单，但并不安全，应该尽量避免使用。

```sql
INSERT INTO 表名（column1, column2...）
VALUES(value1, value2...)
```

### 插入多个行
```sql
INSERT INTO 表名（column1, column2...）
VALUES(value1, value2...);
INSERT INTO 表名（column1, column2...）
VALUES(value1, value2...);
```

OR
```sql
INSERT INTO 表名（column1, column2...）
VALUES(value1, value2...),
（column1, column2...）,
VALUES(value1, value2...);
```
其中单条INSERT语句有多组值，每组值用一对圆括号括起来，用逗号分隔。
>MySQL用单条INSERT语句处理多个插入比使用多条INSERT语句快。 

### 插入检索出的数据
`INSERT SELECT`:将一条`SELECT`语句的结果插入表中。顾名思义，它是由一条`INSERT`语句和一条`SELECT`语句组成的。
```sql
INSERT INTO customers(cust_id, cust_contact)
SELECT cust_id, cust_contact FROM custnew;
```

为简单起见，这个例子在INSERT和INSERT SELECT语句中使用了相同的列名。但是，不一定要求列名匹配。此SELECT中的第一列（不管其列名）将用来填充表列中指定的第一个列，第二列将用来填充表列中指定的第二个列，如此等等。