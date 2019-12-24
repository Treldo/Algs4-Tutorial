## 将各种数据排序

利用 Java 回调机制将任意实现了 Comparable 接口的数据类型排序。

### 交易事务

- 将交易记录按照日期排序的 `compareTo()` 方法。
```java
public int compareTo(Transaction that) {
    return this.when.compareTo(that.when);
}
```

### 指针排序

除了原始数据类型之外，我们操作的总是数据的引用（指针），而非数据本身。

数组保存的是待排序对象的引用，而非对象本身。对于多个引用数组，我们可以将同一组数据的不同部分按照多种方式排序（可能会用到多键）。

### 不可变的键

在 Java 中可以使用不可变的数据类型作为键来避免键值被修改的情况，例如 String、Integer、Double 和 File 都是不可变的。

### 廉价的交换

对于几乎任意大小的元素，使用引用使得在一般情况下交换的成本和比较的成本几乎相同（代价是需要额外空间储存这些引用），如果键值很长，交换的成本甚至会低于比较的成本。

研究将数字排序的算法性能的一种方法就是观察所需要的比较和交换总数因为这里隐式地假设了比较和交换的成本是相同的。这适用于 Java 中的许多应用，因为我们都是在家过引用排序。

### 多种排序方法

Comparator 接口允许我们为任意数据类型定义多种排序方法，用 Comparator 接口代替 Comparable 接口能够更好地将数据类型的定义和两个该类型的对象应该如何比较的定义区分开来。比如 Java 的 String 类型就含有很多比较器。

### 多键数组

为了能够利用多个属性进行排序，用 Comparator 接口正好合适。

下面的例子中，为了避免每次调用 `sort()` 方法都创建一个新的 Comparator 对象，我们可以使用静态方法也就是 static 关键字进行修饰。

```java
public static void sort(Object[] a, Comparator c) {
    int N = a.length;
    for (int i = 1; i < N; i++)
      for (int j = i; j > 0 && less(Comparator, a[j], a[j - 1]; j--))
          exch(a, j, j - 1);
}

private static boolean less(Comparator c, Object v, Object w) {
    return c.compare(v, w) < 0;
}

private static void exch(Object[] a, int i, int j) {
    Object t = a[i];
    a[i] = a[j];
    a[j] = t;
}
```

### 使用比较器实现优先队列

- 导入 `java.util.Comparator` ；
- 为 MaxPQ 添加一个实例变量 compatator 以及一个构造函数，该构造函数接受一个比较器作为参数并用它将 cpmparator 初始化；
- 在 `less()` 中检查 comparator 属性是否为 null（如果不是的话就用它进行比较）。

下面的修改可以使用 Transaction 的多种字段构造不同的优先队列，分别按时间、地点、账号排序。

```java
import java.util.Comparator;

public class Transaction {
    private final String who;
    private final Date when;
    private final double amount;

    public static class WhoOrder implements Comparator<Transaction> {
        public int compare(Transaction v, Transaction w) {
            return v.who.compareTo(w.who);
        }
    }

    public static class WhenOrder implements Comparator<Transaction> {
        public int compare(Transaction v, Transaction w) {
            return v.when.compareTo(w.when);
        }
    }

    public static class HowMuchOrder implements Comparator<Transaction> {
        public int compare(Transaction v, Transaction w) {
            if (v.amount < w.amount) return -1;
            if (v.amount > w.amount) return +1;
            return 0;
        }
    }
}
```

### 稳定性

如果一个排序算法中能够保留数组中重复元素的相对位置则可以称为是 *稳定的* 。

下面 👇就是两种对比：

<div style="text-align:center;">

![stability-sort-second-way](../_media/2.5/stability-sort-second-way.png ':size=400')

</div>

我们学过的插入排序和归并排序是稳定的，但很多不是（选择排序、希尔排序、快速排序和堆排序）。我们可以实现一段能够将任意排序算法变得稳定的封装代码，本节习题 2.5.18 中有涉及。

## 我们应该使用哪种排序算法

下面 👇是各种排序算法的性能特点：
<div style="text-align:center;">

![performance-of-sorting-algs](../_media/2.5/performance-of-sorting-algs.jpeg ':size=400')

</div>

**快速排序是最快的通用排序算法**：它之所以快是因为内循环中的指令很少（能够利用缓存，顺序访问数据），运行时间的增长数量级为 ~ $cNlgN$ ，而这里的 $c$ 要比其他线性对数级别的算法的相应常数都要小。在使用三向切分之后，快速排序对于实际应用中可能出现的某些分布的输入变成线性级别的了，而其他排序算法仍需要线性对数时间。

大多数实际情况中，快速排序是最佳的选择。我们会遇到其他的种种例外（例如在稳定性很重要而空间又不是问题的情况下，归并排序可能是最好的），我们还会进行各种改进方案。在运行时间至关重要的的任何排序应用中，我们要认真地考虑使用快速排序。

### 将原始类型数据排序

一些性能优先的应用的重点可能是将数字排序，因此更合理的做法是跳过引用直接将原始数据类型的数据排序。

对于 double 类型的数组，我们可以直接交换这些数并将数组排序；对于 Double 类型的数组，我们交换的是储存了这些数字的 Double 对象的引用。

如果仅仅是一大组数排序，我们可以跳过引用来节省储存引用所需的空间和通过引用来访问数字的成本。

### Java 系统库的排序算法

Java 系统程序员会对原始数据类型使用（三向切分的）快速排序，对引用类型使用归并排序。这实际上暗示着用速度和空间（对于原始数据类型）来换取稳定性（引用类型）。

Java 的 `Arrays.sort()` 已经基本够用了，它使用的三向快速排序和归并排序都是经典。

### 问题的规约

### 找出重复元素

首先将数组排序，再遍历有序的数组，记录连续出现的重复元素即可。

**统计 a[] 中不重复元素的个数**

```java
Quick.sort(a);
int count = 1;
for (int i = 1; i < a.length; i++)
    if (a[i].compareTo(a[i - 1]) != 0)
        count++;
```

### 排名

两个排列之间的 Kendall tau 距离就是在两组数列中顺序不同的数对的数目；
某个排列和标准排列（即每个元素都在正确位置上的排列）的 Kendall tau 距离就是其中逆序数对的数量。

### 优先队列

之前的 TopM 和 Multiway 都可以用长度为 M 的优先队列解决。

### 中位数与顺序统计

```java
public static Comparable select(Comparable[] a, int k) {
    StdRandom.shuffle(a);
    int lo = 0, hi = a.length - 1;
    while (hi > lo) {
        int j = partition(a, lo, hi);
        if (j == k) return a[k];
        else if (j > k) hi = j - 1;
        else if (j < k) lo = j + 1;
    }
    return a[k];
}
```

上面的 `select()` 方法使用到了快速排序的切分方法，它可以在线性的时间内找到数组中第 $k$ 小的元素。

与快速排序另一个共同点是这段分析依赖于随机的切分元素，因此它的性能保证也来自于概率。

**算法的平均比较次数：**

<div style="text-align: center;">

~ $2N + 2klnN(N/k) + 2(N-k)ln(N/(N-k))$

</div>

## 排序应用一览

- 商业计算
- 信息搜索
- 运筹学
  - 调度
  - 负载均衡
- 事件驱动模拟
- 数值计算
- 组合搜索
  - Prim 算法和 Dijkstra 算法
  - Kruskal 算法
  - 霍夫曼压缩
  - 字符串处理

## 练习

见 [习题 2-5]() 。