---
layout: project
title: SQL CLR - Numbers
categories: [sql_clr, project]
tags: projects, SQL CLR, Tally, Numbers
project_brief: > 
  The numbers (or tally) table is an invaluable tool in creating better performing string manipulation within TSQL. In this project, I have created two simple CLR Table Valued Functions to generate a set of numbers: GetNumbers and GetNumberRange
projectcategory: SQL_CLR
project: SQL_CLR_Numbers
---

# Project: SQL CLR - Numbers

## About this project
An invaluable tool to better performing string manipulation within TSQL is the numbers or tally table, advocated by many of the developers within the SQL Server community (e.g. two such blogs: http://sqlblog.com/blogs/adam_machanic/archive/2006/07/12/you-require-a-numbers-table.aspx and http://www.sqlservercentral.com/articles/T-SQL/62867/). Every SQL developer or DBA seems to have their own method for generating the numbers table to meet their needs, however with the inclusion of CLR creating a numbers table to fit your need has never been easier, nor in fact better performing.

I have created two simple CLR table value functions to generate a set of numbers to meet the users need. 
- **GetNumbers** - This function takes a single parameter, the number of numbers to return. It returns a table of integers, starting from one.
- **GetNumberRange** - This function takes two parameters, a start and an end number, and returns a table of all integers from and including the first to and including the second parameter. 

Because CLR runs in memory both of these function are FAST. 

## GetNumbers
```SQL
SELECT * FROM GetNumbers(@MaxValue INT) N
```
The GetNumbers method returns a list of integers, from 1 to the number specified by `@MaxValue`.

## GetNumbersRange
```
SELECT * FROM GetNumbersRange(@FromValue INT, @ToValue INT) N
```

### Downloading
This project is available from https://github.com/N1NFMind/sqlclr-numbers

### Installing, Terms of Use, Making Changes, etc.
Please refer to [SQL CLR]({{ site.baseurl }}/projects/SQL_CLR/) for information on all this house-keeping stuff.
