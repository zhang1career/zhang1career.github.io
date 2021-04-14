---
layout: post
title:  如何实现 map 排序
date:   2019-04-30 20:10:00 +0800
categories: go
---

# 如何实现 map 排序

## 1. 要点

+ map 是无序的
+ 先把 map 转换成 array，再对 array 排序

## 2. 实现

代码如下：
```
type KV struct {
    Key: int
    Value: interface{}
}

func SortMap(m map[int]interface{}) []KV {
    var keys []int
    
    for k := range m {
        keys = append(keys, k)
    }
    sort.Ints(keys)
    
    ret := make([]KV, len(m))
    for i, k := range keys {
        ret[i] = KV{Key: k, Value: m[k]}
    }
    
    return ret
}
```