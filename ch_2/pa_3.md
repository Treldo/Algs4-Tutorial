**特点**：
- 原地排序（只需要一个很小的辅助栈），所需时间与 $NlgN$ 成正比；
- 内循环短小。

**缺点**：
- 在实现时需要避免低性能，否则只有平方级别。

## 基本算法

与归并排序相同，快速排序也是一种分治的排序算法；与归并排序不同，快速排序的思路是将子数组变有序，从而得到整个有序数列。

它利用切分元素将数组划分成两个部分，递归调用发生在处理整个数组之后，切分的位置取决于数组的内容。

<br />

<div style="text-align: center;">

![quicksort-overview](../_media/2.3/quicksort-overview.jpeg ':size=400')

</div>

🔜**快速排序**

```java
public class Quick {
    public static void sort(Comparable[] a) {
        StdRandom.shuffle(a);          // Eliminate dependence on input.
        sort(a, 0, a.length - 1);
    }
    private static void sort(Comparable[] a, int lo, int hi) {
        if (hi <= lo) return;
        int j = partition(a, lo, hi);
        sort(a, lo, j - 1);            // Sort left part a[lo .. j-1].
        sort(a, j + 1, hi);            // Sort right part a[j+1 .. hi].
    }
}
```

<br />

**快速排序概览**

<div style="text-align: center;">

![quick-sort](../_media/2.3/quick-sort.jpeg ':size=400')

</div>


> **数组满足：**
>
> - 对于某个 j ，a[j] 的位置已经确定；
> - a[lo] 到 a[j-1] 中元素不大于 a[i]；
> - a[j+1] 到 a[hi] 中元素不小于 a[j]。

其中的 a[lo] 就是我们随机取的 **切分元素** ，我们就是通过递归地调用切分来排序的。

**快速排序的切分**

```java
private static int partition(Comparable[] a, int lo, int hi) {
    int i = lo, j = hi+1;            // left and right scan indices
    Comparable v = a[lo];            // partitioning item
    while (true) {
        while (less(a[++i], v)) if (i == hi) break;
        while (less(v, a[--j])) if (j == lo) break;
        if (i >= j) break;
        exch(a, i, j);
    }
    exch(a, lo, j);
    return j;
}
```

**切分轨迹**

<div style="text-align: center;">

![partitioning-trace](../_media/2.3/partitioning-trace.jpeg ':size=400')

</div>

> **注意事项**
> - 原地切分，避免使用辅助数组；
> - 扫描指针不要越界；
> - 保持随机性，使切分元素是随机的；
> - 保证循环可以终止；
> - 处理切分元素有重复的情况；
> - 保证递归可以终止。

## 性能特点

- 归并排序和希尔排序一般都要比快速排序慢，是因为它们在内循环中还要移动数据；
- 快速排序的比较次数很少。

<!-- tabs:start -->

#### **命题**

将长度为 $N$ 的无重复数组排序，快速排序平均需要 ~ $2NlnN$ 次比较以及 $1/6$ 的交换。

#### **证明**

设 $C_N$ 为对于 $N$ 个元素的平均比较次数，由递归得到归纳关系：

<div style="text-align: center;">

$$C_N = N+1 + (C_0+C_1+...+C_{N-2}+C_{N-1})/N + (C_{N-1}+C_{N-2}+...+C_0)/N$$

</div>

其中第一项为切分成本，后两项是将左右子数组的排序的平均成本。两边同乘 $N$ 得到：

<div style="text-align: center;">

$$NC_N = N(N+1) + 2(C_0+C_1+...+C_{N-2}+C_{N-1})$$

</div>

两边减去 $N-1$ 时的相同等式：

<div style="text-align: center;">

$$NC_N - (N-1)C_{N-1} = 2N + 2C_{N-1}$$

</div>

两边同除以 $N(N+1)$ 得：

<div style="text-align: center;">

$$C_N/(N+1) =  C_{N-1}/N + 2(N+1)$$

</div>

归纳法推导得：

<div style="text-align: center;">

$C_N$ ~ $2(N+1)(1/3+1/4+...+1/(N+1))$

</div>

<br />

括号内的量是曲线 $2/x$ 下从 $3$ 到 $N$ 的离散近似面积加一，积分得到 $C_N$ ~ $2NlnN$ 。而 $2NlnN \approx 1.39NlgN$ ，也就是平均次数只比最好情况多 39% 。

<!-- tabs:end -->

<!-- tabs:start -->

#### **<b>命题</b>**

快速排序最多需要进行 $N_2/2$ 次比较，随机打乱可以预防这种情况的发生。

比较次数为：

<div style="text-align: center;">

$$N+(N-1)+(N-2)+...+2+1 = (N+1)N/2$$

</div>

<!-- tabs:end -->

## 算法改进

### 切换到插入排序

- 对于小数组，插入排序更快；
- 快速排序的 `sort()` 方法在小数组中也会调用自己。

将 `sort()` 方法中的语句：

```java
if (hi <= lo) return;
```

替换成：

```java
if (hi <= lo + M) { Insertion.sort(a, lo, hi); return; }
```

转换参数 M 的最佳值与系统相关，5 ~ 15 之间的任意值在大多情况下都能令人满意。

### 三取样切分

使用子数组一小部分元素的中位数作为切分元素，这样得到的切分效果更好，代价是需要计算中位数（创建一个三取样的带返回值方法，与 lo 元素进行交换）。

**切分轨迹**

<div style="text-align: center;">

![quicksort-of-3](../_media/2.3/quicksort-of-3.jpeg ':size=300')

</div>

**关于哨兵（去掉边界测试）**：

- 这个 while 是为了得到一个比 v 小的元素 a[j] ，而 v = a[lo] ，若 `less(v, a[--j])` 总为 true ，循环结束后，此时的 j 也就与 lo 相等，否则 j 始终大于 lo ，可以直接去掉数组左边界的测试；
```java
while (less(v, a[--j])) if (j == lo) break;
```
- 这个 while 是为了得到一个比 v 大的元素 a[i] ，若想要去掉数组的右边界测试，可以将数组中的最大元素置于 a[length - 1] 。
```java
while (less(a[++i], v)) if (i == hi) break;
```

### 熵最优的排序

在实际应用中可能会出现数据重复次数很多的情况，这就具有很大的改进潜力，我们可以将当前实现的线性对数级的性能提高到线性级别。一个简单的思路是将数组切分为三个部分。

```java
public class Quick3way {
    public static void sort(Comparable[] a) {
        StdRandom.shuffle(a);          // Eliminate dependence on input.
        sort(a, 0, a.length - 1);
    }
    private static void sort(Comparable[] a, int lo, int hi) {
        if (hi <= lo) return;
        int lt = lo, i = lo+1, gt = hi;
        Comparable v = a[lo];
        while (i <= gt) {
            int cmp = a[i].compareTo(v);
            if (cmp < 0) exch(a, lt++, i++);
            else if (cmp > 0) exch(a, i, gt--);
            else i++;
        }  // Now a[lo..lt-1] < v = a[lt..gt] < a[gt+1..hi].
        sort(a, lo, lt - 1);
        sort(a, gt + 1, hi);
    }
}
```

**三种情况**

- a[i] < v，将 a[lt] 和 a[i] 进行交换，将 lt 和 i 加一；
- a[i] > v，将 a[gt] 和 a[i] 进行交换，将 gt 减一；
- a[i] = v，将 i 加一。

> **三向切分的快速排序主要是用在重复数据量比较大的情况，**比如对按男女进行排序。对于这种只有若干主键的随机数组，归并排序的时间复杂度是线性对数的，而三向切分的快速排序则是线性的。
> 
> **而对于主键几乎不重复的随机序列，三向切分的快速排序的性能只会是线性对数级别，**在这种情况下可以使用三取样四取样切分等方法来达到更好的排序效果。

**切分轨迹**

<div style="text-align: center;">

![3-way-partitioning-trace](../_media/2.3/3-way-partitioning-trace.jpeg ':size=300')

</div>

**可视轨迹**

<div style="text-align: center;">

![visual-trace-of-quick-sort-3-way](../_media/2.3/visual-trace-of-quick-sort-3-way.jpeg ':size=400')

</div>

<br />

设 $P(i)$ 为随机抽取一个元素时第 $i$ 个主键值出现的概率，那么所有主键的 **信息熵（香农信息量）**可以定义为：

$$H(P) = \sum_{i=1}^n P(i)log_2{\frac{1}{P(i)}} = -\sum_{i=1}^n P(i)log_2{P(i)}$$

储存空间的储存因子 $F(P_i)$：

$$F(P_i) = log_2{(\frac{1}{P_i})}$$

关于 *信息熵（香农信息量）* 的内容可以参考：

- [【维基百科】熵（信息论）](https://zh.wikipedia.org/wiki/熵_(信息论))；
- [【知乎问答】信息熵](https://www.zhihu.com/question/30828247/answer/160647576) ；
- [【遥行的博客】信息熵、交叉熵和相对熵](https://charlesliuyx.github.io/2017/09/11/什么是信息熵、交叉熵和相对熵/) 。

?> 给定任意一个待排序的数组，通过统计每个主键出现的频率就可以计算出它包含的信息量。可以通过这个信息量得出三向切分的快速排序所需要的比较次数的上下界。

<!-- tabs:start -->

#### **<b>命题</b>**

不存在任何基于比较的算法能够保证在 $NH - N$ 次比较之内将 $N$ 个元素排序，其中 $H$ 为主键值出现频率定义的 *香农信息量* 。

<!-- tabs:end -->

<!-- tabs:start -->

#### **<b>命题</b>**

对于大小为 $N$ 的数组，三向切分的快速排序需要 ~ $(2ln2)NH$ 次比较。

<!-- tabs:end -->

<br />

**三向切分的最坏情况是所有主键均不相同，**此时有 $H = lgN$（所有主键的概率均为 $1/N$），当存在重复主键时，性能就会比归并排序好得多。三向切分是 信息量最优 的，即对任意分布的输入，最优的基于比较的算法平均所需要的比较次数和三向切分的快速排序平均所需的比较次数相互处于常数因子范围内。

**三向切分的快速排序的运行时间和输入的信息量的 $N$ 倍是成正比的。**对于包含大量重复元素的数组，它将排序时间从线性对数级别降低到了线性级别。三向切分的快速排序对重复元素的适应性使之成为了排序库函数的最佳算法选择 —— 需要将包含大量重复元素的数组排序的用例很常见。

## 补充

- 随机打乱能够防止出现最坏情况并使运行时间可以预计；
- 重复元素会直接影响到实际应用中的性能。

## 练习

见 [练习 2-3]() 。