---
layout: post
title:  Troubleshootings for MySQL
date:   2018-12-10 15:06:00 +0800
categories: troubleshooting
---

> 


## 1. [Server SQL Modes](https://dev.mysql.com/doc/refman/5.7/en/sql-mode.html)

+ [SELECT list is not in GROUP BY clause and contains nonaggregated column … incompatible with sql_mode=only_full_group_by](https://stackoverflow.com/questions/41887460/select-list-is-not-in-group-by-clause-and-contains-nonaggregated-column-inc)

&nbsp;&nbsp;&nbsp;&nbsp;查看全局sql_mode
{% highlight sql %}
SELECT @@global.sql_mode;
{% endhighlight %}

&nbsp;&nbsp;&nbsp;&nbsp;设置全局sql_mode
{% highlight sql %}
SET @@global.sql_mode=(SELECT REPLACE(@@global.sql_mode, 'ONLY_FULL_GROUP_BY', ''));
{% endhighlight %}

&nbsp;&nbsp;&nbsp;&nbsp;仅仅设置全局，需要重新连接进来才会生效
