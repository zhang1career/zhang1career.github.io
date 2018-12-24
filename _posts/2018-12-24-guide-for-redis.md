---
layout: post
title:  Guide for Redis
date:   2018-12-14 10:27:00 +0800
categories: redis
---

> 


## 1. List[^1]

### Read value by index

{% highlight shell %}
redis> LINDEX foo 1
{% endhighlight %}


### Preserve part, delete others

{% highlight shell %}
redis> LTRIM foo 1 100
{% endhighlight %}

## Reference
[^1]: https://redis.readthedocs.io/en/2.4/list.html
