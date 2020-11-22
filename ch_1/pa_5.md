## 动态连通性

假设“相连”是一种等价关系，则它具有：自反性、对称性和传递性。

<small>**注：**<mark>本节使用网络方面的术语，将对象称为触点，将整数对称为连接，将等价类称为连通分量或是简称为分量。</mark></small>

**union-find 算法的 API**

![api-union-find](../_media/1.5/api-union-find.jpeg ':size=600')

**成本模型：** *数组的访问次数* 。

## 实现

### quick-find 算法

```java
public class UF {
    private int[] id;
    private int count;

    public UF(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++)
            id[i] = i;
    }

    public int count() { return count; }

    public boolean connected(int p, int q) { return find(p) == find(q); }

    public int find(int p) { return id[p]; }

    public void union(int p, int q) {
        int pID = find(p);
        int qID = find(q);
        if (pID == qID) return;
        for (int i = 0; i < id.length; i++)
            if (id[i] == pID) id[i] = qID;
        count--;
    }
}
```

可以引入一个测试用例：

```java
public static void main(String[] args) {
    int N = StdIn.readInt();
    UF uf = new UF(N);
    while (!StdIn.isEmpty()) {
        int p = StdIn.readInt();
        int q = StdIn.readInt();
        if (uf.connected(p, q)) continue;
        uf.union(p, q);
        StdOut.println(p + " " + q);
    }
    StdOut.println(uf.count + "components");
}
```

我们使用 `connected()` 方法判断两个整型是否相同，来判断它们是否在同一分量中（我们将数值相等作为触点存在于同一分量的标准）。如果不在同一分量中，`union()` 方法会将数组中 id 与 p 相同的元素的值替换为 id[q] 的值，以此起到建立连接的作用。

具体操作如图，这样可能会更加明晰一点：

<div style="text-align: center;">

![dynamic-connectivity-tiny](../_media/1.5/dynamic-connectivity-tiny.jpeg ':size=200')

</div>

### quick-find 算法的分析

> 关于本节 **并查集（union-find）** 的解读，可以参考这篇大牛的 <a href="https://blog.csdn.net/dm_vincent/article/details/7655764" target="_blank" >博客</a> ，没有理解的同学可以找一下思路。

quick-find 算法解决动态连通性问题最后的到一个连通分量，那么至少需要调用 $N-1$ 次 `union()` 方法，那么数组访问次数就是 $(N+3)(N-1)$ ~ $N^2$ ，可以猜想 quick-find 算法是平方级别的，可见不能够很好解决数据量很大的情况。

### quick-union 算法

```java
public class UF {
    private int[] id;
    private int count;

    public UF(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++)
            id[i] = i;
    }

    public int count() { return count; }

    public boolean connected(int p, int q) { return find(p) == find(q); }

    public int find(int p) {
        while (p != id[p]) p = id[p];
        return p;
    }

    public void union(int p, int q) {
        int pRoot = find(p);
        int qRoot = find(q);
        if (pRoot == qRoot) return;
        id[pRoot] = qRoot;
        count--;
    }
}
```

id[] 数组使用父链接的形式表示了一片森林，实际上每个触点的 id 值都表示了它的根节点或父节点的 id 值，而触点中的根触点或者说根节点的 id 值都与自己的索引值是相同的。

看图结合代码仔细想想就能明白了：

<div style="text-align: center;">

![quick-union-overview](../_media/1.5/quick-union-overview.jpeg ':size=360')

</div>

可以再观察这个例子：

<div style="text-align: center;">

![quick-union-trace](../_media/1.5/quick-union-trace.jpeg ':size=360')

</div>

### quick-union 算法的分析

quick-union 算法是对 quick-find 算法的改良，但并不是所有情况都要比 quick-find 算法快，但它避免了操作总是线性级别的问题。quick-union 算法中的 `find()` 方法在最坏的情况下需要访问数组 $2N+1$ 次（这里的 $N$ 代指深度）。

<!-- tabs:start -->

#### **<b>定义</b>**

- 节点的深度是它到根节点的路径上的链接数；
- 树的大小是它的节点数量；
- 树的高度是所有节点中的最大深度。

<!-- tabs:end -->

一种最坏的情况就是每一次的 find 操作都会进行 $2N+1$ 次的数组访问，如图：

<div style="text-align: center;">

![quick-union-worst-case](../_media/1.5/quick-union-worst-case.jpeg ':size=300')

</div>

按这种操作处理，对于 $N$ 对整数， find 方法就需要访问数组 $3+5+7+...+(2N-1)$ ~ $N^2$ 次。

### 加权 quick-union 算法

前面的 quick-union 算法中可能会在 `union()` 方法中会随意将一个树连接到另一个树上，在加权实现中，我们会将较小的树连接到较大的树上，像这样：

<div style="text-align: center;">

![weighted-quick-union](../_media/1.5/weighted-quick-union.jpeg ':size=300')

</div>

可以再观察这个例子：

<div style="text-align: center;">

![weighted-quick-union-traces](../_media/1.5/weighted-quick-union-traces.jpeg ':size=480')

</div>

<br />

```java
public class WeightedQuickUnionUF {
    private int[] id;       // 父链接数组
    private int[] sz;       // 根节点对应的分量大小
    private int count;      // 连通分量的数量

    public WeightedQuickUnionUF(int N) {
        count = N;
        id = new int[N];
        for (int i = 0; i < N; i++) id[i] = i;
        sz = new int[N];
        for (int i = 0; i < N; i++) sz[i] = 1;
    }

    public int count() { return count; }

    public boolean connected(int p, int q) { return find(p) == find(q); }

    public int find(int p) {
        while (p != id[p]) p = id[p];
        return p;
    }

    public void union(int p, int q) {
        int i = find(p);
        int j = find(q);
        if (i == j) return;
        if (sz[i] < sz[j]) { id[i] = j; sz[j] += sz[i]; }
        else {id[j] = i; sz[i] += sz[j]; }
        count--;
    }
}
```

### 加权 quick-union 算法的分析


在算法中将较小的树连接到较大的树上，树的大小由 sz[] 数组的索引值来决定，初始时所有触点的分量大小为 1 。

?> 当一个触点成为根节点之后，如果它又成为了某个根节点的子节点，自身的分量数不会增加，即使将其他分量数更小的节点与这个节点建立关系，最终都会连接到它现在的根节点。


> 对于 $N$ 个触点，加权 quick-union 算法构造的森林中的任意节点的深度最多为 $lgN$ 。对于加权 quick-union 算法和 $N$ 个触点，在最坏情况下 **find()**、 **connected()** 和 **union()** 的成本的增长数量级为 $logN$ 。

### quick-union 算法和加权 quick-union 算法的比较

<br />

<div style="text-align: center;">

![quick-union-and-weighted-quick-union](../_media/1.5/quick-union-and-weighted-quick-union.jpeg ':size=400')

</div>

<br />

加权 quink-union 算法处理 $N$ 个触点和 $M$ 连接时最多访问数组 $cMlgN$ 次（$c$ 为常数），比起普通的 quick-union 算法的至少 $MN$ 次，在在处理大规模动态连通性问题时要快许多。

**各种 union-find 算法的性能特点**

<br />

<div style="text-align: center;">

![performance](../_media/1.5/performance.jpeg ':size=540')

</div>

### 最优算法

有一种能够保证在常数时间内完成各种操作的算法，就是使用 **路径压缩** 的方式。这样做就是要在检查节点的同时将它们直接链接到根节点，只需要在 `find()` 里添加一个循环实现操作。

?> 使用路径压缩的加权 quick-union 算法的每个操作在最坏情况下都不是常数级别的（均摊后），但这已经是我们能给出的最优解了。

**find() 方法的实现：**

```java
public int find(int p) {
    int current = p, t;
    while (p != id[p]) p = id[p];
    // 将当前节点的每代父节点都链接到根节点上（但不会改变它的子节点）
    while (current != p) {
        t = id[current];
        id[current] = p;
        current = t;
    }
    return p;
}
```

## 补充

- cell-probe 是一种计算模型，我们只会记录对随机内存的访问，内存的大小足以保存所有输入且假设其他操作均没有成本。

## 习题

见 [习题 1-5](/exercise/ex_1/pa_5) 。