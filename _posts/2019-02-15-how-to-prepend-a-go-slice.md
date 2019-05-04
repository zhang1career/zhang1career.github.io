---
layout: post
title:  如何 prepend 切片
date:   2019-02-15 22:30:00 +0800
categories: go
---

> 

## 1. 示例

直接上码：
```
func Prepend(slice []interface{}, elems ...interface{}) []interface{} {
   if len(elems) <= 0 {
      return slice
   }
    
   ret := slice
   for i := len(elems)-1; i >= 0; i-- {
      ret = append([]interface{}{elems[i]}, ret...)
   }
   return ret
}
```