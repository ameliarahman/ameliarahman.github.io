---
layout: post
title: "Improving Query Performance Using Join On Coalesce"
description: "How to improve "
date: 2023-10-11 11:09:00
comments: true
keywords: "postgresql, database, sql"
category: [Tech]
tags:
- database, query, postgres
---

There are times when we need to join a table with a null condition. If `column a` is null, then join it using the condition with column b, and vice versa.

Initially, I thought it could be easily done using a `CASE WHEN` query. However, when the data is already large, it poses performance issues.

Let's jump to the example. 
Create two tables and seed them with 100,000 rows of data:
<script src="https://gist.github.com/ameliarahman/7af1db68d32108f2f49c2b65dffeda7f.js"></script>
<script src="https://gist.github.com/ameliarahman/21a61ca6a27c5773abb0ce7b828efd1e.js"></script>
