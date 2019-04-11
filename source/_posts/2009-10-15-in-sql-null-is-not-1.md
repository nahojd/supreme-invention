---
title: "In SQL, null is not != 1"
author: johan
date: 2009-10-15 11:18:29
updated: 2009-10-15 11:18:29
slug: in-sql-null-is-not-1
tags: [sql]
---

Had an interesting error today. An database query that should have returned thousands of lines returned nothing. Turned out it was due to how the inequality operator works in SQL. In most programming languages, like C# or java the inequality operator handles null, so for example the following code (although extremely stupid) will work.
  

``` csharp 
bool? nullValue = null;
bool? trueValue = true;
bool? falseValue = false;
Console.WriteLine((true != nullValue) );
Console.WriteLine((true != trueValue) );
Console.WriteLine((true != falseValue) );
```





This will produce the result *True, False, True*, since true really isn’t equal to null.

In SQL, however, this is not the case. Consider the following SQL query:




``` sql 
-- TestTable data
-- id    name    isTrue
-- 1     test    1
-- 2     test    0
-- 3     test    NULL

select * from TestTable
where isTrue != 0
```





If you’re not familiar with SQL, you might think that this would return two rows, the one where isTrue is 1 and the one where isTrue is NULL. This is obviously not so, otherwise I wouldn’t have written this post. You only get the one where isTrue is 1. This is due to the fact that in SQL, the inequality operator (!= or <>) does not work on null values. So in order to get both lines, you would have to modify the query like this:




``` sql 
select * from TestTable
where isTrue != 0 or isTrue is null
```





Of course, in this case, this is not the correct solution. A column that is obviously Boolean should not allow null values at all! This was also the case in the non-working query I came across. So the moral of the story is:

*Never allow null values in for boolean columns!*


<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:0767317B-992E-4b12-91E0-4F059A8CECA8:4153853d-6d6d-4419-bb58-5aab921cacc0" class="wlWriterEditableSmartContent">Tags: [sql](/tags/sql)</div>
