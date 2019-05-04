---
layout: post
title:  Go 闭包何时计算
date:   2019-04-30 22:35:00 +0800
categories: go
---

> 

## 1. 实现

直接上码：
```
func calcLate() []func()  {
    var funs []func()
    for i:=0; i<2; i++  {
        funs = append(funs, func() {
            println(&i, i)
        })
    }
    return funs
}
 
func calcNow() []func()  {
    var funs []func()
    for i:=0; i<2; i++  {
        x := i
        funs = append(funs, func() {
            println(&x, x)
        })
    }
    return funs
}
```

测试代码：
```
package main
 
func main(){
    funsLate := calcLate()
    for _, fl := range funsLate {
        fl()
    }
    
    funsNow := calcLate()
    for _, fn := range funsNow {
        fn()
    }
}
```