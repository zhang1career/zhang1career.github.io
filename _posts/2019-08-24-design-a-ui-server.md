---
layout: post
title:  Server端UI层设计思想
date:   2019-08-24 20:05:00 +0800
categories: ui
---

> 

## 1. 

### 1.1. 



```
+---------------+
|               |
|      App      |
|               |
|               |
+---------------+




+---------------+---------------+---------------+
|               |               |               |
|      Home     |     Ploan     |      Pay      |
|   UI Server   |   UI Server   |   UI Server   |
|               |               |               |
+---------------+---------------+---------------+
|                                               |
|                     Base                      |
|                   UI Server                   |
|                                               |
+-----------------------------------------------+




+-----------------------------------------------+
|                                               |
|                      Log                      |
|                                               |
|                                               |
+-----------------------------------------------+




+---------------+
|               |
|    Analyzer   |
|               |
|               |
+---------------+

```



## A. Reference

[Building a modular UI with server-driven rendering](https://infinum.co/the-capsized-eight/building-modular-ui-with-server-driven-rendering)

[Client Side vs Server Side UI Rendering. Advantages and Disadvantages](https://blog.webix.com/client-side-vs-server-side-ui-rendering)


