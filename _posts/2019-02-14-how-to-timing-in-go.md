---
layout: post
title:  如何实现 go 的时效控制
date:   2019-04-30 22:35:00 +0800
categories: go
---

> 

## 1. 实现

直接上码：
```
func timing(out chan<- interface{}, in1, in2 <-chan interface{}) {
    heartbeat := time.Tick(time.Second)
	lifetime := time.After(10 * time.Second)
	for {
		select {
		// heartbeat
		case <-heartbeat:
			fmt.Println("queue len =", len(inBuffer))
		// lifetime
		case <-lifetime:
			fmt.Println("bye")
			return
		}
	}
```