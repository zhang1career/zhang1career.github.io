---
layout: post
title:  如何从 go 的应用代码抽象出通用代码
date:   2019-02-17 21:40:00 +0800
categories: go
---

# 如何从 go 的应用代码抽象出通用代码

## 1. 要点
+ 可以使用回调函数
+ 可以使用回调接口

## 2. 原理

### 2.1.回调函数
通用代码保留在通用函数中；把应用代码写入回调函数中；把回调函数作为参数传给通用函数，供其调用。

优点
+ 这是代码抽象的基本方式
+ 编译时多态

缺点
+ 不是运行时多态

### 2.2. 回调接口
+ 通用代码定义实例接口，应用代码实现这个接口
+ 动态代理

优点
+ 运行时多态

## 3. 示例

### 3.1. 通用代码

定义 Work 接口，执行 Work 接口定义的 Do()：
```
type Work interface {
    Do() interface{}
}

type worker struct {
    id      int
    Work
}

func createWorker(id int, work Work) *worker {
    return &worker{id: id, Work: work}
}
 
func (this *worker) run() {
    go func() {
        // 执行 Work 接口定义的 Do()
    }()
}
```

接收应用代码创建的实例，执行相应的 Do()
```
func (this *Parser) AddWorker(num int, out chan int, callback chan Work) {
    for i := 0; i < num; i++ {
        out <- i
        work := <- callback
        worker := createWorker(i, work)
        worker.run()
    }
}
```

### 3.2. 应用代码

实现 Work 接口：
``` 
func (this *snowflake) Do() interface{} {
   return fmt.SPrintf("worker%d doing", this.id)
}
```

创建实例，以 Work 接口返回给通用代码：
```
func CreateSnowFlow(machineIds chan int) chan Work {
   output := make(chan Work)
   go func() {
      defer close(output)
      for {
         select {
         case id := <-machineIds:
            output <- createSnowFlake(id)
         }
      }
   }()
   return output
}

type snowflake struct {
   id int
}

func createSnowFlake(id int) *snowflake {
   return &snowflake{id: id}
}
```

### 3.3. 测试代码
```
func TestParser(t *testing.T) {
   machineIds := make(chan int)
   snows := CreateSnowFlow(machineIds)
    
   p := Parser{}
   p.AddWorker(100, machineIds, snows)
   p.Run()
}
```