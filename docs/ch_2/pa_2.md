---
layout: default
title: 2.2&ensp;归并排序
nav_order: 2
parent: 第二章&emsp;排序
---

## 2.2&ensp;归并排序
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

归并排序最吸引人的性质就是能够保证长度为 \\(N\\) 的数组排序所需时间和 \\(NlogN\\) 成正比；缺点是所需的额外空间和 \\(N\\) 成正比。

## 原地归并

将数组拆分成两个部分。操作完成后，左半部分小于等于右半部分。

```java
public static void merge(Compareable[] a, int lo, int mid, int hi) {
    int i = lo, j = mid + 1;

    for (int k = lo; k <= hi; k++)
        // 这是 Merge 类的一个方法，aux[] 在方法外部定义
        aux[k] = a[k];
    for (int k = lo; k <= hi; k++) {
        if (i > mid) a[k] = aux[j++];
        else if (j > hi) a[k] = aux[i++];
        else if (less(aux[j], aux[i])) a[k] = aux[j++];
        else a[k] = aux[i++];
    }
}
```

## 自顶向下的归并排序

实现了递归归并，是应用高效算法设计中 *分治思想* 的最典型的一个例子。

```java
public class Merge {
    private static Compareable[] aux;

    public static void sort(Compareable[] a) {
        aux = new Compareable[a.length];
        sort(a, 0, a.length - 1);
    }

    private static void sort(Compareable[] a, int lo, int hi) {
        if (hi <= lo) return;
        int mid = lo + (hi - lo) / 2;
        sort(a, lo, mid);
        sort(a, mid + 1, hi);
        merge(a, lo, mid, hi);
    }

    public static void merge(Compareable[] a, int lo, int mid, int hi) { }
}
```

每个小部分通过递归调用将它们单独排序。

<br />

---

**命题**：对于长度为 \\(N\\) 的任意数组，自顶向下的归并排序需要 \\(\frac{N}{2}lgN\\) 至 \\(NlgN\\) 次比较。

**证明**：用 \\(C(N)\\) 表示长度为 \\(N\\) 的数组排序时所需要的比较次数，有 \\(C(0) = C(1) = 0\\) 。

比较次数上限：

\$\$C(N) \leq C(N/2) + C(N/2) + N\$\$

*前两项为左右两边排序所用的比较次数，第三项为归并所用的比较次数。*

比较次数下限：

\$\$C(N) \geq C(N/2) + C(N/2) + N/2\$\$

当 \\(N = 2^n\\) 时， \\(N/2 = 2^{n-1}\\) ，有：

\$\$C(2^n) = 2C(2^{n-1}) + 2^n\$\$

两边同时除以 \\(2^n\\) ：

\$\$C(2^n)/2^n = C(2^{n-1}) / 2^{n-1} + 1\$\$

替换：

\$\$C(2^n)/2^n = C(2^{n-2}) / 2^{n-2} + 1 + 1\$\$

重复 n-1 遍得：

\$\$C(2^n) / 2^n = C(2^0) / 2^0 + n\$\$

两边同时乘以 \\(2^n\\) 得：

\$\$C(N) = C(2^n) = n2^n = NlgN\$\$

---

<br />

*换一种方式理解：下面的树就有 \\(n\\) 层，对于 \\(0\\) 到 \\(n - 1\\) 之间的任意 \\(k\\) ，自顶向下第 \\(k\\) 层有 \\(2^k\\) 个子数组，每个数组的长度为 \\(2^{k-n}\\) ，归并最多需要 \\(2^{k-n}\\) 次比较。每层的比较次数就是 \\(2^k \times 2^{k-n} = 2^n\\) ，\\(n\\) 层总共为 \\(n2^n = NlgN\\) 。*

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.2/mergesort-tree-n-16.png %}" width="600" />
</div>

<br />

---

**命题**：对于长度为 \\(N\\) 的任意数组，自顶向下的归并排序最多需要访问数组 \\(6NlgN\\) 次。

**证明**：我们知道归并的总次数为 \\(lgN\\) ，而每次归并所需数组访问次数为 \\(6N\\) 次（ \\(2N\\) 次复制，\\(2N\\) 次移动元素回归，\\(2N\\) 次比较）。要知道每一次的复制、移动或是比较都需要双向访问，也就是访问数组两次。

---

<br />

归并排序的缺点是所使用的额外空间和 \\(N\\) 的大小成正比。

### 对小规模子数组使用插入排序

因为递归会使小规模问题中方法的调用过于频繁，所以我们可以对小规模的子数组采用插入排序的处理方法，这样做一般可以将归并排序的运行时间缩短 10% ~ 15% 。

### 测试数组是否已经有序

上面提到的 `merge()` 方法是将数组拆分成两个部分（左小右大），而对于左右两边都已经排好序的情况下，当 a[mid] < a[mid + 1] 时，实际上整个数组已经有序，我们就可以跳过这一步的归并操作。这样做并不会影响排序的递归调用，但对于任意有序的子数组，算法的运行时间就变为线性的了。

### 不将元素复制到辅助数组

我们可以先将输入数组排序到辅助数组，然后在递归调用的每个层次交换 🎭 输入数组与辅助数组的角色，这样就省去了迭代复制的操作。

## 自底向上的归并排序

思路就是先归并微型数组，再成对归并的到的子数组。

```java
public class MergeBU {
    private static Compareable[] aux;

    public static void merge(Compareable[] a, int lo, int mid, int hi) { }

    public static void sort(Compareable[] a) {   // 共进行 lgN 次两两归并
        int N = a.length;
        aux = new Compareable[N];   // 这里的 aux 数组在 merge 方法中会使用
        for (int sz = 1; sz < N; sz += sz)
            for (int lo = 0; lo < N - sz; lo += 2 * sz)
                // 归并两个大小为 sz 的数组，保证右索引不超过 N - 1
                merge(a, lo, lo + sz -1, Math.min(lo + 2 * sz - 1, N - 1));
    }
}
```

图片会更直观一点：

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.2/visual-trace-of-bottom-up-mergesort.png %}" width="200" />
</div>

<br />

**命题**：对于长度为 \\(N\\) 的任意数组，自底而上的归并排序需要 \\(1/2NlgN\\) 至 \\(NlgN\\) 次比较，最多访问数组 \\(6NlgN\\) 次。（每一遍会访问数组 \\(6N\\) 次，比较次数在 \\(N/2\\) 和 \\(N\\) 之间）

当数组长度为 2 的幂时，自顶向下和自底向上的归并排序所用的比较次数和数组访问次数刚好相同，只是顺序不同。

*对于自底而上的实现方式，它比较适合用链表组织的数据，这种方法只需要重新组织链表链接就能进行原地排序。*

## 排序算法的复杂度

**注意**：本章的排序算法都是基于主键的比较，局限于实现了 Compareable 接口的对象，忽略了访问数组的开销。

<br />

---

**命题**：没有任何基于比较的算法能够保证使用少于 \\(lg(N!)\\) ~ \\(NlgN\\) 次比较将长度为 \\(N\\) 的数组排序。

**证明**：对于 \\(N\\) 个不同的主键，有 \\(N!\\) 种不同的排列。这是一个 \\(N = 3\\) 时的比较树：

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.2/tree-n-3.png %}" width="300" />
</div>

<br />

<p>树中的结点要么是一片叶子 <img src="{{ site.baseurl }}{% link /assets/images/2.2/leaf.png %}" width="110" /> ，表示排序完成前的原输入顺序；要么是一个内部结点 <img src="{{ site.baseurl }}{% link /assets/images/2.2/in-node.png %}" width="24" /> ，表示比较操作。</p>

二叉树的一个基本组合学性质就是高度为 \\(h\\) 的树最多只可能有 \\(2^h\\) 个叶子结点，拥有 \\(2^h\\) 个结点的树是完美平衡的，或称为完全树。所以我们可以得到：

\$\$N! \leq 叶子结点的数量 \leq 2^h\$\$

\\(h\\) 的值就是最坏情况下的比较次数，两边同时取对数得到任意算法的比较次数至少为 \\(lgN!\\) 。

根据 [斯特林公式](https://zhuanlan.zhihu.com/p/56546743) 可得近似 \\(lgN!\\) ~ \\(NlgN\\) 。

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.2/stirling.jpg %}" width="300" />
</div>

---

<br />

**命题**：归并排序是一种渐进最优的基于比较的排序算法。

**本节中理论的局限性：**
- 归并排序的空间复杂度不是最优的；
- 在实践中不一定会遇到最坏情况；
- 除了比较，算法的其他操作（例如访问数组）也可能很重要；
- 不进行比较也能将某些数据排序。

## 补充

- 更好的解决方案：将 aux[] 变为 `sort()` 方法的局部变量，并将它作为参数传递给 `merge()` 方法。
- 所有元素相同时，归并排序的运行时间是线性的。

## 习题

见 [习题 2-2]({{ site.baseurl }}{% link docs/ex_2/pa_2.md %})

---
