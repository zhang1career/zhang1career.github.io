---
layout: post
title:  Guide for Redis
date:   2018-12-14 10:27:00 +0800
categories: redis
---

> 


## 1. List[^1]

### Get length of list

{% highlight shell %}
redis> LLEN key
{% endhighlight %}


### Read value by index

{% highlight shell %}
redis> LINDEX key index
{% endhighlight %}


### Preserve part, delete others

{% highlight shell %}
redis> LTRIM key start stop
{% endhighlight %}


## Reference
[^1]: https://redis.readthedocs.io/en/2.4/list.html
