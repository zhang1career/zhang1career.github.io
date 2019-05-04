---
layout: post
title:  如何实现变参函数
date:   2019-02-14 20:30:00 +0800
categories: go
---

> 

## 1. 实现

直接上码：
```
func demo(params ...interface{}) {
    s := make([]interface{}, 0)
    if len(args) > 0 {
        for _, p := range params {
            s = append(s, p)
        }
    }
    fmt.Println(s)
}
```