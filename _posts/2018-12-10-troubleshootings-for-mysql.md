---
layout: post
title:  Troubleshootings for MySQL
date:   2018-12-10 15:06:00 +0800
categories: troubleshooting
---

> 


## 1. 

### group by

+ group by 
{% highlight sql %}
SET @@global.sql_mode=(SELECT REPLACE(@@global.sql_mode, 'ONLY_FULL_GROUP_BY', ''));
{% endhighlight %}

