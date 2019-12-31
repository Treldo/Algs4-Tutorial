符号表最主要的目的就是将一个<mark>键</mark>和一个<mark>值</mark>联系起来。本章会讲解多种构造这种数据结构的方法，它们不仅能够高效地插入和查找，还可以进行其他几种方便的操作。

<!-- tabs:start -->

#### **<b>定义</b>**

符号表是一种储存键值对的数据结构，支持两种操作：**插入（put）**，即将一组新的键值对存入表中；**查找（get）**，即根据给定的键得到对应的值。

<!-- tabs:end -->

**典型应用：**字典，图书索引，文件共享，账户管理，网络搜索，编译器...

## API

**一种简单泛型符号表 API**

![api](../_media/3.1/table-api.jpeg ':size=600')

### 设计决策

- **泛型：**没有指定处理对象的类型。
- **重复的键：**
    + 每个键只对应一个值；
    + 新的键值对会在产生冲突时覆盖旧的键值对。
- **空键：**键不能为空，否则会产生运行时异常。
- **空值：**不允许有空值，否则会与 `get()` 方法返回值混淆。
- **删除操作：**
    + *延时删除：*将键值设置为空，在某个时候删去所有键值为空的键。
    + *即时删除：*立即删除指定的键。
- **便捷方法：**

<div style="text-align: center;">

![table-methods](../_media/3.1/table-methods.jpeg ':size=450')

</div>

- **迭代：**使用迭代器以方便遍历所有的键。
- **键的等价性：**为自己的数据类型实现 `equals()` 方法。

## 有序符号表

**一种有序的泛型符号表的 API**

![signed-api](../_media/3.1/symbol-table-api.jpeg ':size=600')

### 设计决策

- **最大键和最小键：**相对于优先队列，有序符号表支持的操作更多，但不可以存在重复的键。
- **向上取整和向下取整：**表示找出大于等于或小于等于该键的最小键或最大键。
- **排名和选择：**即找出小于等于指定键的键的数量，以及找出排名为 k 的键。
- **范围查找：**给定范围内键的数量是多少？有哪些？这就需要接受两个参数的 `size()` 和 `key()` 方法。
- **例外情况：**当一个方法需要返回一个键，但没有合适的键可以反回时，我们会抛出一个异常。
- **便捷方法：**

<div style="text-align: center;">

![symbol-table-methods](../_media/3.1/symbol-table-methods.jpeg ':size=600')

</div>

- **（再谈）键的等价性：**任何一种 Compareable 类型的两个值 a 和 b 都要保证 `a.compareTo(b) == 0` 和 `a.equals(b)` 的返回值相同。为避免潜在的二义性，我们只使用 `compareTo()` 方法来比较两个键。
- **成本模型：**我们使用<mark>比较</mark>一词来表示将一个符号表条目和一个被查找的键进行比较的操作。

<!-- tabs:start -->

#### **<b>查找的成本模型</b>**

在符号表的实现中，我们会统计<mark>比较</mark>的次数（等价性测试或是键的相互比较）。在内循环不进行比较（极少）的情况下，我们会统计 **数组的访问次数** 。

<!-- tabs:end -->

符号表的重点在于其中使用的数据结构和 `get()`、`put()` 方法。在用例代码中，除非我们想使用一个特定的实现，我们都会使用 ST 表示一个符号表的实现。

## 用例举例

### 行为测试用例

从标准输入接受多个字符串，构造一张符号表来将 i 和第 i 个字符串相关联，再打印符号表。

**简单的符号表测试用例**

```java
public static void main(String[] args) {
    ST<String, Integer> st;
    st = new St<String, Integer>();

    for (int i = 0; !StdIn.isEmpty(); i++) {
        String key = StdIn.readString();
        st.put(key, i);
    }

    for (String s : st.keys())
        StdOut.println(s + " " + st.get(s));
}
```

### 性能测试用例

从标准输入中得到一列字符串并记录每个（长度达到指定阈值）字符串出现的次数，然后遍历所有键并找出出现频率最高的键。这是一种 **字典** 。

**大型测试输入流的性质**

<div style="text-align: center;">

![char-input-stream](../_media/3.1/char-input-stream.jpeg ':size=600')

</div>

**符号表的用例**

```java
public class FrequencyCounter {
    public static void main(String[] args) {
        int minlen = Integer.parseInt(args[0]); // 最小键长
        ST<String, Integer> st = new ST<String, Integer>();
        while (!StdIn.isEmpty()) { // 构造符号表并统计频率
            String word = StdIn.readString();
            if (word.length() < minlen) continue; // 忽略较短的单词
            if (!st.contains(word)) st.put(word, 1);
            else st.put(word, st.get(word) + 1);
        }
        // 找出出现频率最高的单词
        String max = "";
        st.put(max, 0);
        for (String word : st.keys())
            if (st.get(word) > st.get(max)) max = word;
        StdOut.println(max + " " + st.get(max));
    }
}
```

这个符号表的用例统计了标准输入中各个单词的出现频率，然后将频率出现最高的单词打印出来。命令行中的参数指定了表中键的最短长度。

> **研究符号表处理大型文本的性能要考虑：**
>
> - 每个单词都会被作为键进行搜索，因此处理性能和输入文本的单词总量必然有关；
> - 输入的每个单词都会被存入符号表，因此输入流中不同的单词的总数也是相关的。

FrequencyCounter 是一种极为常见的应用的代表，它与许多其他符号表应用有同样的<mark>特性</mark>：

- 混合使用查找和删除的操作；
- 大量不同的键；
- 查找操作比插入操作多得多；
- 虽然不可预测，但查找和插入操作的使用模式并非随机。

## 无序链表中的顺序查找

使用基本链表结构，`get()` 方法遍历链表，`put()` 也同样是遍历链表，在指定位置更新相关键的值。

**使用基于链表的符号表的索引用例的轨迹**

<div style="text-align: center;">

![index-sample-table](../_media/3.1/index-sample-table.jpeg ':size=600')

</div>

**顺序查找（基于无序链表）**

```java
public class SequentialSearchST<Key, Value> {
    private Node first; // 链表首结点

    private class Node { // 链表结点的定义
        Key key;
        Value val;
        Node next;
        public Node(Key key, Value val, Node next) {
            this.key = key;
            this.val = val;
            this.next = next;
        }
    }

    public Value get(Key key) { // 查找给定的键，返回相关联的值
        for (Node x = first; x != null; x = x.next)
            if (key.equals(x.key))
                return x.val; // 命中
        return null; // 未名中
    }

    public void put(Key key, Value val) { // 查找给定的键，找到则更新其值，否则在表中新建结点
        for (Node x = first; x != null; x = x.next)
            if (key.equals(x.key)) {
              x.val = val;
               return;
            } // 命中，更新
        first = new Node(key, val, first); // 未命中，新建结点
    }
}
```

<!-- tabs:start -->

#### **命题**

在含有 $N$ 对键值的机遇（无序）链表的符号表中，未命中的查找和插入操作都需要 $N$ 次比较。命中的查找在最坏的情况也需要 $N$ 次比较。

#### **推论**

向一个空表中插入 $N$ 个不同的键需要 ~$N^2/2$ 次比较。

<!-- tabs:end -->

基于链表的实现以及顺序查找是非常低效的，无法满足 FrequencyCounter 处理庞大输入问题的需求。

这里我们用 FrequencyCounter 以及命令行参数 8 来分析 tale.txt 。为了将性能可视化我们使用了 VisualAccumulator 将每次 `put()` 操作转换为两个点：

> 对于第 i 次 `put()` 操作：
> 
> - 在横坐标为 i，纵坐标为该次操作所进行的比较次数的位置画一个灰点；
> - 在横坐标为 i，纵坐标为前 i 次 `put()` 操作累计所需的平均比较次数的位置画一个红点。

<div style="text-align: center;">

![cost-for-SequentialSeatchST](../_media/3.1/cost-for-SequentialSeatchST.jpeg ':size=600')

</div>

## 有序数组中的二分查找

核心实现是 `rank()` 方法，它返回表中小于给定键的键的数量。

**使用基于有序数组的符号表实现的索引用例的轨迹**

<div style="text-align: center;">

![trace-of-ordered-array-st-for-stdIndexClient](../_media/3.1/trace-of-ordered-array-st-for-stdIndexClient.jpeg ':size=600')

</div>

**二分查找（基于有序数组）**

```java
public class BinarySearchST<Key extends Comparable<Key>, Value> {
    private Key[] keys;
    private Value[] vals;
    private int N;

    public BinarySearchST(int capacity) { // 调整数组大小的标准代码请见算法1.1
        keys = (Key[]) new Comparable[capacity];
        vals = (Value[]) new Object[capacity];
    }

    public int size() {
        return N;
    }

    public Value get(Key key) {
        if (isEmpty()) return null;
        int i = rank(key);
        if (i < N && keys[i].compareTo(key) == 0)
            return vals[i];
        else return null;
    }

    public int rank(Key key) // 请见算法3.2（续1）

    public void put(Key key, Value val) { // 查找键，找到则更新值，否则创建新的元素
        int i = rank(key);
        if (i < N && keys[i].compareTo(key) == 0) {
            vals[i] = val;
            return;
        }
        for (int j = N; j > i; j--) {
            keys[j] = keys[j - 1];
            vals[j] = vals[j - 1];
        }
        keys[i] = key;
        vals[i] = val;
        N++;
    }

    public void delete(Key key) // 该方法的实现请见练习3.1.16

}
```
### 二分查找

**递归的二分查找**

```java
public int rank(Key key, int lo, int hi) {
    if (hi < lo) return lo;
    int mid = lo + (hi - lo) / 2;
    int cmp = key.compareTo(keys[mid]);
    if (cmp < 0) return rank(key, lo, mid - 1);
    else if (cmp > 0) return rank(key, mid + 1, hi);
    else return mid;
}
```

**基于有序数组的二分查找（迭代）**

```java
public int rank(Key key) {
    int lo = 0, hi = N - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;
        int cmp = key.compareTo(keys[mid]);
        if (cmp < 0) hi = mid - 1;
        else if (cmp > 0) lo = mid + 1;
        else return mid;
    }
    return lo;
}
```

**在有序数组中使用二分法查找排名的轨迹**

<dir style="text-align: center;">

![trace-of-binarySearch-for-rank-in-orderedArray](../_media/3.1/trace-of-binarySearch-for-rank-in-orderedArray.jpeg ':size=500')

</dir>

### 其他操作

**基于二分查找的有序符号表的其他操作**

```java
public Key min() {
    return keys[0];
}

public Key max() {
    return keys[N - 1];
}

public Key select(int k) {
    return keys[k];
}

public Key ceiling(Key key) {
    int i = rank(key);
    return keys[i];
}

public Key floor(Key key) // 请见练习3.1.17

public Key delete(Key key) // 请见练习3.1.16

public Iterable<Key> keys(Key lo, Key hi) {
    Queue<Key> q = new Queue<Key>();
    for (int i = rank(lo); i < rank(hi); i++)
        q.enqueue(keys[i]);
    if (contains(hi))
        q.enqueue(keys[rank(hi)]);
    return q;
}
```

自己完成 `delete()` 和 `floor()` 方法。

## 对二分查找的分析

