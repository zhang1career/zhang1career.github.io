---
layout: post
title:  如何选择可变管道
date:   2019-04-30 20:30:00 +0800
categories: go
---

> 

## 1. 要点
+ 可变参数的接收[1]
+ 递归 select
+ reflect.Select[2]

## 2. 原理

### 2.1.递归 select
递归 select 属于运行时多态，可变管道是编译时多态。使用前者对后者予以降维打击

### 2.2. reflect.Select[3]

使用到如下字段：
Field  | Value | Meaning
------ | ----- | ------
chosen |       | 通道序号
recv   |       | 接收内容
recvOK | true  | 通道正常工作
       | false | 通道已经关闭

## 3. 示例

### 3.2. reflect.Select

代码如下：
```
import (
    "fmt"
    "reflect"
)
 
func Mux(out chan<- interface{}, varIns ...<-chan interface{}) {
    ins := make([]<-chan interface{}, 0)
    if len(varIns) > 0 {
       for _, in := range varIns {
          ins = append(ins, in)
       }
    }
    
    cases := make([]reflect.SelectCase, len(ins))
    for i, in := range ins {
        cases[i] = reflect.SelectCase{Dir: reflect.SelectRecv, Chan: reflect.ValueOf(in)}
    }
    
    remaining := len(cases)
    for remaining > 0 {
        chosen, value, ok := reflect.Select(cases)
        // The chosen channel has been closed, so zero out the channel to disable the case
        if !ok {
            cases[chosen].Chan = reflect.ValueOf(nil)
            remaining -= 1
            continue
        }
        fmt.Printf("Channel%#v: %s\n", ins[chosen], value.String())
        out <- value
    }
}
```

## A. 参考
1. [如何实现变参函数](https://zhagn1career.github.io/go/2019/02/14/how-to-handle-variadic-function.html)
2. [You Should use reflect.Select](https://stackoverflow.com/questions/19992334/how-to-listen-to-n-channels-dynamic-select-statement)
3. [reflect.Select](https://golang.org/pkg/reflect/#Select)
4. [使用SelectCase来做不定数量的channel选择](http://tevic.github.io/2016/05/10/select-case/)
