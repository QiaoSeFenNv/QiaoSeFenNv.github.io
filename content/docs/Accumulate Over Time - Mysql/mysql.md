---
title: "Accumulate Over Time - MySQL"
description: ""
summary: ""
date: 2023-09-07T16:13:18+02:00
lastmod: 2023-09-07T16:13:18+02:00
draft: false
images: []
menu:
docs:
parent: ""
identifier: "MySQL-22e9ba845827sdf9891199cf8db3a08cd"
weight: 910
toc: true
---
## 判断表中的值唯一

- 为了判断一个值在某一列中是不是唯一的，我们可以使用 `GROUP BY` 和 `COUNT`。

**要求：** 2015 年的投保额 (tiv_2015) 至少跟一个其他投保人在 2015 年的投保额相同

```markdown
+-----+----------+----------+-----+-----+
| pid | tiv_2015 | tiv_2016 | lat | lon |
+-----+----------+----------+-----+-----+
| 1   | 10       | 5        | 10  | 10  |
| 2   | 20       | 20       | 20  | 20  |
| 3   | 10       | 30       | 20  | 20  |
| 4   | 10       | 40       | 40  | 40  |
+-----+----------+----------+-----+-----+
```

>***Tip***
>
>```mysql
>SELECT
>        TIV_2015
>      FROM
>        insurance
>      GROUP BY TIV_2015
>      HAVING COUNT(*) > 1
>```
>
>摘自：[leetCode 585. 2016年的投资](https://leetcode.cn/problems/investments-in-2016/)






## 窗口函数的运用

### 查询多个连续的数据

- 对于连续性问题我们可以使用`row_number()`开窗函数产生新字段判断是否连续

那么我们需要先知道`row_number()`是做什么用的！我们先不看定义，直接来看使用。

```diff
学生表：
+-----------+-------+
| Student   | Score |
+-----------+-------+
| Alice     | 85    |
| Bob       | 85    |
| Carol     | 92    |
| Dave      | 85    |
| Eve       | 88    |
+-----------+-------+
```

此时，如果我们相对其进行排名，我们是不是第一个想到`order By`。是的，`order By`可以做到，但是

`row_number()`也可以，并且可以生成一个排名序列

```mysql
SELECT
    Student,
    Score
FROM
    students
ORDER BY students.Score desc 
+--------+-------+
| Student| Score |
+--------+-------+
| Carol  |   92  |
| Eve    |   88  |
| Alice  |   85  |
| Bob    |   85  |
| Dave   |   85  |
+--------+-------+
--------------------------------------
SELECT
    Student,
    Score,
    ROW_NUMBER() OVER (ORDER BY Score DESC) AS Ranking
FROM
    students;
+--------+-------+---------+
| Student| Score | Ranking |
+--------+-------+---------+
| Carol  | 92    |   1     |
| Eve    | 88    |   2     |
| Alice  | 85    |   3     |
| Bob    | 85    |   4     |
| Dave   | 85    |   5     |
+--------+-------+---------+
```

如果此时，我们需要分组（分区）之后再进行排序呢？例如，相同分数进行排序，或者说再一定区间访问内进行排序？

```mysql
如果是想定义一个区间来进行分组排序的话。ORDER BY 和 GROUP BY 就不好实现了。但是，ROW_NUMBER()。却可以很好的解决该问题。
SELECT
	Student,
	Score,
	ROW_NUMBER() OVER ( PARTITION BY Score ORDER BY Score ) AS Ranking 
FROM
	students

+--------+-------+---------+
| Student| Score | Ranking |
+--------+-------+---------+
| Alice  |   85  |   1     |
| Bob    |   85  |   2     |
| Dave   |   85  |   3     |
| Eve    |   88  |   1     |
| Carol  |   92  |   1     |
+--------+-------+---------+
----------------------------------------
如果是分组区间呢，例如，每10分一组一组。想法就是为每一个区间中的数字定义一个相同的组编号，使用 ROW_NUMBER() 函数对组编号进行区分（区分使用 PARTITION BY ）
SELECT
    Student,
    Score,
    (FLOOR((Score - 1) / 10) * 10 + 1) AS ScoreGroup,
    ROW_NUMBER() OVER (PARTITION BY (FLOOR((Score - 1) / 10) * 10 + 1) ORDER BY Score desc) AS Ranking
FROM
    students
ORDER BY
    ScoreGroup, Ranking;
+--------+-------+------------+---------+
| Student| Score | ScoreGroup | Ranking |
+--------+-------+------------+---------+
| Eve    |   88  |     81     |    1    |
| Alice  |   85  |     81     |    2    |
| Bob    |   85  |     81     |    3    |
| Dave   |   85  |     81     |    4    |
| Carol  |   92  |     91     |    1    |
+--------+-------+------------+---------+
```

那么，看到这里，你对 `ROW_NUMBER()`函数的使用已经有了大致了解，那么最后，让我们看看`ROW_NUMBER()`的概念吧

`ROW_NUMBER()` 是一个在 MySQL 和其他许多关系型数据库管理系统中可用的窗口函数（Window Function）。它用于为查询结果集中的每一行分配一个唯一的整数值，该值根据指定的排序顺序进行分配。通常，`ROW_NUMBER()` 用于为结果集中的行进行排名或分页操作。

以下是关于 `ROW_NUMBER()` 函数的详细介绍以及如何使用它：

**语法：**

```
ROW_NUMBER() OVER (PARTITION BY partition_expression ORDER BY sort_expression)
```

- `ROW_NUMBER()`: 表示要执行的窗口函数，用于分配唯一的行号。
- `OVER`: 用于指定窗口规范。
- `PARTITION BY`: 用于将结果集分成分区（可选）。如果指定了此子句，窗口函数将在每个分区内独立计算行号。如果省略此子句，将在整个结果集上计算行号。
- `ORDER BY`: 用于指定排序顺序，根据哪个列的值分配行号。



### 一定访问时间内的数据

**要求：** 如何累加一段时间区间内的值。我们可以考虑使用开窗函数。这里我们再补充一些开窗函数的知识

```mysql
窗口函数该怎么写？

[你要的操作] OVER ( PARTITION BY  <用于分组的列名>
                    ORDER BY <按序叠加的列名> 
                    ROWS <窗口滑动的数据范围> )


<窗口滑动的数据范围> 用来限定[ 你要的操作] 所运用的数据的范围，具体有如下这些：

当前行 - current row
之前的行 - preceding
之后的行 - following
无界限 - unbounded
表示从前面的起点 - unbounded preceding
表示到后面的终点 - unbounded following

eg：
取当前行和前五行：ROWS between 5 preceding and current row --共6行
取当前行和后五行：ROWS between current row and 5 following --共6行
取前五行和后五行：ROWS between 5 preceding and 5 folowing --共11行

```

```diff
Customer 表:
+-------------+--------------+--------------+-------------+
| customer_id | name         | visited_on   | amount      |
+-------------+--------------+--------------+-------------+
| 1           | Jhon         | 2019-01-01   | 100         |
| 2           | Daniel       | 2019-01-02   | 110         |
| 3           | Jade         | 2019-01-03   | 120         |
| 4           | Khaled       | 2019-01-04   | 130         |
| 5           | Winston      | 2019-01-05   | 110         | 
| 6           | Elvis        | 2019-01-06   | 140         | 
| 7           | Anna         | 2019-01-07   | 150         |
| 8           | Maria        | 2019-01-08   | 80          |
| 9           | Jaze         | 2019-01-09   | 110         | 
| 1           | Jhon         | 2019-01-10   | 130         | 
| 3           | Jade         | 2019-01-10   | 150         | 
+-------------+--------------+--------------+-------------+
+--------------+--------------+----------------+
| visited_on   | amount       | average_amount |
+--------------+--------------+----------------+
| 2019-01-07   | 860          | 122.86         |
| 2019-01-08   | 840          | 120            |
| 2019-01-09   | 840          | 120            |
| 2019-01-10   | 1000         | 142.86         |
+--------------+--------------+----------------+
```

>***Tip***
>
>```mysql
>SELECT
>   visited_on,
>   sum_amount amount,
>   ROUND( sum_amount / 7, 2 ) average_amount
>FROM (
>   SELECT
>      visited_on,
>      SUM( sum_amount ) OVER ( ORDER BY to_days(visited_on) RANGE BETWEEN 6 PRECEDING AND current ROW ) sum_amount
>   FROM (
>      SELECT
>         visited_on,
>         SUM( amount ) sum_amount
>      FROM Customer
>      GROUP BY visited_on
>   ) tmp1
>) tmp2
>WHERE DATEDIFF(visited_on, ( SELECT MIN( visited_on ) FROM Customer )) >= 6;
>```
>
>上述内容摘自：[1321. 餐馆营业额变化增长 评论](https://leetcode.cn/problems/restaurant-growth/) 



## 联合数据

- 为了人我们可以将表中不同的列放置在同一个列中，我们可以使用 `UNION` 和 `UNION ALL` 字段来完成。

**要求：** 如何将 requester_id 字段和 accepter_id 字段整合为一组可以重复的表

```
+--------------+-------------+-------------+
| requester_id | accepter_id | accept_date |
+--------------+-------------+-------------+
| 1            | 2           | 2016/06/03  |
| 1            | 3           | 2016/06/08  |
| 2            | 3           | 2016/06/08  |
| 3            | 4           | 2016/06/09  |
+--------------+-------------+-------------+
```

>***Tip***
>
>```mysql
>select requester_id as ids from RequestAccepted
>union all
>select accepter_id as ids from RequestAccepted
>```
>
>`UNION`合并的数据不可重复
>
>`UNION` 合并的数据可以重复
>
>摘自：[602. 好友申请 II ：谁有最多的好友](https://leetcode.cn/problems/friend-requests-ii-who-has-the-most-friends/)




## 逻辑判断

- 在sql中进行逻辑判断输出，我们想要的结果，可以使用`CASE WHEN THEN ELSE`

**要求：** 输出树中每个节点的类型,p_id 为 null 出书Root、中间节点为 Inner、叶子也 Leaf

```diff
+----+------+
| id | p_id |
+----+------+
| 1  | null |
| 2  | 1    |
| 3  | 1    |
| 4  | 2    |
| 5  | 2    |
+----+------+
```

>***Tip***
>
>```mysql
>
>SELECT
>    id,
>    (
>        CASE
>            WHEN p_id IS NULL THEN 'Root'
>            WHEN id NOT IN(
>                SELECT
>                    p_id
>                FROM tree
>                WHERE p_id IS NOT NULL
>            ) THEN 'Leaf'
>            ELSE 'Inner'
>        END
>    )as type
>FROM tree
>```
>
>摘自：[608. 树节点](https://leetcode.cn/problems/tree-node/)









## 条件判断

- 当存在只有两个分支的判断时，可以使用If函数来进行逻辑判断

**要求：** id是否为奇数还是偶数，以及判断表的长度是否等于奇数

```diff
+----+---------+
| id | student |
+----+---------+
| 1  | Abbot   |
| 2  | Doris   |
| 3  | Emerson |
| 4  | Green   |
| 5  | Jeames  |
+----+---------+
```

MySQL的IF函数是一种条件函数，它允许根据指定的条件在两个或多个不同的值之间进行选择。它的语法如下：

```mysql
IF(condition, value_if_true, value_if_false)

```

它可以放在`select`语句后，也可以方位`where`语句后等等，只要是想表达输出一个值的地方，它可以被使用


>***Tip***
>
>```mysql
>
>SELECT id-1 AS id, student
>FROM Seat
>WHERE (id % 2) = 0
>
>UNION ALL
>
>SELECT IF(id = (SELECT MAX(id) FROM Seat), id, id+1), student
>FROM Seat
>WHERE (id % 2) != 0
>ORDER BY id;
>```
>
>摘自：[626. 换座位](https://leetcode.cn/problems/exchange-seats/)





- 在`Mysql`遇到判断两种情况，我们可以使用IF(value,true vale, false value) 语句

**要求：** 判断是否是三角形

```diff
+----+----+----+
| 13 | 15 | 30 |
| 10 | 20 | 15 |
+----+----+----+
```

>***Tip***
>
>```
>SELECT *,
>       IF(((x + y) > z) && ((x + z) > y) && ((y + z) > x), 'Yes', 'No') AS triangle
>FROM triangle;
>```
>
>摘自：[610. 判断三角形](https://leetcode.cn/problems/triangle-judgement/)
>


## 元组一致比较

- 这个方法比较少见（可能是我写的Sql写太少了吧）,`(字段，字段) in ( 字段、字段)` 这是一个条件，它检查主查询中的元组是否在子查询的结果集中。如果是，则返回相应的行。

**要求：** 根据产品id 和 时间 来查询具体的数据

```diff
Sales 表：
+---------+------------+------+----------+-------+
| sale_id | product_id | year | quantity | price |
+---------+------------+------+----------+-------+
| 1       | 100        | 2008 | 10       | 5000  |
| 2       | 100        | 2009 | 12       | 5000  |
| 7       | 200        | 2011 | 15       | 9000  |
+---------+------------+------+----------+-------+
```

>***Tip***
>```mysql
>select
>	product_id,
>	year as first_year,
>	quantity,
>	price
>from sales where (product_id, year) in (
>	select product_id, min(year) from sales group by product_id
>);
>```
>
>摘自：[产品销售分析 III](https://leetcode.cn/problems/product-sales-analysis-iii/)





## 区间选择

- 我们可以通过使用`Group by + Min + Max`函数进行区间选择。

**要求：** 根据产品id 和 时间 来查询具体的数据，并且当且仅当产品的时间在一定的区间内。

```diff
Product table:
+------------+--------------+------------+
| product_id | product_name | unit_price |
+------------+--------------+------------+
| 1          | S8           | 1000       |
| 2          | G4           | 800        |
| 3          | iPhone       | 1400       |
+------------+--------------+------------+
Sales table:
+-----------+------------+----------+------------+----------+-------+
| seller_id | product_id | buyer_id | sale_date  | quantity | price |
+-----------+------------+----------+------------+----------+-------+
| 1         | 1          | 1        | 2019-01-21 | 2        | 2000  |
| 1         | 2          | 2        | 2019-02-17 | 1        | 800   |
| 2         | 2          | 3        | 2019-06-02 | 1        | 800   |
| 3         | 3          | 4        | 2019-05-13 | 2        | 2800  |
+-----------+------------+----------+------------+----------+-------+
输出：
+-------------+--------------+
| product_id  | product_name |
+-------------+--------------+
| 1           | S8           |
+-------------+--------------+

```

>***Tip***
>```sql
>SELECT s.product_id , p.product_name
>FROM Sales AS s
>LEFT JOIN Product AS p
>ON s.product_id = p.product_id
>GROUP BY s.product_id
>HAVING MIN(sale_date) >= '2019-01-01' AND MAX(sale_date) <= '2019-03-31'
>
>```
>
>通过对产品id进行分组。判断分组中的产品时间最小的值与最大的值是否在区间内。满足要求则输出。
>
>普通的想法想法就是`not in`思路，来解决：
>
>```sql
>SELECT
>	DISTINCT p.product_id,
>	p.product_name
>FROM
>	Product p
>	LEFT JOIN Sales s ON p.product_id = s.product_id
>WHERE
>	s.sale_date BETWEEN '2019-01-01'
>	AND '2019-03-31'
>	AND p.product_id NOT IN (
>	SELECT
>		s.product_id
>	FROM
>		 Sales s
>	WHERE
>		s.sale_date NOT BETWEEN '2019-01-01'
>	AND '2019-03-31'
>	)
>```
>
>摘自：[1084. 销售分析III](https://leetcode.cn/problems/sales-analysis-iii/)





## 分组条件筛选的选择

- 对于分组筛选，通常我们有两个选择，
  - 先过滤数据集使用`where`语句先对数据过滤，在分组
  - 先分组，在使用`having`对分组进行过滤


**要求：** 统计截至 `2019-07-27`（包含2019-07-27），近 `30` 天的每日活跃用户数

```diff
+---------+------------+---------------+---------------+
| user_id | session_id | activity_date | activity_type |
+---------+------------+---------------+---------------+
| 1       | 1          | 2019-07-20    | open_session  |
| 1       | 1          | 2019-07-20    | scroll_down   |
| 1       | 1          | 2019-07-20    | end_session   |
| 2       | 4          | 2019-07-20    | open_session  |
| 2       | 4          | 2019-07-21    | send_message  |
| 2       | 4          | 2019-07-21    | end_session   |
| 3       | 2          | 2019-07-21    | open_session  |
| 3       | 2          | 2019-07-21    | send_message  |
| 3       | 2          | 2019-07-21    | end_session   |
| 4       | 3          | 2019-06-25    | open_session  |
| 4       | 3          | 2019-06-25    | end_session   |
+---------+------------+---------------+---------------+
```

>***Tip***
>
>先 `where` 再 `group by` 效率更高，因为先把数据集过滤掉，分组操作的数据集就少
>
>```sql
>SELECT activity_date AS day , COUNT(DISTINCT user_id) AS active_users
>FROM activity
>WHERE activity_date BETWEEN '2019-06-28' AND '2019-07-27'
>GROUP BY activity_date
>
>```
>
>先 `group by` 再 `having` 它是再分组之后进行筛选，筛选的数据集是表中的所有数据，效率低
>
>```mysql
>select  a.activity_date as day,count( DISTINCT a.user_id) as active_users   from activity a GROUP BY a.activity_date HAVING  a.activity_date BETWEEN '2019-06-28' and '2019-07-27'
>```
>
>总结：推荐先使用`where` 而不是在`group by` 中使用 `having`。如果是必要的，也可以组合使用`where`提前筛选出数据集


## 列转行

- 如何将列的数据变为列的数据呢？通过可以采用 `group by` + 特定的mysql语句来完成

**要求：** 重新格式化表格，使得 **每个月** 都有一个部门 id 列和一个收入列。

```diff
输入：
Department table:
+------+---------+-------+
| id   | revenue | month |
+------+---------+-------+
| 1    | 8000    | Jan   |
| 2    | 9000    | Jan   |
| 3    | 10000   | Feb   |
| 1    | 7000    | Feb   |
| 1    | 6000    | Mar   |
+------+---------+-------+
输出：
+------+-------------+-------------+-------------+-----+-------------+
| id   | Jan_Revenue | Feb_Revenue | Mar_Revenue | ... | Dec_Revenue |
+------+-------------+-------------+-------------+-----+-------------+
| 1    | 8000        | 7000        | 6000        | ... | null        |
| 2    | 9000        | null        | null        | ... | null        |
| 3    | null        | 10000       | null        | ... | null        |
+------+-------------+-------------+-------------+-----+-------------+
```

>***Tip***
>
>```mysql
>SELECT
>    id,
>    SUM(CASE WHEN month = 'Jan' THEN revenue END) AS Jan_Revenue,
>    SUM(CASE WHEN month = 'Feb' THEN revenue END) AS Feb_Revenue,
>    SUM(CASE WHEN month = 'Mar' THEN revenue END) AS Mar_Revenue,
>    SUM(CASE WHEN month = 'Apr' THEN revenue END) AS Apr_Revenue,
>    SUM(CASE WHEN month = 'May' THEN revenue END) AS May_Revenue,
>    SUM(CASE WHEN month = 'Jun' THEN revenue END) AS Jun_Revenue,
>    SUM(CASE WHEN month = 'Jul' THEN revenue END) AS Jul_Revenue,
>    SUM(CASE WHEN month = 'Aug' THEN revenue END) AS Aug_Revenue,
>    SUM(CASE WHEN month = 'Sep' THEN revenue END) AS Sep_Revenue,
>    SUM(CASE WHEN month = 'Oct' THEN revenue END) AS Oct_Revenue,
>    SUM(CASE WHEN month = 'Nov' THEN revenue END) AS Nov_Revenue,
>    SUM(CASE WHEN month = 'Dec' THEN revenue END) AS Dec_Revenue
>FROM Department
>GROUP BY id;
>```
>
>对于这个问题，需要注意的地方就是:
>
>解析摘自：[评论区](https://leetcode.cn/problems/reformat-department-table/solutions/343480/guan-yu-group-byyu-sumde-pei-he-by-xxiao053/)
>
> GROUP BY id 会使department表按照id分组，生成一张虚拟表
>
>```diff
>+------+---------+-------+
>| id   | revenue | month |
>+------+---------+-------+
>|      | 8000    | Jan   |
>| 1    | 7000    | Feb   |
>|      | 6000    | Mar   |
>+------+---------+-------+
>| 2    | 9000    | Jan   |
>+------+---------+-------+
>| 3    | 10000   | Feb   |
>+------+---------+-------+
>```
>
>接着如果我们通过`CASE WHEN month = 'Nov' THEN revenue END`语句来处理就会出现下面这种情况
>
>```diff
>+------+---------+-------+---------------+------------------------------------------+
>| id   | revenue | month | `month`='Feb' | case when `month`='Feb' then revenue end |
>+------+---------+-------+---------------+------------------------------------------+
>|      | 8000    | Jan   |       0       |                  null                    |
>| 1    | 7000    | Feb   |       1       |                  7000                    |
>|      | 6000    | Mar   |       0       |                  null                    |
>+------+---------+-------+---------------+------------------------------------------+
>| 2    | 9000    | Jan   |       0       |                  null                    |
>+------+---------+-------+---------------+------------------------------------------+
>| 3    | 10000   | Feb   |       1       |                  15000                   |
>+------+---------+-------+---------------+------------------------------------------+
>```
>
>如果，我们没有使用`sum`或者`max`来进行处理，它可能会随机返回 null 、7000、 null中的数据。因此，我们可以通过聚合函数
>
>`sum` `max`等等来进行处理。
>
>因为，7000+null+null = 7000,就是说每一个条件只会有一个唯一的值
>
>摘自：[1179. 重新格式化部门表](https://leetcode.cn/problems/reformat-department-table/)




## 组合分组+日期格式

- 分组`group by`后面不只可以一个字段，可以放多个字段
- `DATE_FORMAT(trans_date, '%Y-%m')`日期格式化函数

**要求：** 查找每个月和每个国家/地区的事务数及其总金额、已批准的事务数及其总金额

```diff
输入：
Transactions table:
+------+---------+----------+--------+------------+
| id   | country | state    | amount | trans_date |
+------+---------+----------+--------+------------+
| 121  | US      | approved | 1000   | 2018-12-18 |
| 122  | US      | declined | 2000   | 2018-12-19 |
| 123  | US      | approved | 2000   | 2019-01-01 |
| 124  | DE      | approved | 2000   | 2019-01-07 |
+------+---------+----------+--------+------------+
输出：
+----------+---------+-------------+----------------+--------------------+-----------------------+
| month    | country | trans_count | approved_count | trans_total_amount | approved_total_amount |
+----------+---------+-------------+----------------+--------------------+-----------------------+
| 2018-12  | US      | 2           | 1              | 3000               | 1000                  |
| 2019-01  | US      | 1           | 1              | 2000               | 2000                  |
| 2019-01  | DE      | 1           | 1              | 2000               | 2000                  |
+----------+---------+-------------+----------------+--------------------+-----------------------+
```

>***Tip***
>
>```mysql
>SELECT DATE_FORMAT(trans_date, '%Y-%m') AS month,
>    country,
>    COUNT(*) AS trans_count,
>    COUNT(IF(state = 'approved', 1, NULL)) AS approved_count,
>    SUM(amount) AS trans_total_amount,
>    SUM(IF(state = 'approved', amount, 0)) AS approved_total_amount
>FROM transactions
>GROUP BY month, country
>```
>
>这里需要注意分组的时候，时间是有日期的。如果是表原有的数据进行分组。那么分组出来的结果也是错误的。需要进行转换后在进行分组
>
>摘自：[1193. 每月交易 I](https://leetcode.cn/problems/monthly-transactions-i/)
>


## 分组计算
- 我可以通过分组，然后配合计算函数与if函数筛选出我们想要的数据

**要求：** 在 MySQL 内做简单的计算操作，比如求平均值，求和等

```diff
输入：
Queries table:
+------------+-------------------+----------+--------+
| query_name | result            | position | rating |
+------------+-------------------+----------+--------+
| Dog        | Golden Retriever  | 1        | 5      |
| Dog        | German Shepherd   | 2        | 5      |
| Dog        | Mule              | 200      | 1      |
| Cat        | Shirazi           | 5        | 2      |
| Cat        | Siamese           | 3        | 3      |
| Cat        | Sphynx            | 7        | 4      |
+------------+-------------------+----------+--------+
输出：
+------------+---------+-----------------------+
| query_name | quality | poor_query_percentage |
+------------+---------+-----------------------+
| Dog        | 2.50    | 33.33                 |
| Cat        | 0.66    | 33.33                 |
+------------+---------+-----------------------+
解释：
Dog 查询结果的质量为 ((5 / 1) + (5 / 2) + (1 / 200)) / 3 = 2.50
Dog 查询结果的劣质查询百分比为 (1 / 3) * 100 = 33.33

Cat 查询结果的质量为 ((2 / 5) + (3 / 3) + (4 / 7)) / 3 = 0.66
Cat 查询结果的劣质查询百分比为 (1 / 3) * 100 = 33.33

```

>***Tip***
>
>```mysql
>select s.query_name ,  round( sum( s.rating  / s.position ) / count(*),2)  as quality,
>round( sum(if( s.rating <3, 1,0) ) /count(*) *100,2) as poor_query_percentage
>from Queries s
>group by  s.query_name
>```
>这里要注意一分组之后，在mysql它会将数据放在同一列中
>
>摘自：[1193. 每月交易 I](https://leetcode.cn/problems/monthly-transactions-i/)
>[1211. 查询结果的质量和占比](https://leetcode.cn/problems/queries-quality-and-percentage/)





## Sum 开窗函数

- `sum() over() `通过使用sum可以对数据库列表中的数据进行依次累加

**要求：** 寻找最后一个能进入电梯的人

```diff
Queue 表
+-----------+-------------+--------+------+
| person_id | person_name | weight | turn |
+-----------+-------------+--------+------+
| 5         | Alice       | 250    | 1    |
| 4         | Bob         | 175    | 5    |
| 3         | Alex        | 350    | 2    |
| 6         | John Cena   | 400    | 3    |
| 1         | Winston     | 500    | 6    |
| 2         | Marie       | 200    | 4    |
+-----------+-------------+--------+------+
+------+----+-----------+--------+--------------+
| Turn | ID | Name      | Weight | Total Weight |
+------+----+-----------+--------+--------------+
| 1    | 5  | Alice     | 250    | 250          |
| 2    | 3  | Alex      | 350    | 600          |
| 3    | 6  | John Cena | 400    | 1000         | (最后一个上巴士)
| 4    | 2  | Marie     | 200    | 1200         | (无法上巴士)
| 5    | 4  | Bob       | 175    | ___          |
| 6    | 1  | Winston   | 500    | ___          |
+------+----+-----------+--------+--------------+
```

>***Tip***
>
>我们其实需要依次对weight的值进行累加，然后在寻找出满足要求的人，如果我可以实现使用sql绘制出下面`cumu_weight`的table就很好找了。如何实现呢？
>
>```
>
>+----+-----------+------+-----------+
>|turn|person_name|weight|cumu_weight|
>+----+-----------+------+-----------+
>|1   |Alice      |250   |250        |
>|2   |Alex       |350   |600        |
>|3   |John Cena  |400   |1000       |
>|4   |Marie      |200   |1200       |
>|5   |Bob        |175   |1375       |
>|6   |Winston    |500   |1875       |
>+----+-----------+------+-----------+
>```
>
>我们可以通过 sum 的开窗函数来实现上面的 table 查询
>
>```mysql
>select
>    turn,
>    person_name,weight,
>    sum(weight) over(order by turn) as cumu_weight
>from Queue
>```
>
>最后通过筛选出我们需要的值
>
>```mysql
>select
>    person_name
>from
>(select
>    turn,
>    person_name,weight,
>    sum(weight) over(order by turn) as cumu_weight
>from Queue) t
>where cumu_weight <= 1000
>order by turn desc
>limit 1;
>```
>
>摘自：[1204. 最后一个能进入巴士的人](https://leetcode.cn/problems/last-person-to-fit-in-the-bus/)




## 连接中条件查询的位置

- 通常，我们查询条件是写在`where`语句后的。当时，再做连接查询的时候，也可以将查询语句放在 `join` 语句之后。

**要求:** 查找每种产品的平均售价

```diff
Prices table:
+------------+------------+------------+--------+
| product_id | start_date | end_date   | price  |
+------------+------------+------------+--------+
| 1          | 2019-02-17 | 2019-02-28 | 5      |
| 1          | 2019-03-01 | 2019-03-22 | 20     |
| 2          | 2019-02-01 | 2019-02-20 | 15     |
| 2          | 2019-02-21 | 2019-03-31 | 30     |
| 3          | 2019-02-21 | 2019-03-31 | 30     |
+------------+------------+------------+--------+
UnitsSold table:
+------------+---------------+-------+
| product_id | purchase_date | units |
+------------+---------------+-------+
| 1          | 2019-02-25    | 100   |
| 1          | 2019-03-01    | 15    |
| 2          | 2019-02-10    | 200   |
| 2          | 2019-03-22    | 30    |
+------------+---------------+-------+
+------------+---------------+
| product_id | average_price |
+------------+---------------+
| 1          | 6.96          |
| 2          | 16.96         |
| 3          | 0             |
+------------+---------------+
```

>***Tip***
>
>这道题就是简单的连接问题，有点难度在于空值获取，我们需要搞清楚条件是放在join这边还是where之后，先看查询sql
>
>```mysql
>SELECT
>	p.product_id,
>IF
>	(
>		sum( us.units ),
>		ROUND( sum( p.price * us.units ) / sum( us.units ), 2 ),
>		0 
>	) AS average_price 
>FROM
>	Prices p
>	LEFT JOIN UnitsSold us ON p.product_id = us.product_id 
>	AND us.purchase_date BETWEEN p.start_date 
>	AND p.end_date 
>GROUP BY
>	p.product_id
>```
>
>如果，我们将查询时间的条件放在where则查询不出null值。下面给出情况分析：
>
>- 这个条件在 `JOIN` 子句中起作用，所以所有的行都被联接起来，即使在 `UnitsSold` 表中没有匹配的销售日期也会保留
>
>- 将这个条件移动到 `WHERE` 子句中，那么它将在 `JOIN` 之后应用，这将导致过滤掉在日期范围外的行
>
>**放在`JOIN ON`子句：**
>
>- 查询条件直接应用于连接操作，这意味着只有满足连接条件的行才会被合并到结果集中。
>- 连接条件通常在执行连接操作之前筛选掉不匹配的行，从而减少了连接的工作量，提高了性能。
>- 适用于内连接（INNER JOIN）和外连接（LEFT JOIN、RIGHT JOIN、FULL JOIN）。
>
>**放在`WHERE`子句：**
>
>- 查询条件在连接之后应用，即首先将两个表连接起来，然后再筛选结果集中的行。
>- 这可能导致执行连接操作时涉及更多的行，而后筛选出不符合条件的行，可能会导致性能下降。
>- 适用于一些特殊情况，例如需要使用非等值连接条件时，或者需要根据多个连接条件进行筛选。
>
>



## 保留Null值

**要求：** 查询出每个学生参加每一门科目测试的次数，结果按 `student_id` 和 `subject_name` 排序。

```diff
Students table:
+------------+--------------+
| student_id | student_name |
+------------+--------------+
| 1          | Alice        |
| 2          | Bob          |
| 13         | John         |
| 6          | Alex         |
+------------+--------------+
Subjects table:
+--------------+
| subject_name |
+--------------+
| Math         |
| Physics      |
| Programming  |
+--------------+
Examinations table:
+------------+--------------+
| student_id | subject_name |
+------------+--------------+
| 1          | Math         |
| 1          | Physics      |
| 1          | Programming  |
| 2          | Programming  |
| 1          | Physics      |
| 1          | Math         |
| 13         | Math         |
| 13         | Programming  |
| 13         | Physics      |
| 2          | Math         |
| 1          | Math         |
+------------+--------------+
输出：
+------------+--------------+--------------+----------------+
| student_id | student_name | subject_name | attended_exams |
+------------+--------------+--------------+----------------+
| 1          | Alice        | Math         | 3              |
| 1          | Alice        | Physics      | 2              |
| 1          | Alice        | Programming  | 1              |
| 2          | Bob          | Math         | 1              |
| 2          | Bob          | Physics      | 0              |
| 2          | Bob          | Programming  | 1              |
| 6          | Alex         | Math         | 0              |
| 6          | Alex         | Physics      | 0              |
| 6          | Alex         | Programming  | 0              |
| 13         | John         | Math         | 1              |
| 13         | John         | Physics      | 1              |
| 13         | John         | Programming  | 1              |
+------------+--------------+--------------+----------------+
```



### 左连接：

- 通过我们再做多表查询的是时候会使用到`left join`函数，如果我们要连接的结果集中有满足要求的数则会出现`null`

>***Tip***
>
>```mysql
>SELECT
>    s.student_id,
>    s.student_name,
>    su.subject_name,
>   COUNT(e.subject_name) AS attended_exams
>FROM
>    Students AS s
>JOIN
>    Subjects AS su
>LEFT JOIN
>    Examinations AS e
>ON
>    e.student_id = s.student_id
>AND
>    e.subject_name = su.subject_name
>GROUP BY
>    s.student_id,
>    su.subject_name
>ORDER BY
>    s.student_id,
>    su.subject_name
>```
>
>我们要知道`left join`的特点，才能查询出我们想要的答案。
>
>- LEFT JOIN的时候，会是以左表为主表，右表是连接表。左表的数据是完整的，如果匹配不到右表的数据会自动为NULL
>- COUNT函数是不统计NULL的。但是函数会返回一个数值 0
>
>如果，我们不用`left join`想要查询出`null`值为 0 会比较麻烦
>
>摘自：[1280. 学生们参加各科测试的次数](https://leetcode.cn/problems/students-and-examinations/)

### 交叉连接：

- 交叉连接（Cross Join）是 MySQL 中的一种连接操作，也被称为笛卡尔积（Cartesian Product）。在交叉连接中，它会将两个表中的每一行与另一个表中的每一行进行组合，生成一个新的结果集。这意味着没有任何条件限制，两个表之间的所有可能的组合都会出现在结果中

>***Tip***
>
>在实际应用中，通常要避免使用交叉连接，因为它会生成大量的行数，可能导致性能问题。但在某些情况下，交叉连接也可能有其用途，例如当你需要生成某种组合或排列的情况。
>
>先查询出所有的考试情况
>
>```mysql
>SELECT 
>    student_id, subject_name, COUNT(*) AS attended_exams
>FROM 
>    Examinations
>GROUP BY 
>    student_id, subject_name
>
>```
>
>再使用交叉连接查询出所有组合
>
>```mysql
>SELECT 
>    *
>FROM
>    Students s
>CROSS JOIN
>    Subjects sub
>```
>
>最后，进行组合
>
>```mysql
>SELECT 
>    s.student_id, s.student_name, sub.subject_name, IFNULL(grouped.attended_exams, 0) AS attended_exams
>FROM 
>    Students s
>CROSS JOIN 
>    Subjects sub
>LEFT JOIN (
>    SELECT student_id, subject_name, COUNT(*) AS attended_exams
>    FROM Examinations
>    GROUP BY student_id, subject_name
>) grouped 
>ON s.student_id = grouped.student_id AND sub.subject_name = grouped.subject_name
>ORDER BY s.student_id, sub.subject_name;
>
>```
>
>摘自：[1280. 学生们参加各科测试的次数](https://leetcode.cn/problems/students-and-examinations/)






