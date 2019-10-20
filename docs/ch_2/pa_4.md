---
layout: default
title: PA_4
nav_order: 4
parent: CH_2
---

## 2.4&ensp;优先队列
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

支持两种操作：*删除最大元素和插入元素* 。

优先队列的一些重要的应用场景包括模拟系统、任务调度以及数值计算等，*堆排序* 就是基于堆的优先队列的实现。

## API

为了保证灵活性使用了泛型，将实现了 Comparable 接口的数据的类型作为参数 Key ，使我们不必区别元素和元素的键。

**泛型优先队列的 API**

<div style="text-align:center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.4/api-generic-pq.png %}" width="500" />
</div>

<br />

如果我们要从 N 个输入中找出最大的 M 个样本，一种方法是先将输入排序，另一种方法是将每个新的输入和已知的 M 个最大元素作比较，这两种方法在输入很大的时候效率都不高，所以我们需要高效地实现 `insert()` 和 `delMin()` 方法。

<br />

**从 N 个输入中找到 M 个元素所需成本**

<table>
  <tr>
    <td rowspan="2">示例</td>
    <td colspan="2">增长的数量级</td>
  </tr>
  <tr>
    <td>时间</td>
    <td>空间</td>
  </tr>
  <tr>
    <td>排序算法的用例</td>
    <td>\(NlogN\)</td>
    <td>\(N\)</td>
  </tr>
  <tr>
    <td>调用初级实现的优先队列</td>
    <td>\(NM\)</td>
    <td>\(M\)</td>
  </tr>
  <tr>
    <td>调用基于堆实现的优先队列</td>
    <td>\(NlogM\)</td>
    <td>\(M\)</td>
  </tr>
</table>

**一个优先队列的用例**

```java
public class TopM {
    public static void main(String[] args) {
        int M = Integer.parseInt(args[0]);
        MinPQ<Transaction> pq = new MinPQ<Transaction>(M+1);
        while (StdIn.hasNextLine()) {
            pq.insert(new Transaction(StdIn.readLine()));
            if (pq.size() > M) pq.delMin();
        }
        Stack<Transaction> stack = new Stack<Transaction>();
        while (!pq.isEmpty()) stack.push(pq.delMin());
        for (Transaction t : stack) StdOut.println(t);
    }
}
```

这段代码读入多组交易数据，将最大的 M 组数据存到栈中，再打印出来。

## 初级实现

优先队列的实现大致分为 *数组实现（无序）* 、*数组实现（有序）* 和 *链表表示法* 三种，下面是各种实现在最坏情况下运行时间的增长数量级：

| 数据结构 | 插入元素 | 删除最大元素    |
| :-----: | :--------: | :--------: |
| 有序数组 | \\(N\\)    | \\(1\\)    |
| 无序数组 | \\(1\\)    | \\(N\\)    |
|    堆   | \\(logN\\) | \\(logN\\) |
| 理想情况 | \\(1\\)    | \\(1\\)    |

## 堆的定义

当一棵二叉树的每个结点都大于等于它的两个子结点时，它被称为 *堆有序* ，而根结点就是这棵树中的最大结点。

### 二叉堆表示法

二叉树中的每个元素需要三个指针来找到它的上下结点。如果是完全二叉树，只需要使用数组而不需要指针就可以表示，具体方法就是将二叉树的结点按照 *层级顺序* 放入数组中。下面 👇 就是一颗堆有序的完全二叉树。

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.4/hp-binary-tree.png %}" width="200" />
</div>

**定义**：*二叉堆* 是一组能够用堆有序的完全二叉树排序的元素，并在数组中按照层级储存（不使用数组的第一个位置）。

*在一个二叉堆中，位置为 \\(k\\) 的结点的父节点的位置为 \\(⎣k/2⎦\\) ，它的两个子结点的位置为 \\(2k\\) 和 \\(2k-1\\) 。我们可以计算数组的索引在树中上下移动：从 a[k] 向上一层就另 k 等于 k / 2 ，向下一层则令 k 等于 2k 或 2k+1 。下面是就一个基于数组的堆的表示：*

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.4/heap-representations.png %}" width="250" />
</div>

**命题**：一棵大小为 \\(N\\) 的完全二叉树的高度为 \\(⎣lgN⎦\\) 。

## 堆的算法

我们只通过两个分别是比较和交换的方法对堆元素进行访问。

```java
private boolean less(int i, int j) {
    return pq[i].compareTo(pq[j]) < 0;
}

private void exch(int i, int j) {
    Key t = pq[i];
    pq[i] = pq[j];
    pq[j] = t;
}
```

堆的操作会首先进行一些改动而打破堆的状态，我们需要遍历并按照要求将堆的状态恢复，这个过程叫做 *堆的有序化（reheapifying）*。

在 **有序化** 的过程中我们会遇到两种情况：

- 某个结点的优先级上升（或是在堆底加入某个新的元素）时，我们需要 *由上至下* 恢复堆的排序；
- 某个结点的优先级下降（例如，将根结点替换成一个较小元素）时，我们需要 *由下至上* 恢复堆的排序。

### 由下至上的堆有序化（上浮）

如果某个结点变得比它的父节点要大，我们就需要交换它们来保证堆有序。（位置为 \\(k\\) 的结点的父结点的位置是 \\(⎣k⎦\\) ）。

```java
private void swim(int k) {
    while (k > 1 && less(k / 2, k)) {
        exch(k / 2, k);
        k /= 2;
    }
}
```

下面 👇 就是由下至上的堆有序化示意图。

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.4/buttom-up(swim).png %}" width="250" />
</div>

### 由上至下的堆有序化（下沉）

位置为 \\(k\\) 的结点的子结点位置是 \\(2k\\) 和  \\(2k+1\\) 。下面 👇 就是由上至下的堆有序化示意图。

<div style="text-align: center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.4/top-down(sink).png %}" width="200" />
</div>

```java
private void sink(int k) {
    while (2 * k <= N) {
        int j = 2 * k;
        if (j < N && less(j, j + 1)) j++;
        if (!less(k, j)) break;
        exch(k, j);
        k = j;
    }
}
```

`sink()` 和 `swim()` 方法是高效实现优先队列 API 的基础。

- *插入元素* ：将新元素加到数组末尾，增加堆的大小并让它上浮到合适位置；
- *删除最大元素* ：从数组顶端删去最大的元素并将数组最后一个元素放到顶端，减小堆的大小并让它下沉到合适位置。

下面 👇 就是堆的这两种操作的示意图。

<div style="text-align:center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.4/heap-operations.png %}" width="400" />
</div>

**基于堆的优先队列**

```java
public class MaxPQ<Key extends Comparable<Key>> {
    private Key[] pq;
    private int N = 0;

    public MaxPQ(int maxN) {
        pq = (Key[]) new Comparable[maxN + 1];
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public int size() {
        return N;
    }

    public void insert(Key v) {
        pq[++N] = v;
        swim(N);
    }

    public Key delMax() {
        Key max = pq[1];
        exch(1, N--);
        pq[N + 1] = null;
        sink(1);
        return max;
    }

    // 见前面的代码
    private boolean less(int i, int j);
    private void exch(int i, int j);
    private void swim(int k);
    private void sink(int k);
}
```

具体的思路看代码就可以明白，在这里省略了动态调整数组的代码，可以参考前面栈的部分 [调整数组大小](../../ch_1/pa_3/#调整数组的大小) 的内容。

**命题**：对于一个含有 \\(N\\) 个元素的基于堆的优先队列，*插入元素* 操作只需不超过 \\((lgN+1)\\) 次比较，*删除最大元素* 的操作需要不超过 \\(2lgN\\) 次比较（两次比较一次用来找出最大的子结点，一次用来确定子结点是否需要上浮）。

### 多叉堆

<div>
  <img style="float:right; width:30%;" src="{{ site.baseurl }}{% link /assets/images/2.4/priority-queue-op-heap.png %}" />
  <p>三叉堆的元素中，位置 \(k\) 的结点大于等于位于 \(3k-1\) 、\(3k\) 和 \(3k+1\) 的结点，小于等于位于 \(⎣(k+1)/3⎦\) 的结点。而对于给定的 \(d\) ，我们需要在树高 \((log_d{N})\) 和在每个结点的 \(d\) 个子结点找到最大者的代价之间找到折中，这取决于实现的细节以及不同操作的预期相对频繁程度。</p>
  <p>堆上的优先队列操作如右图所示。</p>
</div>

### 调整数组大小

我们可以在 `insert()` 方法中添加一段将数组长度加倍的代码，在 `delMax()` 方法中添加一段将数组长度减半的代码。就像前面栈的部分 [调整数组大小](../../ch_1/pa_3/#调整数组的大小) 里那样。对数时间复杂度的上限只是针对一般性的队列长度 \\(N\\) 而言。

### 元素的不可变性

优先队列储存了用例创建的对象，但同时假设用例不会改变它们（否则很可能打破堆的有序性）。我们一般不会把这个假设转化为强制条件，因为增加代码的复杂性会降低性能。

### 索引优先队列

**关联索引的泛型优先队列的 API**

<div style="text-align:center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.4/api-generic-pq-associated-indices.png %}" width="500" />
</div>

<br />

允许用例引用已经进入优先队列中的元素是有必要的。一种简单的方法是为每个元素增加索引，所以可能会用到多个（平行）数组来储存这些元素的信息，代码可以参考 <a href="https://algs4.cs.princeton.edu/24pq/IndexMinPQ.java.html" target="_blank">IndexMinPQ</a> 。

当堆发生变化时，我么同样使用会使用下沉或上浮操作来回复堆的有序性。而在元素与索引关联之后我们便可以利用索引查找元素，从而我们能够加入一个 `delete()` 操作。

**命题**：在一个大小为 \\(N\\) 的索引优先队列中，插入一个元素（insert）、改变优先级（change）、删除（delete）和删除最小元素（remove the minimum）操作所需的比较次数和 \\(logN\\) 成正比。

**含有 N 个元素的基于堆的索引优先队列所有操作在最坏情况下的成本**

| 操作   | 比较次数的增长数量级 |
| :----------: | :--------: |
| `insert()`   | \\(logN\\) |
| `change()`   | \\(logN\\) |
| `contains()` | \\(1\\)    |
| `delete()`   | \\(logN\\) |
| `min()`      | \\(1\\)    |
| `minIndex()` | \\(1\\)    |
| `delMin()`   | \\(logN\\) |

这段讨论针对找出最小元素的队列 <a href="https://algs4.cs.princeton.edu/24pq/IndexMinPQ.java.html" target="_blank">IndexMinPQ.java</a> ；找出最大元素的版本在这里 <a href="https://algs4.cs.princeton.edu/24pq/IndexMaxPQ.java.html" target="_blank">IndexMaxPQ.java</a> 。_

📒 **看到代码中使用到了三个（平行）数组，这里大致说一下三个数组的作用：**

- keys 数组用来保存传进来的类型为 Key 的对象，它一般只会在插入和删除时被改变；
- pq 是优先队列，用来保存对象在 keys 数组中的索引；
- qp 是 pq 的索引数组，与 pq 数组刚好“互补”，有 `qp[pq[i]] = pq[qp[i]] = i` ，用来存放优先队列的索引。

❗️**这里要清晰一点**：qp 数组的作用就是为了保存优先队列的索引，而它的索引同 keys 数组对应，所以可以通过 keys 数组中对象的索引得到其对应在优先队列 pq 中的索引。我在这里截取其中两段代码作一下解释：

```java
// 插入一个索引为 i 的新元素
public void insert(int i, Key key) {
    if (contains(i))         // 判断是否已经存在了索引为 i 的元素
        throw new IllegalArgumentException("index is already in the priority queue");
    n++;                     // 插入了一个新的元素，队列长度加一
    qp[i] = n;               // i 是一个新的索引值，新元素还没有和索引关联。向优先队列中加入新的关联内容
    pq[n] = i;               // 将新的索引值加入“堆”中，这行代码与上面一行颠倒没有影响
    keys[i] = key;           // 索引值为 i 的元素对象为 key
    swim(n);                 // 新元素在“堆”底加入优先队列中，需要进行上浮操作，保证堆有序
}

// 删除队列中的最大元素
public int delMax() {
    if (n == 0)              // 如果索引优先队列之中没有元素
        throw new NoSuchElementException("Priority queue underflow");
    int max = pq[1];         // 表示最大元素的索引值。每次有序处理完成后，根结点对应的元素为最大
    exch(1, n--);            // 将“堆”中根结点与最后一片“叶子”进行交换，并将队列长度减一
    sink(1);                 // 刚刚替换上来的“叶子”可能不是最大元素，需要进行吓成操作，保证堆有序
    assert pq[n+1] == max;   // 断言当前 n+1 位置上的索引值为 max，否则抛出 AssertionError
    qp[max] = -1;            // 设置此索引值为 -1 ，撤销索引的对应关系，表示没有此索引对应的元素
    keys[max] = null;        // 设置此索引对应的 keys 数组中对象为空，告知系统进行回收
    pq[n+1] = -1;            // 不是必要的，如果像 insert() 方法中的先令 qp[i] = n ，
                             // 再令 pq[n] = i 的顺序，是不会访问到 pq 中被删除元素索引的
    return max;              // 返回被删除元素的索引
}
```

🔗 关于优先队列和索引优先队列，还可以参考这两篇文章：
{: .fs-4 }

<li><a href="https://www.cnblogs.com/nullzx/p/6624731.html" target="_blank">索引优先队列的工作原理与简易实现</a> ；</li>
<li><a href="https://zhuanlan.zhihu.com/p/35381524" target="_blank">算法优化小助手：索引优先队列</a> 。</li>

### 索引优先队列用例

下面的例子解决了 *多向归并* 问题：将多个有序的输入归并成一个有序的输出流。

输入可能来自多种科学仪器 🔬 的输出（按时间排序）、多个音乐 🎵 或电影网站 🎬 的信息列表（按名称或艺术家名字排序）、商业交易（按账号或时间排序）或者其他。如果有足够的空间，你可以把它们简单地读入一个数组并排序，但如果使用了优先队列，无论输入有多么长，你都可以把它们全部读入并排序。

**使用优先队列的多向归并**

```java
public class Multiway {
    public static void merge(In[] streams) {
        int N = streams.length;
        IndexMinPQ<String> pq = new IndexMinPQ<String>(N);

        for (int i = 0; i < N; i++)
            if (!streams[i].isEmpty())
                pq.insert(i, streams[i].readString());

        while (!pq.isEmpty()) {
            StdOut.println(pq.min());
            int i = pq.delMin();

            if (!streams[i].isEmpty())
                pq.insert(i, streams[i].readString());
        }
    }

    public static void main(String[] args) {
        int N = args.length;
        In[] streams = new In[N];
        for (int i = 0; i < N; i++)
            streams[i] = new In(args[i]);
        merge(streams);
    }
}
```

话不多说，思路全在代码中... 😁

## 堆排序

**堆排序可以分为两个阶段：**

- *堆的构造* ：将原始数组重新组织安排进一个堆中；
- *下沉排序* ：按递减顺序取出所有元素并得到排序结果。

### 堆的构造

- 从左至右遍历数组，用 `swim()` 保证指针左侧的所有元素已经是一棵有序的完全树（类似于插入元素）；
- 从右至左用 `sink()` 函数构造子堆，只需要扫描一半的元素（\\(N/2~1\\)），因为可以跳过大小为 1 的子堆，从大小为 3 的子堆开始进行下沉操作。

这样的到的结果是构造一个“堆有序”的二叉堆，还没有完成排序过程。

**命题**：用下沉操作由 \\(N\\) 个元素构造堆只需要少于 \\(2N\\) 次比较以及少于 \\(N\\) 次交换。

**堆排序**

```java
public static void sort(Comparable[] a) {
    int N = a.length;
    for (k = N/2; k >= 1; k--)
        sink(a, k, N);
    while (N > 1) {
        exch(a, 1, N--);
        sink(a, 1, N);
    }
}
```

这里的 `sink()` 方法已经改写，具体可以参考 <a href="https://algs4.cs.princeton.edu/24pq/Heap.java.html" target="_blank">Heap.java</a> 。

**堆排序的轨迹**

<div style="text-align:center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.4/heapsort-trace.png %}" width="250" />
</div>

<br />

**堆排序：堆的构造（左）和下沉排序（右）**

<div style="text-align:center;">
  <img src="{{ site.baseurl }}{% link /assets/images/2.4/heapsort.png %}" width="450" />
</div>

### 下沉排序


<img style="float:right; width:25%;" src="{{ site.baseurl }}{% link /assets/images/2.4/visual-trace-heapsort.png %}" />

堆排序的主要工作就在第二步，也就是上面代码中的 while 循环。它主要是将堆中的最大元素删除并缩短数组长度，循环结束数组有序，达到排序效果，这就类似于选择排序。

堆排序的可视轨迹见右图 ➡️

---

**命题**：将 \\(N\\) 个元素排序，堆排序只需要少于（\\(2NlgN+2N\\)）次比较（以及一半次数地交换）。

**证明**：\\(2N\\) 项来自于堆的构造，\\(2NlgN\\) 项来自于每次下沉最大可能需要 \\(2lgN\\) 次比较。

---

*堆排序* 的发明人是 **J.W.J.Williams** ，并由 **R.W.Floyd** 在 1964 年改进。

### 先下沉后上浮

因为大多数重新插入堆的元素会直接加入到堆底，**Floyd** 发现我们可以免去检查元素是否到达正确位置来节省时间。也就是直接提升较大的子结点直至到达堆底，然后再使元素上浮 🔝 到正确位置。

这样几乎可以将比较的次数减半，接近了堆司机数组进行归并排序所需要的比较次数。不过需要额外的空间，在实际应用中只会当比较操作代价高时较高时才会使用（例如对字符串或其他键值较长类型元素进行排序时）。

堆排序是我们所知唯一能够同时最优地利用时间和空间的方法 —— 在最坏情况下也能保证使用 ~ \\(2NlgN\\) 次比较和额外空间。当空间非常紧张的时候（例如嵌入式系统或低成本的移动设备中）很流行，甚至机器码也只需要几行就能够实现较好的性能。

*而现代系统的许多应用很少使用它，因为它无法利用缓存。数组元素很少和邻其他元素进行比较，因此缓存未命中的次数要远远高于大多数比较都在相邻元素见进行的算法，如快速排序、归并排序，甚至是希尔排序。*

用对实现的优先队列能够在 *插入操作* 和 *删除最大元素操作* 混合的动态场景中保证对数级别的运行时间。

## 补充

- 使用泛型是为了在用例中将方法返回值转换为某种具体的类型（一般要尽量避免在用例中进行类型转换）；
- a[0] 的值可以用作哨兵（作为 a[1] 的父结点），在某些堆的应用中很有用；
- 构造堆时，使用代码中的 `sink()` 方式要比逐个向堆中添加元素快上 20% ，所需代码更少（没有用到 `swim()` 函数）；
- 继承 `Comparable<Key>` 是为了使用其中的 `compareTo()` 方法。

## 练习

见 [习题 2-4]({{ site.baseurl }}{% link docs/ex_2/pa_4.md %})

---
