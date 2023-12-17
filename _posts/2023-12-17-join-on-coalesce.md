---
layout: post
title: "Improving Query Performance Using Join On Coalesce"
description: "How to improve "
date: 2023-12-17 11:09:00
comments: true
keywords: "postgresql, database, sql"
category: [Tech]
tags:
- database, query, postgres
---

There are times when we need to join a table with a null condition. If `column a` is null, then join it using the condition with `column b`, and vice versa.

Initially, I thought it could be easily done using a `CASE WHEN` expression. However, when dealing with a large data, it poses performance issue.

Let's proceed with the example. 
Create two tables and seed them with 100,000 rows of data:
<script src="https://gist.github.com/ameliarahman/7af1db68d32108f2f49c2b65dffeda7f.js"></script>

Seed data using `generate series` for each table. Here, I just add a condition to set table_a with a null value in one column used for the join condition:
<script src="https://gist.github.com/ameliarahman/21a61ca6a27c5773abb0ce7b828efd1e.js"></script>


On the first attempt, I use `CASE WHEN` expression to fetch the data. When `column_a_1` column is not null then join on condition `column_a_1`, otherwise join with `id` column.
<script src="https://gist.github.com/ameliarahman/79b0ea419f49a543f8325057508e2bf9.js"></script>
The `EXPLAIN ANALYZE` result is shown in the following image. It takes quite long to execute:
![](../assets/img/join_coalesce/explain1.png)

Later, I found out that `COALESCE` expression can also be used for join condition. Referring to <a href="https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-coalesce/" target="_top"> PostgreSQL COALESCE </a>, The COALESCE function accepts an unlimited number of arguments. It returns the first argument that is not null. If all arguments are null, the COALESCE function will return null.
So, here is the full query:

<script src="https://gist.github.com/ameliarahman/fb05c5691c48b01cce65c5f537512425.js"></script>
See how the EXPLAIN ANALYZE demonstrates a significant improvement compared to the previous query:

![](../assets/img/join_coalesce/explain2.png)

That's it.
