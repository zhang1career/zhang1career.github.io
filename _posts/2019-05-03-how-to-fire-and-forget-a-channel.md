---
layout: post
title:  如何实现无限写入的管道
date:   2019-05-03 18:00:00 +0800
categories: go
---

> 管道对于开发者是1个挑战。
> 不论是阻塞型管道（buffer-size = 0），严格要求写入和读取的配合；
> 还是非阻塞型管道（buffer-size > 0），只写不读最终依然会造成管道阻塞。
> 本文尝试实现1种不需要读写配合的管道。


## 1.要点
+ 在写入和读取之间，通过程序逻辑实现无限写入
+ 实现 fire-and-forget 特性，以期降低写入-读取的编程耦合，降低应用开发难度
+ 受到缓存深度的限制，写入数据可能丢失

## 2.原理和实现
### 2.1.心跳
+ 协程监听 in 管道，将收到的数据放入缓存；如果缓存满了就删除最老的数据，写入最新的数据
+ 协程监听 out 管道，将缓存数据放入管道；如果缓存空了就不操作管道

代码如下：
```
type infinite struct {
   in          chan interface{}
   out         chan interface{}
   buffer      []interface{}
   bufLength   int
}
 
func NewInfinite(length int) (chan<- interface{}, <-chan interface{}, error) {
   if length <= 0 {
      return nil, nil, fmt.Errorf("length should be natural number")
   }
    
   item := &infinite{
      in     : make(chan interface{}),
      out    : make(chan interface{}),
      buffer : make([]interface{}, 0),
      bufLength : length,
   }
    
   go func() {
      defer close(item.out)
      item.transceive()
   }()
    
   return item.in, item.out, nil
}
 
func (this *infinite) outChannel() chan interface{} {
   if len(this.buffer) == 0 {
      return nil
   }
   return this.out
}
 
func (this *infinite) outValue() interface{} {
   if len(this.buffer) == 0 {
      return nil
   }
   return this.buffer[0]
}
 
func (this *infinite) popValue() {
   if len(this.buffer) == 0 {
      return
   }
   this.buffer = this.buffer[1:]
}
 
func (this *infinite) pushValue(value interface{}) {
   // replace oldest value by newest value
   if len(this.buffer) >= this.bufLength {
      this.popValue()
   }
   this.buffer = append(this.buffer, value)
}
 
func (this *infinite) transceive() {
   for {
      select {
      case v, ok := <- this.in:
         if !ok {
            this.in = nil
            return
         }
         this.pushValue(v)
      case this.outChannel() <- this.outValue():
         this.popValue()
      }
   }
}
```

测试代码如下：
```
func TestNewInfinite(t *testing.T) {
   // enviroment data
   bufferSize := 0
   in, out, err := NewInfinite(bufferSize)
   if err != nil {
      t.Error(err)
      return
   }
   // reading
   go func() {
      for {
         select {
         case v, ok := <- out:
            if !ok {
               fmt.Println("in has been closed")
               return
            }
            vi := v.(int)
            fmt.Println(vi)
         }
      }
      fmt.Println("finished redading")
   }()
   // writing
   for i := 0; i < 100; i++ {
      fmt.Println("writing", i)
      in <- i
   }
   // closing
   close(in)
   fmt.Println("finished writing")
}
```

## A.参考
