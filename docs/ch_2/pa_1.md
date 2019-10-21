---
layout: default
title: 2.1&ensp;初级排序算法
nav_order: 1
parent: 第二章&emsp;排序
---

## 2.1&ensp;初级排序算法
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

在创建数据类型时，只要实现 Comparable 接口就能够保证用例代码可以将其排序。要做到这点就要实现 `compareTo()` 方法来定义目标类型的自然次序。

**排序类算法的模版：**

```java
public class Example {
    public static void sort(Comparable[] a) { }
    private static boolean less(Comparable v, Comparable w) {
        return v.compareTo(w) < 0;
    }
    private static void exch(Comparable[] a, int i, int j) {
        Comparable t = a[i]; a[i] = a[j]; a[j] = t;
    }
    private static void show(Comparable[] a) {
        for (int i = 0; i < a.length; i++)
            StdOut.print(a[i] + " ");
        StdOut.println();
    }
    public static boolean isSorted(Comparable[] a) {
        for (int i = 1; i < a.length; i++)
            if (less(a[i], a[i - 1])) return false;
        return true;
    }
    public static void main(String[] args) {
        String[] a = In.readStrings();
        sort(a);
        assert isSorted(a);
        show(a);
    }
}
```

Java 的习惯是在 `compareTo()` 方法被调用时返回一个负整数、零和一个正整数（一般是 -1 、0 和 1）。在两者无法进行比较或者其中一方为 null 时，方法会抛出一个异常。此外，`compareTo()` 必须实现一个 **全序关系** ：

- 自反性，对于所有 v ，v = v ；
- 反对称性，对于所有 v > w 都有 v < w ，且 v = w 时，w = v ；
- 传递性，对于所有 v 、w 和 x ，如果 v <= w 且 w <= x ，则有 v <= x 。

## 选择排序

*找到数组中最小的那个元素，将它和数组的第一个元素进行交换，如此往复...*

> 对于长度为 \\(N\\) 的数组来说，选择排序需要大约 \\(N^2/2\\) 次比较和 \\(N\\) 次交换
>
> 比较次数：\\((N-1)+(N-2)+...+2+1=\frac{N(N-1)}{2}\\) ~ \\(N^2/2\\)

**特点**

- *运行时间和输入无关：* 一个有序或主键全部相等的数组和一个元素随机排列的数组所用的排序时间几乎一样长；
- *数据移动最少：* 进行了 \\(N\\) 次交换，交换次数和数组大小是线性关系。

<br />

**算法 2.1&emsp;选择排序**

```java
public class Selection {
    public static void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 0; i < N; i++) {
            int min = i;
            for (int j = i + 1; j < N; j++)
                if (less(a[i], a[min])) min = j;
            exch(a, i, min);
        }
    }
    // less()、exch()、isSorted() 和 main() 方法见模版
}
```

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.1/selection.png %}" width="400" />
</div>

<br />

## 插入排序

和选择排序不同的是，插入排序的时间取决于输入中元素的初始顺序。在实际应用中对于某些类型的非随机数组很有效，对于有序数组，它的耗时是线性的。

> 对于随机排列长度为 \\(N\\) 且主键不重复的数组，平均需要进行 ~ \\(N^2/4\\) 次比较以及 ~ \\(N^2/4\\) 次交换。最坏情况下要进行 ~ \\(N^2/2\\) 次比较和 ~ \\(N^2/2\\) 次交换，最好情况下要 \\(N-1\\) 次比较和 \\(0\\) 次交换。

<br />

**算法 2.2&emsp;插入排序**

```java
public class Insertion {
    public static void sort(Comparable[] a) {
        int N = a.length;
        for (int i = 1; i < a.length; i++) {
            for (int j = i; j > 0 && less(a[j], a[j - 1]); j--)
                exch(a, j, j - 1);
        }
    }
    // less()、exch()、isSorted() 和 main() 方法见模版
}
```

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.1/insertion.png %}" width="400" />
</div>

<br />

**部分有序数组：**

- 数组中每个元素离它最终位置不远；
- 一个有序大数组接一个小数组；
- 数组中只有几个元素的位置不正确。

*插入排序对这样的数组很有效，而选择排序不然。当倒置的数量很少时，插入排序很可能比其他绝大部分算法都要快。*

> 插入排序需要的交换操作和数组中倒置的数量相同，需要的比较次数大于等于倒置的数量，小于等于倒置的数量加上数组的大小再减一。

想要提高插入排序的速度，可以在内循环中将较大的元素向右移动而不是交换两个元素（访问数组的次数会减半）。

## 排序算法的可视化

因为插入排序不会移动比被插入元素更小的元素，它所需要的比较次数平均只有选择排序的一半。

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.1/bars.png %}" width="240" />
</div>

<br />

## 比较两种排序算法

- 实现并调试它们；
- 分析它们的基本性质；
- 对它们的相对性能作出猜想；
- 用实验证明我们的猜想。

*对于随机排序的数组，两者的运行时间都是平方级别的。*

*\\(N^2\\) 前面的常数值取决于计算机中比较和交换元素的成本，而对于许多数据类型和一般的计算机，假设这些成本是接近的，可以得出猜想：*

> 对于随机排序的无重复主键的数组，插入排序和选择排序的运行时间是平方级别的，两者之比应该是一个较小的常数。

<br />

**比较两种排序算法**

```java
public class SortCompare {
    // 针对给定输入，为排序算法计时 ⌛️
    public static double time(String alg, Comparable[] a) {
        Stopwatch timer = new Stopwatch();
        if (alg.equals("Insertion")) Insertion.sort(a);
        if (alg.equals("Selection")) Selection.sort(a);
        if (alg.equals("Shell")) Shell.sort(a);
        if (alg.equals("Merge")) Merge.sort(a);
        if (alg.equals("Quick")) Quick.sort(a);
        if (alg.equals("Heap")) Heap.sort(a);
        return timer.elapsedTime();
    }
    public static double timeRandomInput(String alg, int N, int T) {
      // 使用算法 alg 将 T 个长度为 N 的数组排序
        double total = 0.0;
        Double[] a = new Double[N];
        // 生成随机数组并排序
        for (int t = 0; t < T; t++)
            a[i] = StdRandom.uniform();
        total += time(alg, a);
    }
    public static void main(String[] args) {
        String alg1 = args[0];
        String alg2 = args[1];
        int N = Integer.parseInt(args[2]);
        int T = Integer.parseInt(args[3]);
        double t1 = timeRandomInput(alg1, N, T);
        double t1 = timeRandomInput(alg2, N, T);
        StdOut.printf("For %d random Doubles\n    %s is", N, alg1);
        StdOut.printf(" %.1f times faster than %s\n", t2 / t1, alg2);
    }
}
```

**对于初级算法**

- 建立了一些基本规则；
- 展示了一些性能标准；
- 在某些情况下是很好的选择；
- 是更强大算法的基石。

## 希尔排序

希尔排序的思想是使数组中任意间隔为 h 的元素都是有序的。这样的数组被称为 h 有序数组，是由 h 个相互独立的有序数组编织在一起组成的。

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.1/h-sorted.png %}" width="400" />
</div>

<br />

*实现希尔排序的一种方法就是对于每个 h ，用插入排序将 h 个子数组独立地排序，排序之后的子数组都是部分有序的。*

**算法 2.3&emsp;希尔排序**

```java
public class Shell {
    public static void sort(Comparable[] a) {
        int N = a.length;
        int h = 1;
        while (h < N / 3) h = 3 * h + 1;
        while (h >= 1) {
            for (int i = h; i < N; i++) {
                for (int j = i; j >= h && less(a[j], a[j - h]); j -= h)
                    exch(a, j, j - h);
            }
            h = h / 3;
        }
    }
    // less()、exch()、isSorted() 和 main() 方法见模版
}
```

与选择排序和插入排序形成对比的是，希尔排序也可以用于大型数组，对任意排序的数组表现也很好。

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.1/shell.png %}" width="300" />
</div>

<br />

通过 SortCompare 可以看到，希尔排序比选择和插入排序要快得多，而且数组越大优势越明显。对于 *算法 2.3* ，目前只知道它的运行时间不到平方级别。

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.1/shell-bars.png %}" width="400" />
</div>

<br />

> 使用递增序列 1，4，13，40，121，364... 的希尔排序所需的比较次数不会超出 \\(N \\) 的若干倍乘以递归序列的长度。平均每个增幅带来的比较次数约为 \\(N^{1/5}\\) ，但只有在 \\(N\\) 很大的时候才明显。

如果需要解决一个排序问题而又没有系统排序函数可用（例如直接解除硬件或是运行与嵌入式系统中的代码），可以先用希尔排序，再考虑是否值得替换为更复杂的排序算法。


## 补充

- `less()` 和 `exch()` 这些函数使代码更容易理解，而且增强了代码的可移植性。只要将 `less()` 实现为 v < w 就可以给不支持 Comparable 接口的基本数据类型排序了。

## 习题

见 [习题 2-1]({{ site.baseurl }}{% link docs/ex_2/pa_1.md %})

---
