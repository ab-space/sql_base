# SQL基础知识-查询

#### 语法
```
SELECT select_expr [, select_expr ...] 
[
	FROM table_references
	[WHERE where_condition]
	[GROUP BY {col_name|position} [ASC|DESC], ... ]
	[HAVING having_condition]
	[ORDER BY {col_name|expr|position} [ASC|DESC], ...]
	[LIMIT {[offset,] row_count | row_count OFFSET offset}]
]
```

#### SELECT
1. 使用星号（*）来代替其他字段，SELECT语句会返回表的所有字段数据
2. 使用别名，使用AS操作符来将拼接的字符串命名为cust_city。(也可以省略AS关键字直接写别名)
```
SELECT concat(cust_name,':',cust_city) as cust_city FROM customers;
```
3. 计算字段
- 拼接字符串，使用concat函数可以将查询结果拼接起来
```
SELECT concat(cust_name,':',cust_city) FROM customers;
```
- 使用正则表达式，可以将REGEXP像like一样使用，并在REGEXP后面加上正则表达式即可。下面的表达式检索出的结果是，customers中所有cust_name中包含'Fun'的记录。
```
SELECT * FROM customers WHERE cust_name REGEXP 'Fun';
```
4. DISTINCT
```
-- 在查询语句中使用 DISTINCT 关键字来过滤重复数据
SELECT DISTINCT last_name, first_name FROM person;

-- 也可以使用 GROUP BY 来读取数据表中不重复的数据
SELECT last_name, first_name FROM person_tbl
GROUP BY (last_name, first_name);
```
5. CASE WHEN

这两种方式，可以实现相同的功能。简单Case函数的写法相对比较简洁，但是和Case搜索函数相比，功能方面会有些限制，比如写判断式。 
- 简单Case函数
```
SELECT
    CASE sex 
    WHEN '1' THEN '男' 
    WHEN '2' THEN '女' 
    ELSE '其他' END 
FROM table
```
- Case搜索函数
```
SELECT
    CASE WHEN sex = '1' THEN '男' 
         WHEN sex = '2' THEN '女' 
         ELSE '其他' END 
FROM table
```

6. LIMIT
```
-- 使用 LIMIT 属性来设定返回的记录数
SELECT * FROM orders LIMIT 2 OFFERSET 2;  
SELECT * FROM orders LIMIT 2 , 2;
-- 前面的数字2表示查询的数目条数，后面的2表示从哪个位置开始。
-- 所以，上面的效果是查询第3条和第4条数据。
-- 但是，要注意的是数据库中条的编号的起始位置是0。如果不指定第二个2将查出所有数据中前两个记录。
```

#### 目标：FROM
1. 别名AS，SQL语句的是自联结的，需要为两个相同的表分别指定一个别名。(也可以省略AS关键字直接写别名)
```
select * from customers as c1 where c1.name = '名'
```
2. 连接JOIN
- 内联结 INNER JOIN，获取两个表中字段匹配关系的记录。(也可以省略 INNER 使用 JOIN，效果一样)

![](http://ww1.sinaimg.cn/large/71db44b7gy1g64yx7fmisg205k041mx2.gif)
```
使用INNER JOIN sql:

SELECT 
a.id, a.author, b.count 
FROM runoob a 
INNER JOIN tcount b 
ON a.author = b.author;
```
```
不使用INNER JOIN sql:

SELECT 
a.id, a.author, b.count 
FROM runoob a, tcount b 
where a.author = b.author;
```
- 左外联结 LEFT JOIN，获取左表所有记录，即使右表没有对应匹配的记录。

![](http://ww1.sinaimg.cn/large/71db44b7gy1g64yxxyo29g205k0413yf.gif)
```
SELECT 
a.id, a.author, b.count 
FROM runoob a 
LEFT JOIN tcount b 
ON a.author = b.author;
```
- 右外联结 RIGHT JOIN，与 LEFT JOIN 相反，用于获取右表所有记录，即使左表没有对应匹配的记录。

![](http://ww1.sinaimg.cn/large/71db44b7gy1g64yy9d929g205k041a9z.gif)
```
SELECT 
a.id, a.author, b.count 
FROM runoob a 
RIGHT JOIN tcount b 
ON a.author = b.author;
```

#### 过滤：where

1. BETWEEN..AND

```
-- 查询order_num在200到300之间的记录
SELECT * FROM orders WHERE order_num BETWEEN 100 AND 300;
```
2. IN
```
-- 查询order_num为1、2、3的记录
SELECT * FROM orders WHERE order_num in (1, 2, 3);  
```
3. IS NULL
```
-- 查询order_num为null的记录
SELECT * FROM orders WHERE order_num IS NULL;  
```
4. AND
```
-- 查询order_num为200并且cust_id为100的数据
SELECT * FROM orders WHERE order_num = 200 AND cust_id = 100;
```
5. OR
```
-- 查询order_num为200或者且cust_id为100的数据
SELECT * FROM orders WHERE order_num = 200 OR cust_id = 100;
```
6. NOT
```
-- 查询order_num不为200的数据，等同于order_num != 200
SELECT * FROM orders WHERE NOT order_num = 200;
```
7. LIKE
```
-- LIKE 通常与 % 一同使用，使用百分号 %字符来表示任意字符
-- 查询order_num以2开头的数据
SELECT * FROM orders WHERE order_num LIKE '2%';
```

#### 分组：GROUP BY

GROUP BY 语句根据一个或多个列对结果集进行分组。

HAVING 对分组后的数据进行过滤。
```
-- 查询指定order_num的记录的总条数
SELECT order_num, count(order_num) FROM orderitems 
GROUP BY order_num;

-- 查询orders表中order_num小于100的数据，以及每条订单对应的总价（根据orderitems表计算得出）
SELECT t.*, SUM(quantity*item_price) AS total 
FROM orders as t, orderitems 
WHERE t.order_num = orderitems.order_num 
GROUP BY order_num
HAVING order_num < 100;
```

#### 排序：ORDER BY

你可以使用任何字段来作为排序的条件，从而返回排序后的查询结果。

你可以设定多个字段来排序。

你可以使用 ASC 或 DESC 关键字来设置查询结果是按升序或降序排列。 默认情况下，它是按升序排列。

你可以添加 WHERE...LIKE 子句来设置条件。

```
-- 语法
SELECT field1, field2,...fieldN FROM table_name1, table_name2...
ORDER BY field1 [ASC [DESC][默认 ASC]], [field2...] [ASC [DESC][默认 ASC]]

-- 查询所有订单并按照订单号降序排列
SELECT * FROM order ORDER BY order_num DESC;
```
#### 子查询

```
-- 查询用户名和用户对应的订单总额
SELECT c.cust_name, (SELECT SUM(i.quantity*i.item_price) 
                     FROM ORDERITEM as i, orders as o
                     WHERE i.order_num = o.order_num and c.cust_id = o.cust_id 
                     GROUP BY o.cust_id) AS total 
FROM customers AS c;

-- 子查询的另一种是用方式是将子查询的结果放在一个IN中作为取值范围
SELECT * FROM orders where order_num IN (
    SELECT order_num FROM ORDERITEM where item_price < 500
);

```
#### 组合：UNION

```
-- MySQL UNION 操作符用于连接两个以上的 SELECT 语句的结果组合到一个结果集合中（两个SQL检索出的列的数目要相等）。多个 SELECT 语句会删除重复的数据，可以使用UNION ALL关键字来替代UNION，这样重复的行就不会被取消。

-- 语法
SELECT expression1, expression2, ... expression_n
FROM tables
[WHERE conditions]
UNION [ALL | DISTINCT]
SELECT expression1, expression2, ... expression_n
FROM tables
[WHERE conditions];

-- expression1, expression2, ... expression_n: 要检索的列。

-- tables: 要检索的数据表。

-- WHERE conditions: 可选， 检索条件。

-- DISTINCT: 可选，删除结果集中重复的数据。默认情况下 UNION 操作符已经删除了重复数据，所以 DISTINCT 修饰符对结果没啥影响。

-- ALL: 可选，返回所有结果集，包含重复数据。
```
#### 聚合函数

1. 求平均值：AVG

```
-- AVG()函数计算一组值的平均值。 它计算过程中是忽略NULL值的。、
-- 语法：AVG(expression)

-- 计算所以有产品的平均价格
SELECT AVG(buyprice) 'Avarage Price' FROM products;

-- 计算每个产品线的产品的平均价格，您将使用带有GROUP BY子句的AVG函数
SELECT productline,
       AVG(buyprice) 'Avarage Price'
FROM products
GROUP BY productline;

-- 选择产品平均价格大于50的产品线
SELECT productline, AVG(buyprice) 'Avarage Price' FROM products GROUP BY productline HAVING AVG(buyprice) > 50;

-- 多次使用AVG()函数来计算一组平均值的平均值，计算产品线平均购买价格的平均买价
SELECT AVG(pl_avg) 'Average Product'
FROM (
    SELECT AVG(buyprice) pl_avg
    FROM products
    GROUP BY productline
) avgs;
```
2. 统计行的数量：COUNT

```
-- COUNT()函数返回表中的行数。 COUNT()函数允许您对表中符合特定条件的所有行进行计数。
-- 语法：COUNT(expression)

-- COUNT(*)：返回表中满足where条件的行的数量
SELECT COUNT(*) FROM product where product_num > 2;

-- COUNT(列)：返回列值非空的行的数量
SELECT COUNT(product_num) FROM product;

-- COUNT(DISTINCT 列)：返回列值非空的、并且列值不重复的行的数量
SELECT COUNT(DISTINCT product_num) FROM product;
```
3. 求最大值：MAX

```
-- MAX()函数返回一组值中的最大值。
-- 语法：MAX(expression)

-- 获取products表中最昂贵的产品
SELECT MAX(buyPrice) highest_price FROM products;

-- 获取最大支付金额的详细信息
SELECT 
    *
FROM
    payments
WHERE
    amount = (
        SELECT 
            MAX(amount)
        FROM
            payments);
```
4. 求最大值：MIN

```
-- MIN()函数返回一组值中的最小值
-- 语法： MIN(expression)

-- MIN()函数在products表中查找最低价格的产品
SELECT MIN(buyPrice) lowest_price FROM Products;

-- 查询价格最低的产品代码和产品名称等信息
SELECT 
    productCode, productName, buyPrice
FROM
    products
WHERE
    buyPrice = (
        SELECT 
            MIN(buyPrice)
        FROM
            products);
```
5. 求总和：SUM

```
-- SUM()函数用于计算一组值或表达式的总和
-- 语法：SUM(DISTINCT expression)

-- 计算订单编号10100的总金额
SELECT SUM(quantityOrdered * priceEach) AS total FROM orderdetails WHERE orderNumber = 10100;

-- 使用具有GROUP BY子句的SUM函数计算每个订单的总金额
SELECT orderNumber,
       SUM(quantityOrdered * priceEach) total
FROM orderdetails
GROUP BY orderNumber
ORDER BY SUM(quantityOrdered * priceEach) DESC;

-- 前十名最昂贵的产品的总和
SELECT SUM(buyprice) FROM
(SELECT buyprice
FROM products
ORDER BY buyprice DESC
LIMIT 10) price;
```

#### 参考资源
[一张图带你过SQL基础知识(思维导图)](https://blog.csdn.net/chenbifeng/article/details/65936304)
