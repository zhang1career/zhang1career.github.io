---
layout: post
title:  如何实现协程的可观测性
date:   2019-05-03 17:20:00 +0800
categories: go
---

> 协程可以被控制，但是难以被观测。本文尝试实现若干观测协程的方法。


## 1.要点
+ 心跳

## 2.原理和实现
### 2.1.心跳
+ 父协程创建 channel，将其放入 context.Context.WithValue 中，传递给子协程
+ 子协程定期向 channel 中写入心跳信号
+ 父协程监控心跳信号，如果一段时间内没有收到心跳信号，那么认为子协程发生协程运行故障

代码如下：
```
const key = "heartbeat"
 
func main() {
    // 父协程创建 channel
    listener := make(chan interface{})
    ctx := context.WithValue(context.Background(), key, listener)
    defer ctx.Done()
    // 启动子协程
    go heart(ctx)
    // 父协程监控心跳信号
    for o := range listener {
        fmt.Println(o)
    }
    fmt.Println("no heartbeat!!!")
}
 
func heart(ctx context.Context) {
    v := ctx.Value(key).(chan<- interface{})
    if v == nil {
        fmt.Println("context key not found:", key)
        return
    }
    defer close(v)
    // 子协程定期向 channel 中写入心跳信号
    tick := time.Tick(time.Second)
    for i:=1; i<50; i++ {
        select {
        case t := <- tick:
            v <- t
        default:
            time.Sleep(100 * time.Millisecond)
        }
    }
}
```

## A.名词解释
协程运行故障：协程在运行期间的不可预期状态，包含阻塞、死循环等。

## B.参考

## C.Todo
结合《如何实现无限写入的管道》的方法，通过降低 channel 的读写耦合，达到降低编程难度的目标。

