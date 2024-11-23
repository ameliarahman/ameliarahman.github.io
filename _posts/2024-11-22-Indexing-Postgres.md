---
layout: post
title: "Fundamental of Database - Part 3 : Single Index and Composite Index for Better Performance"
description: "Description of some indexing in Postgres"
date: 2024-10-21 15:23:18
comments: true
keywords: "Database, Postgres, Query, Explain, Indexing"
category: [Tech]
tags:
- indexing, database, query planner, explain, sequential scan, bitmap scan, index scan
---

One effective way to improve query performance is by using indexing. After discussing the query planner, including the explanation about scan type used by the database <a href="https://ameliarahman.github.io/2024-10/Explain-Postgres" target="_top"> in the previous article </a>, this section outlines various methods for creating indexes to enhance performance.

Let’s dive straight into the practice.
Create a table with 3 columns and seed them with million of data as usual:
<script src="https://gist.github.com/ameliarahman/ccf0d74fc24ab2764783ec03750a5ca5.js"></script>

Then run these queries below with `EXPLAIN ANALYZE` before query:

```sql
-- Query 1
EXPLAIN ANALYZE SELECT number_c from random_numbers where number_a = 1;

-- Query 2
EXPLAIN ANALYZE SELECT number_c from random_numbers where number_b = 1;

-- Query 3
EXPLAIN ANALYZE SELECT number_c from random_numbers where number_b > 6000000;
```

The database decides to use Sequential Scan for all queries:
![](../assets/img/indexing/single_index_result_0.png)

Now, let's try to create various index for the column and see the difference:

## Single Indexing
A single index is the index that is created on one specific column of a table.
For example, if we want to create an index for each `number_a` and `number_b`, we typically can do:
<script src="https://gist.github.com/ameliarahman/2dccde3545b12b740c15589f0a2578c8.js"></script>

And if we check to the table, now we already have b-tree index on each `number_a` column and `number_b` column:
![](../assets/img/indexing/single_index_result.png)

If we re-run again the queries above, now he database's query planner utilizes the created index during execution:
![](../assets/img/indexing/single_index_result_2.png)

However, as I ever mentioned in <a href="https://ameliarahman.github.io/2024-10/Explain-Postgres" target="_top"> this article </a>, it’s also important to always remember that even if an index is created on a column, the database may not use it if the number of rows returned is too large:

```sql
-- Query 1
EXPLAIN ANALYZE SELECT number_c from random_numbers where number_a > 100;

-- Query 2
EXPLAIN ANALYZE SELECT number_c from random_numbers where number_b > 100;
```

![](../assets/img/indexing/single_index_result_3.png)

Both queries returned more than 9 millions of data. So, using sequential scan is way more efficient than using the index to find the data that matches criteria in that case.

## Composite Indexing
If single index is used for a specific field, the composite index is an index that concatinates two or more columns from a table. It’s especially useful when a query involves conditions on two or more columns. However, __`the order of the column matters`__ on the composite index.

Now, let's drop those indexes and create a composite index on column_a and column_b:
<script src="https://gist.github.com/ameliarahman/b2ead7827af6b82fcc66f49701b7ccb7.js"></script>

The run these queries:

```sql
-- Query 1
EXPLAIN ANALYZE SELECT number_c from random_numbers where number_a = 2;

-- Query 2
EXPLAIN ANALYZE SELECT number_c from random_numbers where number_b = 2;

-- Query 3
EXPLAIN ANALYZE SELECT number_c from random_numbers where number_a < 100000 AND number_b < 100000;

-- Query 4
EXPLAIN ANALYZE SELECT number_c from random_numbers where number_a < 100000 OR number_b < 100000;
```

![](../assets/img/indexing/single_index_result_4.png)
![](../assets/img/indexing/single_index_result_5.png)


Since I created a composite index with `number_a` as the first key and `number_b` as the second, the index cannot be used to filter only on the `number_b` column. This is because the order of columns in a composite index matters. As stated in <a href="https://ameliarahman.github.io/2024-10/Explain-Postgres" target="_top"> Postgres Documentation </a>:

_A multicolumn B-tree index can be used with query conditions that involve any subset of the index's columns, but the index is most efficient when there are constraints on the leading (leftmost) columns. The exact rule is that equality constraints on leading columns, plus any inequality constraints on the first column that does not have an equality constraint, will be used to limit the portion of the index that is scanned_

 Leading (leftmost) columns refer to the columns listed first when we create the composite index.

Another observation from the analyze result above is that the index is not used for an `OR` condition, as the database executes the query using a sequential scan, even though the set of rows is not too large.

Now, let's try to create another single index on `column_b` alone and see the result again:
![](../assets/img/indexing/single_index_result_6.png)

All the queries are affected by the index. Even for an `OR` condition, the database chooses to use a Bitmap Index Scan, with both the composite index on `(column_a, column_b)` and the single index on `column_b` for better performance.

