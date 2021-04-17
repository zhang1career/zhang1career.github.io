---
layout: post
title:  Splittable Ring Buffer
date:   2021-03-31 16:52:00 +0800
categories: [algorithm, ringbuffer]
---

# Splittable Ring Buffer

A splittable ring buffer is a ring buffer that can be split into several parts. Each part is still a ring buffer. The origin buffer and the split ones share a homomorphism addressing method.

## Split Principal

Addressing a ring buffer is usually via modulo an index by the buffer size. For example, if the buffer size is $size$, the distance between adjacent elements is $step$. And the $0$th element address is $offset$. The $j$th element of the buffer addresses

$$(j \cdot step + offset) \quad mod \quad size$$

Suppose the ring buffer to be split into $s$ parts, regularly with each other element. As shown below, split_s_elem_i represents the $i$th element of the $s$th split.

<table><tbody>
    <tr>
        <th>Memory</th><th>...</th><th>si</th><th>si+1</th><th>...</th><th>s(i+1)-1</th><th>...</th>
    </tr>
    <tr>
        <td bgcolor="#eeeeee">split element</td>
        <td bgcolor="#ffffff">...</td>
        <td bgcolor="#d67070">split_0_elem_i</td>
        <td bgcolor="#a3d670">split_1_elem_i</td>
        <td bgcolor="#ffffff">...</td>
        <td bgcolor="#70d6d6">split_s-1_elem_i</td>
        <td bgcolor="#ffffff">...</td>
    </tr>
</table>


In any split, the distance between adjacent elements is $s$. In the $i$th split, the $0$th element address is $i$. The $j$th element of the $i$th split addresses

$$(j_i \cdot s + i) \quad mod \quad size$$

**Then the split parts are all rings too. And both the origin buffer and the split ones share a homomorphism addressing method.**

Especially, when the origin ring buffer's length is a power-2 number, $2^p$ for instance. And the split number *s* is also a power-2 number,  $2^q$ for instance, with both $p$ and $q$ belonging to $Z^+$ and $p >= q$. Then addressing of the ring buffer can be achieved with just masking,  i.e. in any $split_i$, the $j$th element addresses $(j_i \cdot 2^q + i) \quad \& \quad (2^p-1)$.

## Read/Write Seperation

As shown below, the data written into the origin ring buffer, can be read by split ring buffer separately.

|   memory​    | ...  |    $si$     |    $si+1$     | ...  |     current      | ...  |    $s(i+1)-1$     | ...  |
| :---------: | :--: | :---------: | :-----------: | :--: | :--------------: | :--: | :---------------: | :--: |
|  $split_0$  | ...  | $data_{si}$ |               |      |                  |      |                   | ...  |
| *$split_1$* | ...  |             | $data_{si+1}$ |      |                  |      |                   | ...  |
|     ...     | ...  |             |               | ...  |                  |      |                   | ...  |
|  $split_i$  | ...  |             |               |      | $data_{current}$ |      |                   | ...  |
|     ...     | ...  |             |               |      |                  | ...  |                   | ...  |
|  $split_s$  | ...  |             |               |      |                  |      | $data_{s(i+1)-1}$ | ...  |

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

```java
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

