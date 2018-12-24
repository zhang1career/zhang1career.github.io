---
layout: post
title:  Tricks for Git
date:   2018-12-10 15:06:00 +0800
categories: git
---

> 


## 1. Branch

+ track all remote branch

使用shell命令
{% highlight shell %}
for remote in `git branch -r`; do git branch --track ${remote#origin/} $remote; done
{% endhighlight %}

