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



### sum over 窗口函数：

**要求：** 报告每组玩家和日期，以及玩家**到目前为止**玩了多少游戏。 也就是说，在此日期之前玩家所玩的游戏总数。（**在分组的基础上面，将前一个之累加到后一个值上面**）

```diff
Activity：
+--------------+---------+
| Column Name  | Type    |
+--------------+---------+
| player_id    | int     |
| device_id    | int     |
| event_date   | date    |
| games_played | int     |
+--------------+---------+
（player_id，event_date）是此表的主键。
这张表显示了某些游戏的玩家的活动情况。
每一行是一个玩家的记录，
他在某一天使用某个设备注销之前登录并玩了很多游戏（可能是 0 ）。
Activity table:
+-----------+-----------+------------+--------------+
| player_id | device_id | event_date | games_played |
+-----------+-----------+------------+--------------+
| 1         | 2         | 2016-03-01 | 5            |
| 1         | 2         | 2016-05-02 | 6            |
| 1         | 3         | 2017-06-25 | 1            |
| 3         | 1         | 2016-03-02 | 0            |
| 3         | 4         | 2018-07-03 | 5            |
+-----------+-----------+------------+--------------+

Result table:
+-----------+------------+---------------------+
| player_id | event_date | games_played_so_far |
+-----------+------------+---------------------+
| 1         | 2016-03-01 | 5                   |
| 1         | 2016-05-02 | 11                  |
| 1         | 2017-06-25 | 12                  |
| 3         | 2016-03-02 | 0                   |
| 3         | 2018-07-03 | 5                   |
+-----------+------------+---------------------+
对于 ID 为 1 的玩家，2016-05-02 共玩了 5+6=11 个游戏，
								2017-06-25 共玩了 5+6+1=12 个游戏。
对于 ID 为 3 的玩家，2018-07-03 共玩了 0+5=5 个游戏。
请注意，对于每个玩家，我们只关心玩家的登录日期。
```

>***Tip***
>
>```mysql
>SELECT
>	player_id,
>	event_date,
>	SUM( games_played ) over ( PARTITION BY player_id ORDER BY player_id, event_date ) AS games_played_log 
>FROM
>	activity
>```
>
>代码解析：
>
>```mysql
>SELECT
>	player_id,
>	event_date,
>	SUM( games_played ) over (  ORDER BY player_id, event_date ) AS games_played_log 
>FROM
>	activity
>```
>
>我们使用sum 窗口对games_played 进行累加。前提是先根据player_id, event_date分组。结果是：
>
>```diff
>| player_id | event_date | games_played_log |
>|-----------|------------|-------------------|
>| 1         | 2016-03-01 | 5                 |
>| 1         | 2016-05-02 | 11                |
>| 1         | 2017-06-25 | 12                |
>| 3         | 2016-03-02 | 12                |
>| 3         | 2018-07-03 | 17                |
>```
>
>会发现它是将前一个值累加到后一个值。导致player_id为3本应该为0。但是，由于它并没有实现真的分组效果，导致player_id 3把player_id 为1的也累加了。使用`PARTITION BY`在窗口上进行分组
>
>```diff
>SELECT
>	player_id,
>	event_date,
>	SUM( games_played ) over ( PARTITION BY player_id ORDER BY player_id, event_date ) AS games_played_log 
>FROM
>	activity
>```
>
>最后结果：
>
>```diff
>| player_id | event_date | games_played_log |
>|-----------|------------|-------------------|
>| 1         | 2016-03-01 | 5                 |
>| 1         | 2016-05-02 | 11                |
>| 1         | 2017-06-25 | 12                |
>| 3         | 2016-03-02 | 0                 |
>| 3         | 2018-07-03 | 5                 |
>```
>
>其他做法：
>
>```mysql
>select a1.player_id, a1.event_date, 
>    sum(a2.games_played) games_played_so_far
>from Activity a1, Activity a2
>where a1.player_id = a2.player_id
>    and a1.event_date >= a2.event_date
>group by a1.player_id, a1.event_date
>```
>
>**摘自：** [leetcode plus题](https://cloud.tencent.com/developer/article/1787826)



### Sum Over 窗口中的排序，以及指定累加行数

**要求：** 对分组排序后的结果，分组累加。但是要求只累加前当个行以及后两个行数。

Employee 表保存了一年内的薪水信息。

请你编写 SQL 语句，来查询一个员工三个月内的累计薪水，但是不包括最近一个月的薪水。

结果请按 'Id' 升序，然后按 'Month' 降序显示。

```diff
示例：
输入：

| Id | Month | Salary |
|----|-------|--------|
| 1 | 1 | 20 |
| 2 | 1 | 20 |
| 1 | 2 | 30 |
| 2 | 2 | 30 |
| 3 | 2 | 40 |
| 1 | 3 | 40 |
| 3 | 3 | 60 |
| 1 | 4 | 60 |
| 3 | 4 | 70 |
输出：

| Id | Month | Salary |
|----|-------|--------|
| 1 | 3 | 90 |
| 1 | 2 | 50 |
| 1 | 1 | 20 |
| 2 | 1 | 20 |
| 3 | 3 | 100 |
| 3 | 2 | 40 |
```

>***Tip：***
>
>这道题需要我们直到一个窗口函数的知识点，如何只要累加当前行数的以及当前行数的后两个。
>
>```sql
>select id, month, sum(salary) over(partition by id order by Month  rows 2 preceding) as co from employee
>where (id,month) not in (select id, max(month) as month from employee group by id)
>ORDER BY id, month desc;
>```
>
>代码解析：
>
>```mysql
>select id, month, sum(salary) over(partition by id order by Month  ) as co from employee
>where (id,month) not in (select id, max(month) as month from employee group by id)
>```
>
>```
>| id | month | co |
>| 1 | 1 | 20 |
>| 1 | 2 | 50 |
>| 1 | 3 | 90 |
>| 2 | 1 | 20 |
>| 3 | 2 | 40 |
>| 3 | 3 | 100 |
>输出这样的结果。如果不添加'rows 2 preceding',它并会将每一个行累加前2个人。而且将之前都进行累加。你可以试试在表中添加'1''5''100'这样的值，运行就可以感受到效果了
>```
>
>最后，我们来说一下排序。
>
>```mysql
>select id, month, sum(salary) over(partition by id order by Month rows 2 preceding) as co from employee
>where (id,month) not in (select id, max(month) as month from employee group by id)
>ORDER BY id, month desc;
>```
>
>我们也可以在排序完的结果之后在进行排序。对id，month进行排序。这样就可以得到我们想要的结果了
>
>摘自：[579.(Hard)查询员工的累计薪水](https://zqt0.gitbook.io/leetcode/sql/579.find-cumulative-salary-of-an-employee)




### 分组中位数求法

**要求：** 对数据进行查询中位数，如果不涉及多个我们很容易想到中位数求法.

排序-> 偶数 取中间值 和中间值index-1的数

   -> 奇数 取中间值

那么在sql中就有一下解放：

**方法1: 使用 `ORDER BY` 和 `LIMIT`（适用于小数据集）**

```
-- 假设表名为 your_table，列名为 your_column
SELECT your_column
FROM your_table
ORDER BY your_column
LIMIT 1
OFFSET (SELECT COUNT(*) FROM your_table) / 2;
```

这个查询首先按照你要计算中位数的列进行升序排序，然后使用 `LIMIT` 和 `OFFSET` 选择中间的值。

**方法2: 使用 `PERCENTILE_CONT` 函数**

```
-- 假设表名为 your_table，列名为 your_column
SELECT
  PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY your_column) OVER () AS median
FROM
  your_table;
```

这个查询使用 `PERCENTILE_CONT` 函数计算中位数。`0.5` 表示中位数，你可以根据需要调整百分比值。

请注意，`PERCENTILE_CONT` 函数通常在 MySQL 8.0 版本及以上才可用。

**方法3: 使用 `LIMIT` 和 `OFFSET` 的子查询**

```
-- 假设表名为 your_table，列名为 your_column
SELECT your_column
FROM your_table
ORDER BY your_column
LIMIT 1
OFFSET (
  SELECT (COUNT(*) - 1) / 2
  FROM your_table
);
```

如果要对分组之后的内容进行获取中位数，就没有那么简单了。不过，`PERCENTILE_CONT`函数也是可用优雅的解决。

编写SQL查询来查找每个公司的薪水中位数。挑战点：你是否可以在不使用任何内置的SQL函数的情况下解决此问题。

```
+-----+------------+--------+
|Id   | Company    | Salary |
+-----+------------+--------+
|1    | A          | 2341   |
|2    | A          | 341    |
|3    | A          | 15     |
|4    | A          | 15314  |
|5    | A          | 451    |
|6    | A          | 513    |
|7    | B          | 15     |
|8    | B          | 13     |
|9    | B          | 1154   |
|10   | B          | 1345   |
|11   | B          | 1221   |
|12   | B          | 234    |
|13   | C          | 2345   |
|14   | C          | 2645   |
|15   | C          | 2645   |
|16   | C          | 2652   |
|17   | C          | 65     |
+-----+------------+--------+
+-----+------------+--------+
|Id   | Company    | Salary |
+-----+------------+--------+
|5    | A          | 451    |
|6    | A          | 513    |
|12   | B          | 234    |
|9    | B          | 1154   |
|14   | C          | 2645   |
+-----+------------+--------+
```

>***Tip***
>
>```java
>SELECT DISTINCT Id, Company, Salary
>FROM (
>    SELECT *, 
>        ROW_NUMBER() over(PARTITION by Company ORDER BY Salary) rk,
>        COUNT(1) over(PARTITION by Company) n
>    FROM Employee
>) t 
>WHERE rk in (n/2, n/2+1, n/2+0.5)
>```
>
>这个的解题思路就是采用普通的中位数解题。不同的是如何在分组中进行相关的操作。例如排序之类的。这时候就可用使用`ROW_NUMBER`窗口函数了




### 分组后排序非对结果排序

**要求：** 再分组的基础上面，对每一个分组的内容进行排序，我们可以使用开窗函数来完成

```diff
输入：
Logins 表:
+---------+---------------------+
| user_id | time_stamp          |
+---------+---------------------+
| 6       | 2020-06-30 15:06:07 |
| 6       | 2021-04-21 14:06:06 |
| 6       | 2019-03-07 00:18:15 |
| 8       | 2020-02-01 05:10:53 |
| 8       | 2020-12-30 00:46:50 |
| 2       | 2020-01-16 02:49:50 |
| 2       | 2019-08-25 07:59:08 |
| 14      | 2019-07-14 09:00:00 |
| 14      | 2021-01-06 11:59:59 |
+---------+---------------------+
输出：
+---------+---------------------+
| user_id | last_stamp          |
+---------+---------------------+
| 6       | 2020-06-30 15:06:07 |
| 8       | 2020-12-30 00:46:50 |
| 2       | 2020-01-16 02:49:50 |
+---------+---------------------+
解释：
6号用户登录了3次，但是在2020年仅有一次，所以结果集应包含此次登录。
8号用户在2020年登录了2次，一次在2月，一次在12月，所以，结果集应该包含12月的这次登录。
2号用户登录了2次，但是在2020年仅有一次，所以结果集应包含此次登录。
14号用户在2020年没有登录，所以结果集不应包含。
```

>***Tip***
>通过本题可以让我们更加熟悉开窗函数的使用：
>
>```mysql
>
>select distinct user_id,
>first_value(time_stamp)over(
>         partition by user_id order by time_stamp desc)last_stamp
>from logins where year(time_stamp)=2020
>```
>
>```mysql
>
>select distinct user_id,
>max(time_stamp)over(partition by user_id)last_stamp
>from logins where year(time_stamp)=2020
>
>```
>
>使用`first_value`获取分组排序后的第一个出现的值，或者取分组后每一个组中最大值。
>
>简单做法：
>
>```java
>select user_id, max(time_stamp) last_stamp 
>from Logins
>where time_stamp >= '2020-01-01 00:00:00' and time_stamp < '2021-01-01 00:00:00'
>group by user_id
>```
>
>摘自：[1890. 2020年最后一次登录](https://leetcode.cn/problems/the-latest-login-in-2020/)



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
>扩展：[1873. 计算特殊奖金](https://leetcode.cn/problems/calculate-special-bonus/)









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




## distanct 去重多字段

**要求：** 分组去重 = 多字段去重

```mysql
FriendRequest 表：
+-----------+------------+--------------+
| sender_id | send_to_id | request_date |
+-----------+------------+--------------+
| 1         | 2          | 2016/06/01   |
| 1         | 3          | 2016/06/01   |
| 1         | 4          | 2016/06/01   |
| 2         | 3          | 2016/06/02   |
| 3         | 4          | 2016/06/09   |
+-----------+------------+--------------+

RequestAccepted 表：
+--------------+-------------+-------------+
| requester_id | accepter_id | accept_date |
+--------------+-------------+-------------+
| 1            | 2           | 2016/06/03  |
| 1            | 3           | 2016/06/08  |
| 2            | 3           | 2016/06/08  |
| 3            | 4           | 2016/06/09  |
| 3            | 4           | 2016/06/10  |
+--------------+-------------+-------------+

Result 表：
+-------------+
| accept_rate |
+-------------+
| 0.8         |
+-------------+
总共有 5 个请求，有 4 个不同的通过请求，所以通过率是 0.80
```

>***Tip***
>
>简单看很容易想到，他想统计每一个`requester_id`中不同的`accepter_id`个数，容易出写：
>
>```mysql
>SELECT COUNT(DISTINCT accepter_id) as num FROM RequestAccepted GROUP BY requester_id
>
>SELECT COUNT(DISTINCT send_to_id ) as mu1 FROM FriendRequest GROUP BY sender_id
>```
>
>写出这个接下里就是在写一个查询，将两个查询作为子查询即可，解出答应。但是，我们也可以使用`DISTINCT`对多个字段进行去重，效果也是一样的
>
>最终实现sql：
>
>```mysql
>select 
>    round(
>        ifnull(
>            (select count(distinct requester_id, accepter_id) from RequestAccepted)/
>            (select count(distinct sender_id, send_to_id) from FriendRequest)
>        ,0)
>    ,2) accept_rate
>```
>
>**摘自：**[好友申请 I：总体通过率](https://zqt0.gitbook.io/leetcode/sql/597.friend-requests-i-overall-acceptance-rate)





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




## 行转列

- 如何将mysql行数据转为列数据，我们通常可以使用`union`联合查询的方式，将每一列的数据查询来，再进行组合

**要求：** 重构 `Products` 表，查询每个产品在不同商店的价格，使得输出的格式变为`(product_id, store, price)` 

```mysql
输入：
Products table:
+------------+--------+--------+--------+
| product_id | store1 | store2 | store3 |
+------------+--------+--------+--------+
| 0          | 95     | 100    | 105    |
| 1          | 70     | null   | 80     |
+------------+--------+--------+--------+
输出：
+------------+--------+-------+
| product_id | store  | price |
+------------+--------+-------+
| 0          | store1 | 95    |
| 0          | store2 | 100   |
| 0          | store3 | 105   |
| 1          | store1 | 70    |
| 1          | store3 | 80    |
+------------+--------+-------+
解释：
产品 0 在 store1、store2、store3 的价格分别为 95、100、105。
产品 1 在 store1、store3 的价格分别为 70、80。在 store2 无法买到。
```

>***Tip***
>
>```mysql
>SELECT product_id, 'store1' store, store1 price FROM products WHERE store1 IS NOT NULL
>UNION
>SELECT product_id, 'store2' store, store2 price FROM products WHERE store2 IS NOT NULL
>UNION
>SELECT product_id, 'store3' store, store3 price FROM products WHERE store3 IS NOT NULL;
>```
>
>通过编写三条sql语句查询每一个列的数据，再通过 union来进行组装。
>
>摘自：[1795. 每个产品在不同商店的价格](https://leetcode.cn/problems/rearrange-products-table/)




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

- 通过我们再做多表查询的是时候会使用到`left join`函数，如果我们要连接的结果集中有满足要求的数则会出现`null`。注【这里的满足要求一般需靠考虑谁驱动谁。例如表A驱动表B。此时表B的数据有相比表A少，那么left连接是就会出现`NULL`】

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








## 组内的值拼接字符串

**要求：** 在分组之后，我需要将分组之后有相同的列，进行字符串拼接。那么此时，我们可以使用`GROUP_CONCAT`函数进行编写。

`GROUP_CONCAT` 是 MySQL 中的一个聚合函数，用于将组内的值连接成一个字符串。通常，它用于在查询结果中将多行数据连接成一行，以实现数据的汇总或展示。

```diff
输入：
Activities 表：
+------------+-------------+
| sell_date  | product     |
+------------+-------------+
| 2020-05-30 | Headphone   |
| 2020-06-01 | Pencil      |
| 2020-06-02 | Mask        |
| 2020-05-30 | Basketball  |
| 2020-06-01 | Bible       |
| 2020-06-02 | Mask        |
| 2020-05-30 | T-Shirt     |
+------------+-------------+
输出：
+------------+----------+------------------------------+
| sell_date  | num_sold | products                     |
+------------+----------+------------------------------+
| 2020-05-30 | 3        | Basketball,Headphone,T-shirt |
| 2020-06-01 | 2        | Bible,Pencil                 |
| 2020-06-02 | 1        | Mask                         |
+------------+----------+------------------------------+
```

>***Tip***
>
>```mysql
>SELECT sell_date,
>       COUNT(DISTINCT product) num_sold,
>       GROUP_CONCAT(DISTINCT product ORDER BY product ASC) products
>FROM Activities
>GROUP BY sell_date
>```
>
>**基本用法**
>
>假设你有一个表 `Orders`，其中包含订单号 (`order_id`) 和产品名称 (`product_name`) 列。你想按订单号将产品名称连接成一个逗号分隔的字符串：
>
>```
>codeSELECT order_id, GROUP_CONCAT(product_name) AS products
>FROM Orders
>GROUP BY order_id;
>```
>
>这将返回一个结果集，每个订单号对应一个产品名称的逗号分隔字符串。你可以在 `GROUP_CONCAT` 后面使用 `AS` 关键字为结果列指定一个别名。
>
>**更改分隔符**
>
>默认情况下，`GROUP_CONCAT` 使用逗号作为分隔符，但你可以通过使用 `SEPARATOR` 关键字来更改分隔符：
>
>```mysql
>SELECT order_id, GROUP_CONCAT(product_name SEPARATOR ';') AS products
>FROM Orders
>GROUP BY order_id;
>```
>
>在这个示例中，我们将分隔符从逗号更改为分号。
>
>**对结果进行排序**
>
>你可以使用 `ORDER BY` 子句对结果进行排序，然后使用 `GROUP_CONCAT` 连接排序后的值：
>
>```mysql
>SELECT order_id, GROUP_CONCAT(product_name ORDER BY product_name) AS products
>FROM Orders
>GROUP BY order_id;
>```
>
>这将按字母顺序对产品名称进行排序，然后连接它们。
>
>**去重复值**
>
>如果你希望去除重复的值，可以使用 `DISTINCT` 关键字：
>
>```mysql
>SELECT order_id, GROUP_CONCAT(DISTINCT product_name) AS products
>FROM Orders
>GROUP BY order_id;
>```
>
>这将仅包括唯一的产品名称。
>
>**控制连接的最大长度**
>
>默认情况下，`GROUP_CONCAT` 没有长度限制，但你可以使用 `MAX_LENGTH` 来控制连接的最大长度。如果你的连接字符串太长，它可能会被截断。
>
>```mysql
>SELECT order_id, GROUP_CONCAT(product_name ORDER BY product_name SEPARATOR ',' MAX_LENGTH 1000) AS products
>FROM Orders
>GROUP BY order_id;
>```



## 正则表达

**要求：** 被要求匹配一个字符串，应该最先想到写一个正则表达式模式进行匹配

一个有效的电子邮件具有前缀名称和域，其中：

1.  **前缀** 名称是一个字符串，可以包含字母（大写或小写），数字，下划线 `'_'` ，点 `'.'` 和/或破折号 `'-'` 。前缀名称 **必须** 以字母开头。
2. **域** 为 `'@leetcode.com'` 。

```diff
Users 表:
+---------+-----------+-------------------------+
| user_id | name      | mail                    |
+---------+-----------+-------------------------+
| 1       | Winston   | winston@leetcode.com    |
| 2       | Jonathan  | jonathanisgreat         |
| 3       | Annabelle | bella-@leetcode.com     |
| 4       | Sally     | sally.come@leetcode.com |
| 5       | Marwan    | quarz#2020@leetcode.com |
| 6       | David     | david69@gmail.com       |
| 7       | Shapiro   | .shapo@leetcode.com     |
+---------+-----------+-------------------------+
输出：
+---------+-----------+-------------------------+
| user_id | name      | mail                    |
+---------+-----------+-------------------------+
| 1       | Winston   | winston@leetcode.com    |
| 3       | Annabelle | bella-@leetcode.com     |
| 4       | Sally     | sally.come@leetcode.com |
+---------+-----------+-------------------------+
```

>***Tip***
>
>```mysql
>SELECT user_id, name, mail
>FROM Users
>-- 请注意，还需要转义了`@`字符，因为它在某些正则表达式中具有特殊意义
>WHERE mail REGEXP '^[a-zA-Z][a-zA-Z0-9_.-]*\\@leetcode\\.com$';
>```
>
>正则表达式提供各种功能，以下是一些相关功能：
>
>- ^：表示一个字符串或行的开头
>- [a-z]：表示一个字符范围，匹配从 a 到 z 的任何字符。
>- [0-9]：表示一个字符范围，匹配从 0 到 9 的任何字符。
>- [a-zA-Z]：这个变量匹配从 a 到 z 或 A 到 Z 的任何字符。请注意，你可以在方括号内指定的字符范围的数量没有限制，您可以添加想要匹配的其他字符或范围。
>- [^a-z]：这个变量匹配不在 a 到 z 范围内的任何字符。请注意，字符 ^ 用来否定字符范围，它在方括号内的含义与它的方括号外表示开始的含义不同。
>- [a-z]*：表示一个字符范围，匹配从 a 到 z 的任何字符 0 次或多次。
>- [a-z]+：表示一个字符范围，匹配从 a 到 z 的任何字符 1 次或多次。
>- .：匹配任意一个字符。
>- \.：表示句点字符。请注意，反斜杠用于转义句点字符，因为句点字符在正则表达式中具有特殊含义。还要注意，在许多语言中，你需要转义反斜杠本身，因此需要使用\\.。
>- $：表示一个字符串或行的结尾。
>
>摘自：[1517. 查找拥有有效邮箱的用户](https://leetcode.cn/problems/find-users-with-valid-e-mails/)



## 筛选空值

**要求：** 在做多表查询时，可能出现某一些字段为`null`的值，我们可以使用 `is null`

```diff
输入:
Visits
+----------+-------------+
| visit_id | customer_id |
+----------+-------------+
| 1        | 23          |
| 2        | 9           |
| 4        | 30          |
| 5        | 54          |
| 6        | 96          |
| 7        | 54          |
| 8        | 54          |
+----------+-------------+
Transactions
+----------------+----------+--------+
| transaction_id | visit_id | amount |
+----------------+----------+--------+
| 2              | 5        | 310    |
| 3              | 5        | 300    |
| 9              | 5        | 200    |
| 12             | 1        | 910    |
| 13             | 2        | 970    |
+----------------+----------+--------+
输出:
+-------------+----------------+
| customer_id | count_no_trans |
+-------------+----------------+
| 54          | 2              |
| 30          | 1              |
| 96          | 1              |
+-------------+----------------+
```

>***Tip***
>
>```mysql
>SELECT
>	visits.customer_id,
>	count(*) AS count_no_trans 
>FROM
>	visits
>	LEFT JOIN transactions ON visits.visit_id = transactions.visit_id 
>WHERE
>	 transactions.transaction_id IS NULL 
>GROUP BY
>	visits.customer_id
>```
>
>在`where`如果直接查询 `transactions.transaction_id = null`是不合理的，因为`null`值无法直接比较
>
>摘自：[1581. 进店却未进行过交易的顾客](https://leetcode.cn/problems/customer-who-visited-but-did-not-make-any-transactions/)





## inner join > left join (Sometime)

**要求：** 找出额高于 10000 的所有用户的名字和余额. 账户的余额等于包含该账户的所有交易的总和

```diff
输入：
Users table:
+------------+--------------+
| account    | name         |
+------------+--------------+
| 900001     | Alice        |
| 900002     | Bob          |
| 900003     | Charlie      |
+------------+--------------+

Transactions table:
+------------+------------+------------+---------------+
| trans_id   | account    | amount     | transacted_on |
+------------+------------+------------+---------------+
| 1          | 900001     | 7000       |  2020-08-01   |
| 2          | 900001     | 7000       |  2020-09-01   |
| 3          | 900001     | -3000      |  2020-09-02   |
| 4          | 900002     | 1000       |  2020-09-12   |
| 5          | 900003     | 6000       |  2020-08-07   |
| 6          | 900003     | 6000       |  2020-09-07   |
| 7          | 900003     | -4000      |  2020-09-11   |
+------------+------------+------------+---------------+
输出：
+------------+------------+
| name       | balance    |
+------------+------------+
| Alice      | 11000      |
+------------+------------+
解释：
Alice 的余额为(7000 + 7000 - 3000) = 11000.
Bob 的余额为1000.
Charlie 的余额为(6000 + 6000 - 4000) = 8000.
```

>***Tip***
>
>```mysql
>SELECT
>	users.name,
>	sum( Transactions.amount ) AS balance 
>FROM
>	Transactions
>	Left  JOIN users ON users.account = Transactions.account 
>GROUP BY
>	Transactions.account 
>HAVING
>	balance > 10000
>```
>
>```mysql
>SELECT
>	users.name,
>	sum( Transactions.amount ) AS balance 
>FROM
>	Transactions
>	inner  JOIN users ON users.account = Transactions.account 
>GROUP BY
>	Transactions.account 
>HAVING
>	balance > 10000
>```
>
>两个相同的代码，inner的效率比left高上 100 ms。那为什么呢？因为：
>
>1. 数据量减少："INNER JOIN" 仅返回两个表之间匹配的行，而 "LEFT JOIN" 会返回左表的所有行，以及右表中匹配的行。因此， "LEFT JOIN" 可能需要处理更多的数据。
>2. 索引效率：如果表上有适当的索引，"INNER JOIN" 可能能够更有效地查找匹配的行，因为它不需要在不匹配的行上执行额外的操作。而 "LEFT JOIN" 需要处理所有左表的行，不论是否有匹配。
>3. 查询优化：数据库引擎通常会尝试优化查询，以提高性能。对于 "INNER JOIN"，优化器可能有更多的机会选择更有效的执行计划，因为它知道不需要处理左表中没有匹配的行。
>4. 外键关系：如果你的表之间有外键关系，数据库引擎可能能够更好地优化 "INNER JOIN"，因为它可以利用外键的约束信息。
>
>尽管 "INNER JOIN" 在某些情况下可能更高效，但 "LEFT JOIN" 也是非常有用的，因为它可以用于检索左表的所有行，即使它们没有匹配。性能差异通常不会很大。
>
>摘自：[1587. 银行账户概要 II](https://leetcode.cn/problems/bank-account-summary-ii/)





## 排序中也有排序

**要求：** 返回的结果表根据某一个字段进行 的 **降序** 排序，若相同则按 另一个字段进行的 **升序** 排序。

```diff
Users 表：
+---------+-----------+
| user_id | user_name |
+---------+-----------+
| 6       | Alice     |
| 2       | Bob       |
| 7       | Alex      |
+---------+-----------+

Register 表：
+------------+---------+
| contest_id | user_id |
+------------+---------+
| 215        | 6       |
| 209        | 2       |
| 208        | 2       |
| 210        | 6       |
| 208        | 6       |
| 209        | 7       |
| 209        | 6       |
| 215        | 7       |
| 208        | 7       |
| 210        | 2       |
| 207        | 2       |
| 210        | 7       |
+------------+---------+
输出：
+------------+------------+
| contest_id | percentage |
+------------+------------+
| 208        | 100.0      |
| 209        | 100.0      |
| 210        | 100.0      |
| 215        | 66.67      |
| 207        | 33.33      |
+------------+------------+
解释：
所有用户都注册了 208、209 和 210 赛事，因此这些赛事的注册率为 100% ，我们按 contest_id 的降序排序加入结果表中。
Alice 和 Alex 注册了 215 赛事，注册率为 ((2/3) * 100) = 66.67%
Bob 注册了 207 赛事，注册率为 ((1/3) * 100) = 33.33%
```

> ***Tip***
>
> 这是一个简单题，想说的知识点是一个排序中排序的问题。在`order by` 中也存在优先级，如果先写的排序在前，则优先级高
>
> ```mysql
> select contest_id, round((count(*) / (select count(*) from users) )*100,2 ) as percentage
> from register
> group by contest_id
> order by percentage desc, contest_id asc
> ```
>
> `percentage` 的 **降序** 排序，若相同则按 `contest_id` 的 **升序** 排序。
>
> 反之：
>
> ```
> select contest_id, round((count(*) / (select count(*) from users) )*100,2 ) as percentage
> from register
> group by contest_id
> order by  contest_id asc， percentage desc
> ```
>
> 升序，在根据 `percentage` 降序
>
> 摘自：[1633. 各赛事的用户注册率](https://leetcode.cn/problems/percentage-of-users-attended-a-contest/)



## Sqrt 和 Pow函数

**要求：** 在sql数据中，计算两点最短距离

```
写一个查询语句找到两点之间的最近距离，保留 2 位小数。

| x  | y  |
|----|----|
| -1 | -1 |
| 0  | 0  |
| -1 | -2 |
复制
最近距离在点 (-1,-1) 和(-1,2) 之间，距离为 1.00 。所以输出应该为：

| shortest |
|----------|
| 1.00     |
```

>***Tip***
>
>这道题考察的是三角形的数学问题，以及笛卡尔积问题。应该算是一道简单题，才是。
>
>```mysql
>select round(sqrt(min(pow(abs(a.x-b.x),2)+pow(abs(a.y-b.y),2))),2) shortest
>from point_2d a left join point_2d b on (a.x,a.y) != (b.x,b.y); --除自己之外的笛卡尔积
>```
>
>**摘自：** [612.(Medium)平面上的最近距离](https://zqt0.gitbook.io/leetcode/sql/612.shortest-distance-in-a-plane)






## mysql字符串函数

**要求：** 对某一列的值首字母大写

```diff
输入：
Users table:
+---------+-------+
| user_id | name  |
+---------+-------+
| 1       | aLice |
| 2       | bOB   |
+---------+-------+
输出：
+---------+-------+
| user_id | name  |
+---------+-------+
| 1       | Alice |
| 2       | Bob   |
+---------+-------+
```

>***Tip***
>
>```mysql
>select user_id, CONCAT(UPPER(left(name, 1)), LOWER(right(name, length(name) - 1))) as name
>from Users
>order by user_id
>```
>
>这道题考察mysql字符串函数相关的内容:
>
>- CONCAT() 函数：将多个字符串拼接
>- LEFT(str, length) 函数：从左开始截取字符串，length 是截取的长度。
>- UPPER(str) ：将字符串中所有字符转为大写
>- LOWER(str) ：将字符串中所有字符转为小写
>-  SUBSTRING(str, begin, end) ： 截取字符串，end 不写默认为空
>
>摘自：[1667. 修复表中的名字](https://leetcode.cn/problems/fix-names-in-a-table/)


## mysql 解题总结

### one-解题思路：

解题思路：

大部分的msyql，都可以使用拆分法来完成。

- 思路拆分
  - 遇到mysql题可以先将要求和思路进行拆分，一步一步来做。大不了就是多写几行`sql`语句
- 整合归纳
  - 完成任务之后，我们可以进行优化和思考。是否有更好的解决办法

摘自：[1393. 股票的资本损益](https://leetcode.cn/problems/capital-gainloss/)

>```
>输入：
>Stocks 表:
>+---------------+-----------+---------------+--------+
>| stock_name    | operation | operation_day | price  |
>+---------------+-----------+---------------+--------+
>| Leetcode      | Buy       | 1             | 1000   |
>| Corona Masks  | Buy       | 2             | 10     |
>| Leetcode      | Sell      | 5             | 9000   |
>| Handbags      | Buy       | 17            | 30000  |
>| Corona Masks  | Sell      | 3             | 1010   |
>| Corona Masks  | Buy       | 4             | 1000   |
>| Corona Masks  | Sell      | 5             | 500    |
>| Corona Masks  | Buy       | 6             | 1000   |
>| Handbags      | Sell      | 29            | 7000   |
>| Corona Masks  | Sell      | 10            | 10000  |
>+---------------+-----------+---------------+--------+
>输出：
>+---------------+-------------------+
>| stock_name    | capital_gain_loss |
>+---------------+-------------------+
>| Corona Masks  | 9500              |
>| Leetcode      | 8000              |
>| Handbags      | -23000            |
>+---------------+-------------------+
>```

>***Tip***
>
>从题目中可知，我们要计算 `sell` 和 `buy` 之间差值。那么，我们可以编写两条sql语句
>
>**buy**
>
>```mysql
>select stock_name,sum(price) as buy from stocks where operation = 'Buy' group by stocks.stock_name
>```
>
>**sell**
>
>```mysql
>select stock_name,sum(price) as sell from stocks where operation = 'Sell' group by stocks.stock_name
>```
>
>有了这些数据，我们计算差值就可以得出我们想要的结果：
>
>```mysql
>select s1.stock_name,(s2.sell - s1.buy) as capital_gain_loss  from
>(select stock_name,sum(price) as buy from stocks where operation = 'Buy' group by stocks.stock_name) s1,
>(select stock_name,sum(price) as sell from stocks where operation = 'Sell' group by stocks.stock_name) s2
>where s1.stock_name = s2.stock_name;
>```
>
>写到这样，就可以完成任务。但是，我们还需要考虑是否可以优化，或者是否有更好的方式。通过上面的思路，我可以进行变形。去分组判断类型即可。
>
>```mysql
>select  stock_name,(sum( if(operation='Sell' ,price,-price)) ) as capital_gain_loss
>from stocks
>group by stock_name;
>```
>
>优化完成，代码更加简洁。每一道mysql，都可以先做出来，在优化的方式。



### Two-ANSI_QUOTES模式下的''和""：

如果，不注意ANSI_QUOTES模式下`''`和`""`的区别。那么，在编写`sql`的时候就容易出现问题。例如，注意这两个的区别：

```mysql
SELECT id AS resourceId, name AS resourceName, "字符串" AS resourceType FROM info;
```

```java
SELECT id AS resourceId, name AS resourceName,'字符串' AS resourceType FROM info;

```

前者Sql会出现

```error
1054 -Unknown column '虚拟主机" in 'field list'
```

>原因在于：
>
>ANSI_QUOTES模式会将 `"` 视为识别符，如果启用 ANSI_QUOTES，只单引号内的会被认为是 String Literals，双引号被解释为识别符，因此不能用双引号来引用字符串（支持）
>
>- 在默认情况下，MySQL 不区分单引号和双引号。它们都表示字符串文字。除非启用了 `ANSI_QUOTES` 模式，否则双引号不会被解释为标识符。
>- 当引用标识符需要使用保留字或者特殊字符时，启用 `ANSI_QUOTES` 模式可能有用。但在大多数情况下，建议使用单引号表示字符串文字，而使用反引号（```）表示引用标识符。



SQL mode 列表，如下

| 名称                       | 含义                                                         |
| :------------------------- | :----------------------------------------------------------- |
| PIPES_AS_CONCAT            | 将 "\|\|" 视为字符串连接操作符（＋）(同CONCAT())，而不视为OR（支持) |
| ANSI_QUOTES                | 将 `"` 视为识别符，如果启用 ANSI_QUOTES，只单引号内的会被认为是 String Literals，双引号被解释为识别符，因此不能用双引号来引用字符串（支持） |
| IGNORE_SPACE               | 若开启该模式，系统忽略空格。例如：“user” 和 “user “ 是相同的（支持） |
| ONLY_FULL_GROUP_BY         | 如果 GROUP BY 出现的列并没有在 SELECT，HAVING，ORDER BY 中出现，此 SQL 不合法，因为不在 GROUP BY 中的列被查询展示出来不符合正常现象（支持) |
| NO_UNSIGNED_SUBTRACTION    | 在减运算中，如果某个操作数没有符号，不要将结果标记为UNSIGNED（支持） |
| NO_DIR_IN_CREATE           | 创建表时，忽视所有 INDEX DIRECTORY 和 DATA DIRECTORY 指令，该选项仅对从复制服务器有用 （仅语法支持） |
| NO_KEY_OPTIONS             | 使用 SHOW CREATE TABLE 时不会输出 MySQL 特有的语法部分，如 ENGINE，使用 mysqldump 跨 DB 种类迁移的时需要考虑此选项（仅语法支持） |
| NO_FIELD_OPTIONS           | 使用 SHOW CREATE TABLE 时不会输出 MySQL 特有的语法部分，如 ENGINE，使用 mysqldump 跨 DB 种类迁移的时需要考虑此选项（仅语法支持） |
| NO_TABLE_OPTIONS           | 使用 SHOW CREATE TABLE 时不会输出 MySQL 特有的语法部分，如 ENGINE，使用 mysqldump 跨 DB 种类迁移的时需要考虑此选项（仅语法支持） |
| NO_AUTO_VALUE_ON_ZERO      | 若启用该模式，在AUTO_INCREMENT列的处理传入的值是 0 或者具体数值时系统直接将该值写入此列，传入 NULL 时系统自动生成下一个序列号（支持） |
| NO_BACKSLASH_ESCAPES       | 若启用该模式，`\` 反斜杠符号仅代表它自己（支持）             |
| STRICT_TRANS_TABLES        | 对于事务存储引擎启用严格模式，insert非法值之后，回滚整条语句（支持） |
| STRICT_ALL_TABLES          | 对于事务型表，写入非法值之后，回滚整个事务语句（支持）       |
| NO_ZERO_IN_DATE            | 在严格模式，不接受月或日部分为0的日期。如果使用IGNORE选项，我们为类似的日期插入'0000-00-00'。在非严格模式，可以接受该日期，但会生成警告（支持） |
| NO_ZERO_DATE               | 在严格模式，不要将 '0000-00-00'做为合法日期。你仍然可以用IGNORE选项插入零日期。在非严格模式，可以接受该日期，但会生成警告（支持） |
| ALLOW_INVALID_DATES        | 不检查全部日期的合法性，仅检查月份值在 1 到 12 及日期值在 1 到 31 之间，仅适用于 DATE 和 DATATIME 列，TIMESTAMP 列需要全部检查其合法性（支持） |
| ERROR_FOR_DIVISION_BY_ZERO | 若启用该模式，在 INSERT 或 UPDATE 过程中，被除数为 0 值时，系统产生错误 若未启用该模式，被除数为 0 值时，系统产生警告，并用 NULL 代替（支持） |
| NO_AUTO_CREATE_USER        | 防止 GRANT 自动创建新用户，但指定密码除外（支持）            |
| HIGH_NOT_PRECEDENCE        | NOT 操作符的优先级是表达式。例如：NOT a BETWEEN b AND c 被解释为 NOT (a BETWEEN b AND c)。在部份旧版本 MySQL 中，表达式被解释为 (NOT a) BETWEEN b AND c（支持） |
| NO_ENGINE_SUBSTITUTION     | 如果需要的存储引擎被禁用或未编译，可以防止自动替换存储引擎（仅语法支持） |
| PAD_CHAR_TO_FULL_LENGTH    | 若启用该模式，系统对于 CHAR 类型不会截断尾部空格（仅语法支持。该模式[在 MySQL 8.0 中已废弃](https://dev.mysql.com/doc/refman/8.0/en/sql-mode.html#sqlmode_pad_char_to_full_length)。） |
| REAL_AS_FLOAT              | 将 REAL 视为 FLOAT 的同义词，而不是 DOUBLE 的同义词（支持）  |
| POSTGRESQL                 | 等同于 PIPES_AS_CONCAT、ANSI_QUOTES、IGNORE_SPACE、NO_KEY_OPTIONS、NO_TABLE_OPTIONS、NO_FIELD_OPTIONS（仅语法支持） |
| MSSQL                      | 等同于 PIPES_AS_CONCAT、ANSI_QUOTES、IGNORE_SPACE、NO_KEY_OPTIONS、NO_TABLE_OPTIONS、 NO_FIELD_OPTIONS（仅语法支持） |
| DB2                        | 等同于 PIPES_AS_CONCAT、ANSI_QUOTES、IGNORE_SPACE、NO_KEY_OPTIONS、NO_TABLE_OPTIONS、NO_FIELD_OPTIONS（仅语法支持） |
| MAXDB                      | 等同于 PIPES_AS_CONCAT、ANSI_QUOTES、IGNORE_SPACE、NO_KEY_OPTIONS、NO_TABLE_OPTIONS、NO_FIELD_OPTIONS、NO_AUTO_CREATE_USER（支持） |
| MySQL323                   | 等同于 NO_FIELD_OPTIONS、HIGH_NOT_PRECEDENCE（仅语法支持）   |
| MYSQL40                    | 等同于 NO_FIELD_OPTIONS、HIGH_NOT_PRECEDENCE（仅语法支持）   |
| ANSI                       | 等同于 REAL_AS_FLOAT、PIPES_AS_CONCAT、ANSI_QUOTES、IGNORE_SPACE（仅语法支持） |
| TRADITIONAL                | 等同于 STRICT_TRANS_TABLES、STRICT_ALL_TABLES、NO_ZERO_IN_DATE、NO_ZERO_DATE、ERROR_FOR_DIVISION_BY_ZERO、NO_AUTO_CREATE_USER（仅语法支持） |
| ORACLE                     | 等同于 PIPES_AS_CONCAT、ANSI_QUOTES、IGNORE_SPACE、NO_KEY_OPTIONS、NO_TABLE_OPTIONS、NO_FIELD_OPTIONS、NO_AUTO_CREATE_USER（仅语法支持） |




### Three-逻辑分析 ≈ 窗口函数

通过一步一步拆分逻辑，编写sql，也可以写出窗口函数的效果

给如下两个表，写一个查询语句，求出在每一个工资发放日，每个部门的平均工资与公司的平均工资的比较结果 （高 / 低 / 相同）。

```diff
表： salary
| id | employee_id | amount | pay_date   |
|----|-------------|--------|------------|
| 1  | 1           | 9000   | 2017-03-31 |
| 2  | 2           | 6000   | 2017-03-31 |
| 3  | 3           | 10000  | 2017-03-31 |
| 4  | 1           | 7000   | 2017-02-28 |
| 5  | 2           | 6000   | 2017-02-28 |
| 6  | 3           | 8000   | 2017-02-28 |

employee_id 字段是表 employee 中 employee_id 字段的外键。

| employee_id | department_id |
|-------------|---------------|
| 1           | 1             |
| 2           | 2             |
| 3           | 2             |

对于如上样例数据，结果为：

| pay_month | department_id | comparison  |
|-----------|---------------|-------------|
| 2017-03   | 1             | higher      |
| 2017-03   | 2             | lower       |
| 2017-02   | 1             | same        |
| 2017-02   | 2             | same        |

解释
在三月，公司的平均工资是 (9000+6000+10000)/3 = 8333.33…
由于部门 ‘1’ 里只有一个 employee_id 为 ‘1’ 的员工，
 所以部门 ‘1’ 的平均工资就是此人的工资 9000 。
 因为 9000 > 8333.33 ，所以比较结果是 ‘higher’。

第二个部门的平均工资为 employee_id 为 ‘2’ 和 ‘3’ 两个人的平均工资，为 (6000+10000)/2=8000 。
 因为 8000 < 8333.33 ，所以比较结果是 ‘lower’ 。

在二月用同样的公式求平均工资并比较，比较结果为 ‘same’ ，
 因为部门 ‘1’ 和部门 ‘2’ 的平均工资与公司的平均工资相同，都是 7000 。
```

>分析：
>
>我们可以先计算出公司每月金额平均值
>
>```mysql
> SELECT 
>        SUBSTR(s1.pay_date, 1, 7) AS month,
>        AVG(s1.amount) AS avg_salary
>    FROM 
>        salary AS s1
>    GROUP BY 
>        SUBSTR(s1.pay_date, 1, 7)
>```
>
>接着，我们要查询出，每月每部门的信息。
>
>```java
>SELECT 
>    SUBSTR(salary.pay_date, 1, 7) AS pay_month,
>    employee.department_id AS department_id
>FROM
>    employee
>LEFT JOIN
>    salary USING (employee_id)
>
>GROUP BY 
>    department_id, SUBSTR(salary.pay_date, 1, 7);
>
>```
>
>有了这些信息开始组合sql，使用`case when`条件判断来输出higher、lower状态
>
>```mysql
>SELECT 
>    SUBSTR(salary.pay_date, 1, 7) AS pay_month,
>    employee.department_id AS department_id,
>    (
>        CASE
>            WHEN avg(salary.amount) > avg_amount.avg_salary THEN 'higher'
>            WHEN avg(salary.amount) < avg_amount.avg_salary THEN 'lower'
>            ELSE 'same'
>        END
>    ) AS comparison
>FROM
>    employee
>LEFT JOIN
>    salary USING (employee_id)
>JOIN (
>    SELECT 
>        SUBSTR(s1.pay_date, 1, 7) AS month,
>        AVG(s1.amount) AS avg_salary
>    FROM 
>        salary AS s1
>    GROUP BY 
>        SUBSTR(s1.pay_date, 1, 7)
>) AS avg_amount ON SUBSTR(salary.pay_date, 1, 7) = avg_amount.month
>GROUP BY 
>    department_id, SUBSTR(salary.pay_date, 1, 7);
>
>```
>
>当然，还有一个更好的解法：
>
>```mysql
>
>SELECT DISTINCT
>	pay_month,
>	department_id,
>CASE
>		
>		WHEN dep_avg > total_avg THEN
>		'higher' 
>		WHEN dep_avg = total_avg THEN
>		'same' ELSE 'lower' 
>	END AS comparison 
>FROM
>	(# 平均部门工资, 公司平均工资
>	SELECT
>		department_id,
>		avg( amount ) over (
>			PARTITION BY department_id,
>		date_format( pay_date, "%Y-%m" )) dep_avg,
>		date_format( pay_date, "%Y-%m" ) pay_month,
>		avg( amount ) over (
>		PARTITION BY date_format( pay_date, "%Y-%m" )) total_avg 
>	FROM
>		salary s
>	LEFT JOIN employee USING ( employee_id ) 
>	) t
>```
>
>其实，会发现窗口函数的出来的逻辑和连接表查询时差不多的。只不过写法简单。
>
>摘自：[615. 平均工资：部门与公司比较](https://cloud.tencent.com/developer/article/1787908)







