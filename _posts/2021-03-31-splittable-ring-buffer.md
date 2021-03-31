layout: post
title:  Splittable Ring Buffer
date:   2021-03-31 16:52:00 +0800
categories: data-structure



# Splittable Ring Buffer

Splittable ring buffer is a ring buffer that can split into several parts. Each part is still a ring buffer.

## Split Principal

Writting of a ring buffer is usually by a continnously increasing index. Suppose the ring buffer to be split into *s* parts, then the reading of each split*<sub>i</sub>* needs a step of *n* and an offset of *i*, with *s* and *i* being *Z<sup>+</sup>*.

For example, if the index = i, the data<sub>i</sub> can be write into memory[i], and the split<sub>i</sub> can read a new item, which values data<sub>i</sub>.

Specially, when the origin ring buffer's length is a power-2 number, *2<sup>r</sup>* for instance. And the split number *s* is also a power-2 number, 2*<sup>n</sup>* for instance, with *r* and *n* being *Z<sup>+</sup>* and *r >= n*. Then the ring buffer addressing can be achieved with just masking unsigned integer overflow.

Then the split parts are all rings too.

|      memory       | ...  |        0         |       *1*        | ...  |       *i*        | ...  |      *s-1*       | ...  |
| :---------------: | :--: | :--------------: | :--------------: | :--: | :--------------: | :--: | :--------------: | :--: |
|       index       |      |                  |                  |      |        i         |      |                  |      |
| split<sub>0</sub> | ...  | data<sub>0</sub> |                  |      |                  |      |                  | ...  |
| split<sub>1</sub> | ...  |                  | data<sub>1</sub> |      |                  |      |                  | ...  |
|        ...        | ...  |                  |                  | ...  |                  |      |                  | ...  |
| split<sub>i</sub> | ...  |                  |                  |      | data<sub>i</sub> |      |                  | ...  |
|        ...        | ...  |                  |                  |      |                  | ...  |                  | ...  |
| split<sub>s</sub> | ...  |                  |                  |      |                  |      | data<sub>s</sub> | ...  |

## Demo code

Calculate *step* and *offset*, then split the ring buffer into *num* parts.

```java
public void split(int num) {
    step = BinaryUtils.smallestPow2GreaterThanOrEqualTo(num);
    for (int offset = 0; offset < step; i++) {
        holdingChildren.add(new SplittableRingBuffer<>(this, data, step, offset, IO_TYPE.WO));
    }
}
```

## Conclusion

Split mechanism separates a ring buffer into several parts, which can be read/write seperately. Some pingpong buffer can be designed based on this.

## Referance

[Source code](https://github.com/zhang1career/javalab/blob/master/datastruct/src/main/java/datastruct/ringbuffer/SplittableRingBuffer.java)

