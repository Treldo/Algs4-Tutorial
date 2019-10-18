---
layout: default
title: PA_3
nav_order: 3
parent: EX_2
---

## 习题 -> 2.3&ensp;快速排序
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 练习

### 1. 切分轨迹

<div style="text-align: center;">
  <img src="http://foyoodo.oss-cn-beijing.aliyuncs.com/image_src/algs4/2.3/partition-3.1.1.png" width="270" />
</div>

<br />

### 2. 排序轨迹

<div style="text-align: center;">
  <img src="http://foyoodo.oss-cn-beijing.aliyuncs.com/image_src/algs4/2.3/quick-3.1.2.png" width="300" />
</div>

**注意**：这个题目是利用快速排序进行的数组排序轨迹，题解在排序开始前进行了一次打乱。

### 3. 交换次数

对于最大元素，可以在每一次的切分中都进行交换，总共进行切分 \\(lgN\\) 次，所以最多进行 \\(lgN\\) 次交换。

### 4. 交换次数最多的数组

这里给出一个例子：

```
E F G J I H B A D C
```

**排序轨迹**：

```
E F G J I H B A D C
E C G J I H B A D F    lo = 0   i = 1    j = 9
E C D J I H B A G F    lo = 0   i = 2    j = 8
E C D A I H B J G F    lo = 0   i = 3    j = 7
E C D A B H I J G F    lo = 0   i = 4    j = 6
B C D A E H I J G F    lo = 0   i = 5    j = 4
B A D C E H I J G F    lo = 0   i = 1    j = 3
A B D C E H I J G F    lo = 0   i = 2    j = 1
A B C D E H I J G F    lo = 2   i = 3    j = 3
A B C D E H F J G I    lo = 5   i = 6    j = 9
A B C D E H F G J I    lo = 5   i = 7    j = 8
A B C D E G F H J I    lo = 5   i = 8    j = 7
A B C D E F G H J I    lo = 5   i = 6    j = 6
A B C D E F G H I J    lo = 8   i = 9    j = 9
```

### 5. 将只有两种主键值的数组排序

See the code <a href="https://algs4.cs.princeton.edu/23quicksort/Sort2distinct.java.html">Sort2distinct.java <i class="fab fa-java"></i></a> .

### 6. 准确值和估值

要计算 \\(C_N\\) 的准确值可以设置一个整型来记录比较次数，在 `less()` 方法里对记录值进行递增操作，然后在与估计值 \\(2NlnN\\) 进行比较。

### 7. 子数组的数量

```java
private static void sort(Comparable[] a, int lo, int hi) {
    if (hi <= lo) return;          // array_size = 1
    int j = partition(a, lo, hi);
    sort(a, lo, j - 1);            // Sort left part a[lo .. j-1].
    sort(a, j + 1, hi);            // Sort right part a[j+1 .. hi].
}
```

可以在上面的 `sort()` 方法中添加对 lo 与 hi 差值的判断。

### 8. 对全部重复数组的比较次数

相当于每次切分元素都是中间元素，且交换次数最多的情况。比较次数是 ~ \\(lgN\\) 。

### 9. 处理两三种主键值时数组的行为

**两种主键值**：第一次切分后已经排序完成，剩下的都是进行等值交换，不影响总体顺序；

**三种主键值（两种情况）**：
- 第一次的切分元素是中值元素： 进行一次切分即完成排序，剩下的都是等值交换；
- 第一次的切分元素不是中值元素： 在第二次切分完成排序。

### 10. 概率

### 11. 证明是平方级别

### 12. 切分轨迹

<div style="text-align: center;">
  <img src="http://foyoodo.oss-cn-beijing.aliyuncs.com/image_src/algs4/2.3/partition-3.1.12.png" width="300" />
</div>

<br />

### 13. 递归深度

### 14. 证明概率

---
