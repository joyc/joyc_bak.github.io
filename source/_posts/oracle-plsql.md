---
title: Oracle PL/SQL 入门
date: 2017-08-21 22:17:07
tags: PL/SQL
---
### 正则表达式
#### 字符类别
类别 | 描述
---|---
\d | 任意数字（等同于[0-9]）
\D | 任意非数字字符（等同于[^0-9]）
\w | 任意字母或数字（等同于[a-zA-Z0-9]）
\W | 任意非字母或数字字符（等同于[^a-zA-Z0-9]）
\s | 任意空白字符
\S | 任意非空白字符

#### 重复元字符
元字符 | 描述
---|---
* | 0个或多个匹配
+ | 1个或多个匹配（等价于{1,}）
? | 0个或1个匹配（等价于{0,1}）
{n} | 具体的匹配次数
{n,} | 不少于指定的匹配次数
{n,m}| 匹配的范围


#### 锚元字符
锚 | 描述
---|---
^ | 文本的开头
$ | 文本的末尾
^有两种用法：在字符集内（使用[和]定义），使用它来对字符集取反；否则，将把它用于指示字符串的开头。


```sql
/* SELECT prod_name, vend_id
FROM products; */

SELECT DISTINCT vend_id -- DISTINCT 关键字用法
FROM products;

SELECT prod_id, prod_price, prod_name
FROM products
ORDER BY prod_price DESC;

SELECT prod_name, prod_price
FROM products
WHERE prod_price BETWEEN 5 AND 10
ORDER BY prod_name;

SELECT prod_name, prod_price
FROM products
WHERE vend_id = 1002 OR vend_id = 1003
ORDER BY prod_name;

SELECT prod_name, prod_price
FROM products
-- WHERE (vend_id = 1002 OR vend_id = 1003) AND prod_price >= 10
WHERE vend_id IN (1002,1003) AND prod_price >= 10 -- 可以使用IN
ORDER BY prod_name;

SELECT prod_id, prod_name
FROM products
WHERE prod_name LIKE '_ ton anvil%';

SELECT prod_name
FROM products
WHERE REGEXP_LIKE (prod_name, '.000') -- 正则函数
ORDER BY prod_name;

SELECT prod_name
FROM products
WHERE REGEXP_LIKE(prod_name, '1000|2000|3000')
ORDER BY prod_name;

SELECT prod_name
FROM products
WHERE REGEXP_LIKE(prod_name,'[1-5] ton')
ORDER BY prod_name;

SELECT vend_name
FROM vendors
WHERE REGEXP_LIKE(vend_name, '\.'); -- 用转义符"\"搜索"."

SELECT prod_name
FROM products
-- WHERE REGEXP_LIKE(prod_name, '\(\d sticks?\)')
WHERE REGEXP_LIKE(prod_name, '\d{4}')
ORDER BY prod_name;

```

#### 常用文本操作函数
函数 | 描述
---|---
Length() | 返回字符串的长度
Lower() | 把字符串转换成小写形式
LPad() | 在字符串左边填充空格
LTrim() | 从字符串左边修剪掉空白
RPad() | 在字符串右边填充空格
RTrim() | 从字符串右边修剪掉空白
Soundex() | 返回字符串的SOUNDEX值
SubString() | 返回字符串内的字符
Upper() | 把字符串转换成大写形式

#### 常用的日期和时间操作函数
函数 | 描述
---|---
Add_Month() | 给日期添加月份（也可以减去月份）
Extract() | 从日期和时间中减去年、月、日、时、分或秒
Last_Day() | 返回月份的最后一天
Months_Between() | 返回两个月份之间的月数
Next_Day()| 返回指定日期后面的那一天
Sysdate() | 返回当前日期和时间
To_Date() | 把字符串转换成日期

#### 常用的数值操作函数
函数 | 描述
---|---
Abs() | 返回数字的绝对值
Cos() | 返回指定角度的三角余弦值
Exp() | 返回指定数字的指数值
Mod() | 返回除法运算的余数
Sin() | 返回指定角度的三角正弦值
Sqrt() | 返回指定数字的平方根
Tan() | 返回指定角度的三角正切值

#### SQL聚合函数
函数 | 描述
---|---
AVG() | 返回列的平均值
COUNT() | 返回列中的行数
MAX() | 返回列的最大值
MIN() | 返回列的最小值
SUM() | 返回列的值汇总

- 可以使用COUNT()的两种方式是：
    - 使用COUNT(*)统计表中的行数，无论列包含的是数值还是NULL值；
    - 使用COUNT(column)统计在特定列中具有值（忽略NULL值）的行数。

- GROUP BY子句必须出现在WHERE子句的后面和ORDER BY子句的前面。

- HAVING与WHERE的区别：  
WHERE过滤发生在数据分组之前，而HAVING过滤则发生在数据分组之后。这是一个重要的区别，被WHERE子句删除的行不会包括在分组中。这可能会改变计算值，基于HAVING子句中使用的那些值，它反过来又可能影响哪些分组将会被过滤。

#### ORDER BY与GROUP BY的比较
ORDER BY | GROUP BY
---|---
对生成的输出进行排序 | 对行进行分组，不过输出可能没有采用分组顺序
可能使用任意列（甚至包括没有选择的列） | 只可能使用所选的列或表达式列，并且一定会使用所有选择的列表达式
从来都不是必需的 | 如果结合使用列（或表达式）与聚合函数，则是必需的

#### SELECT子句和它们的顺序
子句 | 描述 | 是否必需
---|---|---
SELECT|要返回的列或表达式|是
FROM|要从中检索数据的表|是（Oracle）
WHERE|行级过滤|否
GROUP BY|分组规范|仅当按分组计算聚合值时是必需的
HAVING|分组级过滤|否
ORDER BY|输出的排列顺序|否

#### 查询示例
```sql
SELECT RTRIM(vend_name) || ',(' || RTRIM（vend_country） || ')'
AS vend_title
FROM vendors
ORDER BY vend_name;

SELECT prod_id, quantity, item_price,
       quantity * item_price AS expanded_price
FROM orderitems
WHERE order_num = 20005;

SELECT cust_id, order_num
FROM orders
WHERE order_date = TO_DATE('2015-02-01', 'yyyy-mm-dd');
SELECT cust_id, order_num
FROM orders
WHERE order_date>= TO_DATE('2015-02-01', 'yyyy-mm-dd')
   AND order_date< TO_DATE('2015-02-02', 'yyyy-mm-dd');
   
SELECT cust_id, order_num
FROM orders
-- 检索order_date在2015年和2月的所有行
WHERE Extract(Year FROM order_date) = 2015
   AND Extract(Month FROM order_date) = 2;
   
SELECT AVG(prod_price) AS avg_price
FROM products
WHERE vend_id = 1003;

SELECT COUNT(*) AS num_cust
FROM customers;

SELECT COUNT(cust_email) AS num_cust
FROM customers;

SELECT SUM(quantity) AS items_ordered
FROM orderitems
WHERE order_num = 20005;

SELECT SUM(item_price*quantity) AS total_price
FROM orderitems
WHERE order_num = 20005;

SELECT COUNT(*) AS num_items,
       MIN(prod_price) AS price_min,
       MAX(prod_price) AS price_max,
       AVG(prod_price) AS price_avg
FROM products;

SELECT vend_id, COUNT(*) AS num_prods 
FROM products
GROUP BY vend_id; -- GROUP BY 聚合分组

SELECT cust_id, COUNT(*) AS orders
FROM orders
GROUP BY cust_id
HAVING COUNT(*) >= 2; -- HAVING用于过滤分组 等同于过滤行的 WHERE

/* 列出具有2件或更多产品并且价格在10以上（含10）的所有供应商 */
SELECT vend_id, COUNT(*) AS num_prods
FROM products
WHERE prod_price >= 10
GROUP BY vend_id
HAVING COUNT(*) >= 2;

/* 检索总价在50以上（含50）的所有订单的订单号和订单总价,并按订单总价对输出进行排序 */
SELECT order_num, SUM(quantity * item_price) AS order_total
FROM orderitems
GROUP BY order_num
HAVING SUM(quantity * item_price) >= 50
ORDER BY order_total;

/* 子查询：查询订购商品TNT2的所有顾客的信息，下面有连接表方法查询 */
SELECT cust_name, cust_contact
FROM customers
WHERE cust_id IN (SELECT cust_id
                  FROM orders
                  WHERE order_num IN (SELECT order_num
                                      FROM orderitems
                                      WHERE prod_id = 'TNT2'));

SELECT cust_name,
       cust_state,
       (SELECT COUNT(*)
       FROM orders
       WHERE orders.cust_id = customers.cust_id) AS orders -- orders为计算字段
FROM customers
ORDER BY cust_name;

/* 表的连接，必须使用完全限定的列名（用点号把表和列分隔开） */
SELECT vend_name, prod_name, prod_price
FROM vendors, products
WHERE vendors.vend_id = products.vend_id
ORDER BY vend_name, prod_name;
-- 等同于下面的内连接，WHERE更简化，INNER更清晰
SELECT vend_name, prod_name, prod_price
FROM vendors INNER JOIN products
ON vendors.vend_id = products.vend_id
ORDER BY vend_name, prod_name;

-- 显示了订单号20005中的商品
SELECT prod_name, vend_name, prod_price, quantity
FROM orderitems, products, vendors
WHERE products.vend_id = vendors.vend_id
   AND orderitems.prod_id = products.prod_id
   AND order_num = 20005;

/* 使用连接表：查询订购商品TNT2的所有顾客的信息，上面有子查询方法 */
-- WHERE连接表
SELECT cust_name, cust_contact
FROM customers, orders, orderitems
WHERE customers.cust_id = orders.cust_id
   AND orderitems.order_num = orders.order_num
   AND prod_id = 'TNT2';
-- FROM子句，INNER JOIN ON实现
SELECT cust_name, cust_contact
FROM customers
INNER JOIN orders ON customers.cust_id = orders.cust_id
INNER JOIN orderitems ON orderitems.order_num = orders.order_num
WHERE prod_id = 'TNT2';
```

### 表别名和列别名

列别名使用 `AS`:   
```sql
SELECT RTrim(vend_name) || ', (' || RTrim(vend_country) || ')'
  AS vend_title
FROM vendors
ORDER BY vend_name;
```
表别名如下：
```sql
SELECT cust_name, cust_contact
FROM customers c, orders o, orderitems oi
WHERE c.cust_id = o.cust_id
  AND oi.order_num = o.order_num
  AND prod_id = 'TNT2';
```
> 注意：只在查询执行期间使用表别名。与列别名不同，表别名永远不会返回给客户。

```sql
/* 查询商品ID为DTNTR的商品的同一个供应商制造的所有产品 */
-- 子查询方法
SELECT prod_id, prod_name 
FROM products
WHERE vend_id = (SELECT vend_id
                 FROM products
                 WHERE prod_id = 'DTNTR')
ORDER BY prod_id;
/* 自连接 */
-- 连接表 WHERE
SELECT p1.prod_id, p1.prod_name
FROM products p1, products p2
WHERE p1.vend_id = p2.vend_id
    AND p2.prod_id = 'DTNTR'
ORDER BY prod_id;
-- 连接表 INNER JOIN ON
SELECT p1.prod_id, p1.prod_name
FROM products p1
INNER JOIN products p2 ON p1.vend_id = p2.vend_id
WHERE p2.prod_id = 'DTNTR'
ORDER BY prod_id;

/* 自然连接 */
SELECT c.*, o.order_num, o.order_date,   -- c.*为customers表中所有列
            oi.prod_id, oi.quantity, OI.item_price
FROM customers c, orders o, orderitems oi
WHERE c.cust_id = o.cust_id
   AND oi.order_num = o.order_num
   AND prod_id = 'FB';
   
SELECT c.*, o.order_num, o.order_date,
            oi.prod_id, oi.quantity, OI.item_price
FROM customers c
INNER JOIN orders o ON c.cust_id = o.cust_id
INNER JOIN orderitems oi ON oi.order_num = o.order_num
WHERE prod_id = 'FB';   

/* 外连接：检索所有顾客及其订单 */
SELECT c.cust_id, o.order_num
FROM customers c
INNER JOIN orders o ON c.cust_id = o.cust_id;
-- 检索所有顾客的列表，包括那些没有下订单的顾客
SELECT customers.cust_id, orders.order_num
FROM customers
LEFT OUTER JOIN orders ON customers.cust_id = orders.cust_id;

```
与将两个表中的行相关联的内连接不同，外连接还包括没有相关行的行。
当使用`OUTER JOIN`语法时，必须使用RIGHT或LEFT关键字指定要包括所有行的表（RIGHT用于OUTER JOIN右边的表，LEFT则用于左边的表）。前面的示例在FROM子句中使用LEFT OUTER JOIN从左边的表（customers表）中选择所有的行。要从右边的表中选择所有的行，可以使用`RIGHT OUTER JOIN`。

> 外连接类型:  
有两种基本的外连接形式：左外连接和右外连接，它们之间的唯一区别是相关联的表的顺序。换句话说，可以把左外连接转变成右外连接，只需在FROM或WHERE子句中颠倒表的顺序即可。因此，可以互换地使用两类外连接，而要使用哪种外连接则取决于方便性。

```sql
/* 检查所有顾客的列表以及每位顾客下的订单数量 */
-- 内连接
SELECT customers.cust_name,
       COUNT(orders.order_num) AS num_ord
FROM customers
INNER JOIN orders ON customers.cust_id = orders.cust_id
GROUP BY customers.cust_name;
-- 外连接
SELECT customers.cust_name,
       COUNT(orders.order_num) AS num_ord
FROM customers
LEFT OUTER JOIN orders ON customers.cust_id = orders.cust_id
GROUP BY customers.cust_name;

/* 组合查询 UNION */
/* 价格在5以下的所有产品，包括由供应商1001和1002制造的所有产品,不管价格 */
SELECT vend_id, prod_id, prod_price
FROM products
WHERE prod_price <= 5
UNION
SELECT vend_id, prod_id, prod_price
FROM products
WHERE vend_id IN (1001,1002)
ORDER BY vend_id, prod_price;

SELECT vend_id, prod_id, prod_price
FROM products
WHERE prod_price <= 5
  OR vend_id IN (1001,1002);
```
> 使用UNION ALL，Oracle将不会消除重复的行。

### INSERT 插入的用法
```sql
/* INSERT语句用法示例 */
-- 下面是依赖列顺序的，强烈不推荐
INSERT INTO Customers
VALUES(10006,
       'Pep E. LaPew',
       '100 Main Street',
       'Los Angeles',
       'CA',
       '90046',
       'USA',
       NULL,
       NULL);
-- 推荐写法
INSERT INTO customers(cust_id,
                      cust_name,
                      cust_address,
                      cust_city,
                      cust_state,
                      cust_zip,
                      cust_country)
VALUES(10006,
       'Pep E. LaPew',
       '100 Main Street',
       'Los Angeles',
       'CA',
       '90046',
       'USA');
       
       
-- 使用INSERT SELECT把custnew中的所有数据导入到customers中       
INSERT INTO custnew(cust_id,
                    cust_contact,
                    cust_email,
                    cust_name,
                    cust_address,
                    cust_city,
                    cust_state,
                    cust_zip,
                    cust_country)
SELECT cust_id,
       cust_contact,
       cust_email,
       cust_name,
       cust_address,
       cust_city,
       cust_state,
       cust_zip,
       cust_country
FROM custnew;
```
### UPDATE更新数据

```sql
-- 更新顾客10005的电子邮件
UPDATE customers
SET cust_email = 'elmer@fudd.com'
WHERE cust_id = 10005;

-- 更新多列数据
UPDATE customers
SET cust_name = 'The Fudds',
    cust_email = 'elmer@fudd.com'
WHERE cust_id = 10005;

-- 更新为空值（删除列）
UPDATE customers
SET cust_email = NULL
WHERE cust_id = 10005;

```
> UPDATE语句需要结束于一个WHERE子句，它告诉Oracle要更新哪一行,否则更新所有行。

### DELETE删除数据的使用
```sql
-- 从customers表中删除单独一行
DELETE FROM customers
WHERE cust_id = 10006;
```
> DELETE不接受列名或者通配符，它将删除整行，而不是删除列。要删除特定的列，可以使用UPDATE语句。  
永远不要执行不带有WHERE子句的UPDATE或DELETE，除非确实打算更新和删除每一行。
### 创建表
```sql
---------------------------------------------
-- Create customers table
---------------------------------------------
CREATE TABLE customers
(
  cust_id   int      NOT NULL ,
  cust_name char(50) NOT NULL ,
  cust_address char(50)  NULL ,
  cust_city    char(50)  NULL ,
  cust_state   char(5)   NULL ,
  cust_zip     char(10)  NULL ,
  cust_country char(50)  NULL ,
  cust_contact char(50)  NULL ,
  cust_email   char(255) NULL
);


---------------------------------------------
-- Create orderitems table
---------------------------------------------
CREATE TABLE orderitems
(
  order_num  int          NOT NULL ,
  order_item int          NOT NULL ,
  prod_id    char(10)     NOT NULL ,
  quantity   int          DEFAULT 1 NOT NULL , -- 设置默认值
  item_price decimal(8,2) NOT NULL
);
```
#### ALTER TABLE更新表定义
```sql
-- 向表中添加一列
ALTER TABLE vendors
ADD vend_phone CHAR(20);

-- 删除添加列
ALTER TABLE vendors
DROP COLUMN vend_phone;

-- 定义主键
----------------------
-- Define primary keys
----------------------
ALTER TABLE customers ADD CONSTRAINT pk_customers
                      PRIMARY KEY (cust_id);
ALTER TABLE orderitems ADD CONSTRAINT pk_orderitems
                       PRIMARY KEY (order_num, order_item);
ALTER TABLE orders ADD CONSTRAINT pk_orders
                   PRIMARY KEY (order_num);
ALTER TABLE products ADD CONSTRAINT pk_products
                     PRIMARY KEY (prod_id);
ALTER TABLE vendors ADD CONSTRAINT pk_vendors
                    PRIMARY KEY (vend_id);
ALTER TABLE productnotes ADD CONSTRAINT pk_productnotes
                         PRIMARY KEY (note_id);      
                         
-- 定义外键
---------------------------------------------
-- Define foreign keys
---------------------------------------------
ALTER TABLE orderitems
      ADD CONSTRAINT fk_orderitems_orders FOREIGN KEY (order_num)
      REFERENCES orders (order_num);
ALTER TABLE orderitems
      ADD CONSTRAINT fk_orderitems_products
      FOREIGN KEY (prod_id) REFERENCES products (prod_id);
ALTER TABLE orders
      ADD CONSTRAINT fk_orders_customers FOREIGN KEY (cust_id)
      REFERENCES customers (cust_id);
ALTER TABLE products
      ADD CONSTRAINT fk_products_vendors
      FOREIGN KEY (vend_id) REFERENCES vendors (vend_id);
ALTER TABLE productnotes
      ADD CONSTRAINT fk_productnotes_products
      FOREIGN KEY (prod_id) REFERENCES products (prod_id);
      
```
#### 删除表
```sql
-- 永久删除整个表customers2
DROP TABLE customers2; 
```

#### 重命名表
```sql
ALTER TABLE customers2 RENAME TO customers;
```