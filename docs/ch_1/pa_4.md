---
layout: default
title: 1.4&ensp;算法分析
nav_order: 4
parent: 第一章&emsp;基础
---

## 1.4&ensp;算法分析
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

如何更好地将问题规模和运行时间的关系量化？

## 计时器的例子

- 计时器的数据类型
```java
public class Stopwatch {
    private final long start;

    // 创建一个计时器。
    public Stopwatch() {
        start = System.currentTimeMillis();
    }

    // 返回创建对象以来经过的时间。
    public double elapsedTime() {
        long now = System.currentTimeMillis();
        return (now - start) / 1000.0;
    }
}
```

- 对数组中三个不同元素进行加和检验
```java
public class ThreeSum {
    // 计算数组中相邻三个数的加和，如果等于 0 记录，最后返回计算结果为 0 的次数。
    public static int count(int[] a) {
        int n = a.length;
        int cnt = 0;
        for (int i = 0; i < n; i++)
            for (int j = i + 1; j < n; j++)
                for (int k = j + 1; k < n; k++)
                    if (a[i] + a[j] + a[k] == 0)
                        cnt++;
        return cnt;
    }
}
```

- 测试用例
```java
public static void main(String[] args) {
    int n = Integer.parseInt(args[0]);
    int[] a = new int[n];
    for (int i = 0; i < n; i++) {
        a[i] = StdRandom.uniform(-1000000, 1000000);
    }
    Stopwatch stopwatch = new Stopwatch();
    int cnt = ThreeSum.count(a);
    System.out.println(cnt+ "   " + stopwatch.elapsedTime() + "s");
}
```

- 下面这个例子是打印一个图，横坐标为问题规模 N ，纵坐标为处理 N 个随机 6 位整数的运行时间。
```java
public class Test {
    public static double timeTrial(int N) {
        int MAX = 1000000;
        int[] a = new int[N];
        for (int i = 0; i < N; i++)
            a[i] = StdRandom.uniform(-MAX, MAX);
        Stopwatch timer = new Stopwatch();
        int cnt = ThreeSum.count(a);
        return timer.elapsedTime();
    }

    public static void main(String[] args) {
        // 坐标轴的范围在这里修改
        StdDraw.setXscale(0, 5000);
        StdDraw.setYscale(-1, 5);
        StdDraw.setPenRadius(.008);
        StdDraw.setPenColor(StdDraw.RED);
        // 这里可以修改 N 的测试范围
        for (int N = 1000; N <= 4000; N += 100) {
            double time = timeTrial(N);
            System.out.printf("%7d %5.1f\n", N, time);
            StdDraw.point(N, time);
        }
    }
}
```

我使用这个方法进行了两组操作，分别得到了 N 从 100 到 4000 ，和 N 从 1000 到 4000 的图，它们分别是：

<img src="{{ site.baseurl }}{% link /assets/images/1.4/100-4000.png %}" width="240">
<img src="{{ site.baseurl }}{% link /assets/images/1.4/1000-4000.png %}" width="240">

正常来说随着问题规模 N 的增加，运行时间也应该不断增加。但是 N 从 1000 到 4000 的图像却分成了两个部分，我还没有弄明白，你也运行一下试试看吧。

## 数学模型

我们观察 ThreeSum 这个类的实现，它计算了在数组中任意三个不同元素的和：

```
a[1] + a[2] + a[3]
a[1] + a[2] + a[4]
...
a[1] + a[2] + a[N - 1]
a[1] + a[3] + a[4]
a[1] + a[3] + a[5]
...
a[2] + a[3] + a[4]
...
a[N - 3] + a[N - 2] + a[N - 1]
```

所以相当于遍历了 a[N] 中的所有不同的三个元素，我们可以使用 \\(C_{N}^{3}\\) 来进行计算。算下来 if 语句的执行次数就是：

$$\frac{N(N-1)(N-2)}{6}=\frac{N^{3}}{6}-\frac{N^{2}}{2}+\frac{N}{3}$$

> 我们用 \~f(N) 表示所有随着 N 的增大除以 f(N) 的结果趋近于 1 的函数。用 g(N)\~f(N) 表示 g(N)/f(N) 随着 N 的增大趋近于 1 。

我们就可以使用 \~\\(\frac{N^{3}}{6}\\) 来表示 ThreeSum 中的 if 语句的执行次数。根据前文所述的所述的数学模型有 \\(T(N)\\)~\\(a{N^{3}}\\) ，其中常数 a 取决于计算机的型号。

### 成本模型

使用成本模型来评估算法的性质，定义了我们所研究算法中的基本操作。比如研究 3-sum 问题，我们记录数组的访问次数。

**得到运行时间的数学模型：**
- 确定输入模型，定义问题规模；
- 识别内循环；
- 根据内循环中的操作确定成本模型；
- 对于给定输入，判断这些操作的执行频率。

| 算法 | 运行时间的增长数量级 |
| :------------- | :------------- |
| ThreeSum | \\(NlogN\\) |
| ThreeSumFast | \\(N^{2}logN\\) |

ThreeSumFast 使用了 `Arrays.sort()` 的归并排序和 `BinarySearch.indexOf()` 的二分查找（对整型数组进行处理）

- ThreeSumFast 源码
```java
public class ThreeSumFast {
    public static int count(int[] a) {
        Arrays.sort(a);
        int n = a.length;
        int cnt = 0;
        for (int i = 0; i < n; i++)
            for (int j = i + 1; j < n; j++)
                if (BinarySearch.indexOf(a, -a[i] - a[j]) > j)
                    cnt++;
        return cnt;
    }
    public static void main(String[] args) {
        int[] a = StdIn.readInts();
        System.out.println(count(a));
    }
}
```

显而易见，ThreeSumFast 算法更快。在这里二分查找的增长数量级为线性级别 \\(N\\) ，对数组进行排序时用到的归并排序的增长数量级为线性对数级别 \\(NlogN\\) 。

**大致介绍一下常见增长数量级的分类：**
- 常数级别：运行时间不依赖于 \\(N\\) ；
- 对数级别：仅比常数时间的程序稍慢，典型的例子就是 \\(二分查找\\)；
- 线性级别：单个 for 循环是最常见的，运行时间和 \\(N\\) 成正比；
- 线性对数级别：增长数量级为 \\(NlogN\\) ，对数的底数和增长数量级无关，典型的例子是 Merge.sort()（算法 2.4）和 Quick.sort()（算法 2.5）；
- 平方级别：一般为两个 for 循环，例子有 `Selection.sort()`（算法 2.1）和 `Insertion.sort()`（算法 2.2）；
- 立方级别：一般有三个 for 循环，ThreeSum 就是一个例子；
- 指数级别：一般描述为 \\(b^{N}\\) ，这里的 b > 1 且为常数。

## 倍率实验

## 内存

- 原始数据类型的常见内存、需求

| 类型    | 字节 |
| :------------- | :------------- |
| boolean | 1 |
| byte    | 1 |
| char    | 2 |
| int     | 4 |
| float   | 4 |
| long    | 8 |
| double  | 8 |

### 链表

- 一个 Node 对象需要使用 40 字节（其中对象开销 16 字节，指向 Item 和 Node 对象的引用各需 8 字节，另外还有 8 字节的额外开销）

- Integer 对象需要使用 24 字节（16 字节的对象开销，4 字节保存 int 值以及 4 个填充字节）

一个含有 N 的整数的基于链表的栈需要使用（32+64N）字节（16 字节对象开销，引用类型实例变量 8 字节，int 型实例变量 4 字节，4 个填充字节，每个元素包含一个 Node 对象的 40 字节和一个 Integer 对象的 24 字节）

### 数组

一个原始数据类型的数组一般需要 24 字节的头信息（16 字节的对象开销，4 字节用于保存数组长度以及 4 填充字节）。

例如：一个含有 N 个 int 值的数组对象需要使用（24+4N）字节（会被填充为 8 的倍数），如果是 double 则需要使用（24+8N）字节。

### 字符串对象

4 个实例变量（一个 8 字节的指向字符数组的引用和三个 int 值）

3 个 int 值：
- 字符数组中的偏移量；
- 计数器（字符串的长度）；
- 散列值。

所以每个 String 对象会使用 40 字节（16 字节表示对象，三个 4 字节 int 实例变量，8 字节数组引用和 4 个填充字节），这是除字符数组之外字符串所需要的内存空间，字符所需的内存需要另记，因为 String 的 char 数组是在多个字符串之间共享的。（String 对象不可变，多个对象含有同一个 `value[]` 数组时可以节省内存。

### 字符串的值和子字符串

子字符串重用了 `value[]` ，只会使用 40 字节的内存。可以说，一个子字符串所需的额外内存是一个常数，构造一个子字符串的时间也是常数。这样做可以避免子字符串复制字符使用线性的时间和空间。

当程序调用一个方法时，系统会从栈中为方法分配所需要的内存用于保存局部变量；
方法返回时，它所占用的内存也被返回给了系统栈。

所以在递归程序中创建数组或是其他大型对象是很危险的！

***关注运行成本！***

## 补充

- \\(f(N)\\) ~ \\(g(N)\\) 的正式定义为 \\(\lim_{N\to\infty}\frac{f(N)}{g(N)} = 1\\)

## 习题

见 [习题 1-4]({{ site.baseurl }}{% link docs/ex_1/pa_4.md %})

---
