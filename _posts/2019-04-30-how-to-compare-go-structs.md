---
layout: post
title:  如何比较 go 的结构体
date:   2019-04-30 21:35:00 +0800
categories: go
---

> 

## 1. 要点
+ 如果元素类型不同、顺序不同，那么结构体不可比较
+ 如果元素包含不可比较类型（map, slice），那么结构体不可比较
+ 对于不可比较的结构体，使用 == 会报错，而应该使用 reflect.DeepEqual

## 2. 实现

代码如下：
```
sn1 := struct {
    age  int
    name string
}{age: 11, name: "qq"}
 
 
sn2 := struct {
    age  int
    name string
}{age: 11, name: "qq"}
 
if reflect.DeepEqual(sn1, sn2) {
    fmt.Println("sn1 == sn2")
} else {
    fmt.Println("sn1 != sn2")
```