---
layout: post
title: "Dump and Restore PostgreSQL"
description: "How to dump postgres"
date: 2023-10-11 19:36:18
comments: true
keywords: "postgresql, database, sql, rds"
category: [Tech]
tags:
- database
---

During development, there will be times when we need to dump database from staging/production environment and restore it to our local. After gathering information from various sources (<a href="https://www.postgresql.org/docs/" target="_top">PostgeSQL</a>, <a href="https://docs.docker.com/engine/reference/commandline/exec/" target="_top">Docker Command</a>, stackoverflow and ask to ChatGPT) and trial error, here's some steps that I usually do:

<ul>
  <li>SSH to desired server</li>
  <li>Dump the database:
    ```
    pg_dump -h hostname_postgresql -U username -d database_name > filename.sql;
    ```
    <ul>
      <li>pg_dump: command in PostgreSQL used for creating backups (dumps) of PostgreSQL databases.</li>
      <li>-h hostname_postgresql: specify the hostname of PostgreSQL server.</li>
      <li>-U username: username used to connect to database.</li>
      <li>-d database_name: database name</li>
      <li> filename.sql: > symbol is used for output redirection and filename.sql is the generated file. <li>
    </ul>
    </li>
    <li>Download the file:
    ```
    scp -i private_key user@hostname:source_filename.sql destination_filename.sql
    ```
    <ul>
      <li>scp: stands for Secure Copy Protocol, used to copy files between local and remote system.</li>
      <li>-i private_key: specifies the private key file to use for authentication.</li>
      <li>user@hostname: specifies the username and hostname of the server</li>
      <li>source_filename.sql: the path to the file on the server that we want to copy</li>
      <li>destination_filename.sql: the path where we want to save the file on our local <li>
    </ul>
    </li>
  <li>Restore the database to dockerized PotsgreSQL:
    ```
    cat your_dump.sql | docker exec -i your-db-container psql -U database_user
    ```
    <ul>
      <li>cat dump_file.sql: command to read dump_file.sql</li>
      <li>|: used to take the outpout from "cat" command and take it as the input to next command</li>
      <li>docker exec -i your-db-container: execute a command in a running container </li>
      <li>psql -U database_user: used to interact with PostgreSQL databases with -U is the option that specifies the database user to connect as.</li>
    </ul>
  </li>
  <li>Restore the database in custom format
    In other case, I have also encountered dump file with a .tar extension.
    ```
    pg_restore -h hostname_postgresql -U username -d database_name -F t backup_file.tar
    ```
    for inside docker container:
     ```
    pg_restore -U username -d database_name -F t backup_file.tar
     ```
    <ul>
      <li>-F t: specifies the format of the backup file, in this case it's in custom format.</li>
    </ul>
  </li>
</ul>


