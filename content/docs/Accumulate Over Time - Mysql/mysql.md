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

**要求：2015 年的投保额 (tiv_2015) 至少跟一个其他投保人在 2015 年的投保额相同**

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





## 查询多个连续的数据

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




## 联合数据

- 为了人我们可以将表中不同的列放置在同一个列中，我们可以使用 `UNION` 和 `UNION ALL` 字段来完成。

**要求：如何将 requester_id 字段和 accepter_id 字段整合为一组可以重复的表 **

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
















