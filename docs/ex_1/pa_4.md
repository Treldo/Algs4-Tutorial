---
layout: default
title: PA_4
nav_order: 4
parent: EX_1
---

## 习题 -> 1.4&ensp;算法分析
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 练习

### 1. 证明不同组合数的个数

我们可以使用中学排列与组合的知识：

\\(Res = C_N^3 = \frac{A_N^3}{3!} = \frac{N!}{(N-3)!3!} = \frac{N(N-1)(N-2)}{6}\\)

### 2. 修改 ThreeSum 以处理两值相加超出 int 范围

```java
public static int count(int[] a) {
    int n = a.length;
    int cnt = 0;
    long num_1;
    long num_2;
    for (int i = 0; i < n; i++)
        for (int j = i + 1; j < n; j++)
            for (int k = j + 1; k < n; k++)
                if (a[i] + a[j] + a[k] == 0) {
                    num_1 = a[i];
                    num_2 = a[j];
                    if (num_1 + num_2 > Integer.MAX_VALUE) continue;
                    if (a[i] + a[j] == -a[k]) cnt++;
                }
    return cnt;
}
```

我在这里用了两个 long 类型来保存三个数中前两个数的值，当它们相加超过 `Integer.MAX_VALUE` 时，continue...

### 3. 修改 DoublingTest

修改 main 函数：

```java
public static void main(String[] args) {
    StdDraw.setXscale(0, 20000);
    StdDraw.setYscale(-50, 200);
    StdDraw.setPenRadius(0.01);
    for (int n = 250; true; n += n) {
        double time = timeTrial(n);
        StdOut.printf("%7d %7.1f\n", n, time);
        StdDraw.point(n, time);
    }
}
```

生成的图像类似于这样：

<img src="http://foyoodo.oss-cn-beijing.aliyuncs.com/image_src/sketch/DoublingTest.png" width="200"/>

### 4. 为 TwoSum 建立表格

这个 TwoSum 大致是这样的，我们标出语句块：

```java
public class TwoSum {
    public static int count(int[] a) {
        int N = a.length;                   // <A>
        int cnt = 0;
        for (int i = 0; i < N; i++)         // <B>
            for (int j = i + 1; j < N; j++) // <C>
                if (a[i] == -a[j])          // <D>
                    cnt++;                  // </B> </C> </D>
        return cnt;                         // </A>
    }
}
```

<br />

| 语句块 | 运行时间（以秒记） | 频&emsp;率 | 总 时 间|
| :------------- | :------------- | :------------- | :------------- |
| D | \\(t_0\\) | \\(x\\)（取决于输入） | \\(t_0 {x}\\)|
| C | \\(t_1\\) | \\(\frac{N^2}{2} - \frac{N}{2}\\)| \\(t_1 (\frac{N^2}{2} - \frac{N}{2})\\)|
| B | \\(t_2\\) | \\(N\\) | \\(t_2 {N}\\)|
| A | \\(t_3\\) | \\(1\\) | \\(t_3\\)|
|  |  | \\(总时间\\) | \\(\frac{t_1}{2} N^2 + (t_2 - \frac{t_1}{2}) N + t_3 + t_0 x\\)|
|  |  | \\(近似\\) | ~\\(\frac{t_1}{2} N^2\\)（假设 \\(x\\) 很小）|
|  |  | \\(增长的数量级\\) | \\(N^2\\) |

### 5. 量的近似

&emsp;a. ~\\(N\\)

&emsp;b. ~\\(\frac{1}{N}\\)

&emsp;c. ~\\(\frac{1}{2}\\)

&emsp;d. ~\\(N^3\\)

&emsp;e. ~\\(lgN\\)

&emsp;f. ~\\(lgN^2\\)

&emsp;g. ~\\(0\\)

### 6. 给出代码的增长数量级

&emsp;a. \\(\frac{1\times(1-2^{log_2{N}+1-1})}{1-2}\\) = \\(2N-1\\) ~ \\(2N\\) ---> 增长数量级为 N

&emsp;b. \\(\frac{1\times(1-2^{log_2{N}+1-1})}{1-2}\\) = \\(2N-1\\) ~ \\(2N\\) ---> 增长数量级为 N

&emsp;c. \\(Nlog_2{N}\\) ---> 增长数量级为 NlgN

&emsp;**注：** 如果 a b 使用等比数列近似计算的话看上面，另有方法详解见 [StackExchange](https://softwareengineering.stackexchange.com/questions/253421/running-time-of-simple-for-loops) 。

### 7. 成本模型

这里的话我们要理解成本模型是我们用来评估算法性质的一个标准。

所以如果统计算术操作为成本模型的话，在第 3 个 for 循环的 if 语句中进行了两次加和操作和一次比较操作，这样的话和数组访问次数相同，也是 ~\\(\frac{N^3}{2}\\) 次。

**证明：** 每次循环执行 3 次操作。

### 8. 整数对的数量

我们可以模仿 TwoSumFast 的做法，它的增长数量级一样也为 \\(NlogN\\) 。

```java
public static void main(String[] args) {
    int[] a = StdIn.readAllInts();
    int cnt = 0;
    Arrays.sort(a);
    for (int i = 0; i < a.length; i++) {
        if (BinarySearch.indexOf(a, a[i]) > i)  cnt++;
    }
    System.out.println(cnt);
}
```

### 9. 给出公式

\\(T(N_0) = 2^b\cdot{T}\\)

\\(T(2N_0) = (2^b)^2\cdot{T}\\)

\\(...\\)

\\(T(N) = (2^b)^{lg\frac{N}{N_0}}\cdot{T}\\)

\\(T(N) = (\frac{N}{N_0})^b\cdot{T}\\)

**说明：** 这里的 \\(lg\\) 也就是 \\(log_2\\) 。

### 10. 修改 BinarySearch

```java
public static int indexOf(int[] a, int key) {
    int lo = 0;
    int hi = a.length - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (key < a[mid]) hi = mid - 1;
        else if (key > a[mid]) lo = mid + 1;
        else for (int i = mid; true; i--)
                if (a[i] != a[mid]) return i + 1;
    }
    return -1;
}
```

这种方法修改的话不能保证数组中有大量待查找元素时的线性对数级别运算，也就是在我要查找的数组中有 N 个与 key 同值的元素。我们也可以尝试用下面这种方法：

```java
public static int indexOf(int[] a, int key) {
    int lo = 0;
    int hi = a.length - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (key < a[mid]) hi = mid - 1;
        else if (key > a[mid]) lo = mid + 1;
        else {
            if (mid > 0 && a[mid - 1] == key) hi = --mid;
            else return mid;
        }
    }
    return -1;
}
```

### 11. 方法 howMany

```java
public static int howMany(int[] a, int key) {
    int lo = 0;
    int hi = a.length - 1;
    int min = BinarySearch.indexOf(a, key);
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        if (key < a[mid]) hi = mid - 1;
        else if (key > a[mid]) lo = mid + 1;
        else {
            if (mid > 0 && a[mid + 1] == key) lo = ++mid;
            else return mid + 1 - min;
        }
    }
    return -1;
}
```

我们这里用到了 BinarySearch 中的 indexOf 方法，但要注意这里 indexOf 是我们改进之后的方法，返回值是数组中的最小索引。我们在 while 中获得了最大索引 mid ，所以元素数量就为 mid + 1 - min 这个值。不用多说，自己试试吧!

### 12. 打印公共元素

```java
public static void sameElements(int[] a, int[] b) {
    for (int i = 0, j = 0; i < a.length && j < b.length; ) {
        if (a[i] < b[j]) i++;
        else if (a[i] > b[j]) j++;
        else {
            if (i == 0 || a[i] != a[i - 1]) System.out.printf("%d ", a[i]);
            i++;
            j++;
        }
    }
}
```

上面的例子考虑到了多种可能发生的情况，且打印出的元素不会重复。

### 13. 内存量

&emsp;a. Accumulator 中有 16 字节的对象开销，8 字节保存 double 值，4 字节保存 int 值以及 4 个填充字节，共 32 字节；

&emsp;b. Transaction 中有 16 字节的对象开销，40 字节用来保存 String 对象，8 字节保存 double 值，以及保存 Date 对象的 32 字节，共 96 字节；

&emsp;c. FixedCapacityStackOfStrings 中有 16 字节的对象开销，4 字节用来保存 int 值，\\(\displaystyle\sum_{i=0}^N{(40+(24+2n))}\\) 个字节用来保存字符串数组（n 表示每个字符串所含字符的个数），以及 4 个填充字节，共 \\((\displaystyle\sum_{i=0}^N{(40+(24+2n))} + 24)\\) 个字节；

&emsp;<font color="red">‼️</font> **下面的四个都不确定，有待修正…**

&emsp;d. Point2D 中有 16 字节的对象开销，两个 8 字节的 double 值，共 32 个字节；

&emsp;e. Interval1D 中有 16 字节的对象开销，两个 8 字节的 double 值，共 32 个字节；

&emsp;f. Interval2D 中有 16 字节的对象开销，两个 32 字节的 Interval1D 对象，共 80 字节；

&emsp;g. Double 中有 16 字节的对象开销，六个 8 字节的 double 值，四个 4 字节的 int 值，共 80 字节。

---
