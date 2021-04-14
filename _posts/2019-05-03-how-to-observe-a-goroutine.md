---
layout: post
title:  协程的可观测性——协程如何传出运行信息
date:   2019-05-03 17:20:00 +0800
categories: go
---

> 协程可以由管道从外部进行控制，控制的依据一般是协程运行信息。本文提出一种协程传出运行信息的方法。

# 协程的可观测性——协程如何传出运行信息

## 1. 要点

+ 基于 context.Context 的 key-value，实现协程传出运行信息的功能

## 2. 原理
+ 父协程创建 channel 并放入 context.Context.WithValue，将 context 传递给子协程
+ 子协程向 channel 写入运行信息
+ 父协程从 channel 读取运行信息，实现监控

代码如下：
```go
const key = "info"

func parent() {
    // 创建 channel
    listener := make(chan interface{})
    ctx := context.WithValue(context.Background(), key, listener)
    defer ctx.Done()
    // 启动子协程
    go children(ctx)
    // 监控运行信息
    for {
        select {
        case o := <- listener:
            fmt.Println("children running", o)
        }
        default:
            time.Sleep(100 * time.Millisecond)
    }    
}
 
func children(ctx context.Context) {
    v := ctx.Value(key).(chan<- interface{})
    if v == nil {
        fmt.Println("context key not found:", key)
        return
    }
    defer close(v)
    // 子协程定期向 channel 中写入运行信息
    tick := time.Tick(time.Second)
    for {
        select {
        case t := <- tick:
            v <- t
        }
    }
}
```

## 3. 实现
+ 子协程定期向 channel 写入心跳信号
+ 父协程监控心跳信号，如果一段时间内没有收到心跳信号，那么认为子协程发生运行故障

代码如下：
```go
const key = "heartbeat"
 
func main() {
    // 父协程创建 channel
    listener := make(chan interface{})
    ctx := context.WithValue(context.Background(), key, listener)
    defer ctx.Done()
    // 启动子协程
    go children(ctx)
    // 父协程监控心跳信号
    for o := range listener {
        fmt.Println(o)
    }
    fmt.Println("no heartbeat!!!")
}
 
func children(ctx context.Context) {
    v := ctx.Value(key).(chan<- interface{})
    if v == nil {
        fmt.Println("context key not found:", key)
        return
    }
    defer close(v)
    // 子协程定期向 channel 中写入心跳信号
    tick := time.Tick(time.Second)
    for {
        select {
        case t := <- tick:
            v <- t
        }
    }
}
```

## A. 名词解释
+ 运行故障：协程在运行期间的不可预期状态，包含阻塞、死循环等。