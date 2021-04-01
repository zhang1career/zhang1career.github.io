layout: post
title:  Splittable Ring Buffer
date:   2021-03-31 16:52:00 +0800
categories: data-structure



# Splittable Ring Buffer

Splittable ring buffer is a ring buffer that can be split into several parts. Each part is still a ring buffer.

## Split Principal

Writting to a ring buffer is usually by a continnously increasing index. Suppose the ring buffer to be split into *s* parts, then the addressing of each split*<sub>i</sub>* needs a *step* of *n* and an *offset* of *i*, with both *s* and *i* belonging to *Z<sup>+</sup>*.

For example, if the tail of the *split<sub>i</sub>* addresses *memory[p step + offset]*, with *p* belonging to *Z<sup>+</sup>*. Given *index = i*, a *data<sub>i</sub>* is write to *memory[i]*, then the *split<sub>i</sub>* gets a new item in *memory[(p+1) step + offset]*, which values *data<sub>i</sub>*.

|      *memory*       | ...  |        *0*         |        *1*         | ...  |        *i*         | ...  |       *s-1*        | ...  |
| :-----------------: | :--: | :----------------: | :----------------: | :--: | :----------------: | :--: | :----------------: | :--: |
|       *index*       |      |                    |                    |      |        *i*         |      |                    |      |
| *split<sub>0</sub>* | ...  | *data<sub>0</sub>* |                    |      |                    |      |                    | ...  |
| *split<sub>1</sub>* | ...  |                    | *data<sub>1</sub>* |      |                    |      |                    | ...  |
|         ...         | ...  |                    |                    | ...  |                    |      |                    | ...  |
| *split<sub>i</sub>* | ...  |                    |                    |      | *data<sub>i</sub>* |      |                    | ...  |
|         ...         | ...  |                    |                    |      |                    | ...  |                    | ...  |
| *split<sub>s</sub>* | ...  |                    |                    |      |                    |      | *data<sub>s</sub>* | ...  |

Specially, when the origin ring buffer's length is a power-2 number, *2<sup>r</sup>* for instance. And the split number *s* is also a power-2 number, 2*<sup>n</sup>* for instance, with both *r* and *n* belonging to *Z<sup>+</sup>* and *r >= n*. Then addressing of the ring buffer can be achieved with just masking *(2<sup>r-n</sup> step + offset)* overflow.

**Then the split parts are all rings too.**

## Demo Code

Calculate *step* and *offset*, then split the ring buffer into *num* parts.

```java
public void split(int num) {
    step = BinaryUtils.smallestPow2GreaterThanOrEqualTo(num);
    for (int offset = 0; offset < step; i++) {
        holdingChildren.add(new SplittableRingBuffer<>(this, data, step, offset, IO_TYPE.WO));
    }
}
```

Write to buffer

```java
public Boolean offer(E element) {
    if (isFull()) {
        return null;
    }
    setElement(element);
    stepWriteIndex();
    return isFull();
}
```

Read from buffer

```java
public E poll() {
    if (isEmpty()) {
        return null;
    }
    E element = getElement();
    stepReadIndex();
    return element;
}
```

Relative codes

```
public static int smallestPow2GreaterThanOrEqualTo(int num) {
    if ((num & (num - 1)) == 0) {
        return num;
    }
    return smallestPow2GreaterThan(num);
}

private void setElement(E element) {
    data[normalize(writeIndex)] = element;
}

private E getElement() {
    return data[normalize(readIndex)];
}

private void stepWriteIndex() {
    writeIndex += step;
    readableLength++;
}

private void stepReadIndex() {
    readIndex += step;
    readableLength--;
}

private int normalize(int index) {
    return index & mask;
}

private boolean isEmpty() {
    return readableLength <= 0;
}

private boolean isFull() {
    return readableLength >= length;
}
```



## Conclusion

Split mechanism separates a ring buffer into several parts, which can be read/write seperately. Some pingpong buffer can be designed based on this.

## Referance

[Source code](https://github.com/zhang1career/javalab/blob/master/datastruct/src/main/java/datastruct/ringbuffer/SplittableRingBuffer.java)

