---
layout: default
title: PA_3
nav_order: 3
parent: CH_1
---

## 1.3&ensp;背包、队列和栈
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

三种数据类型：背包（Bag）、队列（Queue）和栈（Stack）。它们的不同之处就在于删除或者访问对象的顺序不同。

## API

<br />

<table>
  <caption>表 1.3.1&emsp;泛型可迭代的基础集合数据类型的 API</caption>
  <tr>
    <th colspan="3">背包</th>
  </tr>
  <tr>
    <td colspan="3">public class <b>Bag&lt;Item&gt;</b> implements Iterable&lt;Item&gt;</td>
  </tr>
  <tr>
    <td></td>
    <td>Bag()</td>
    <td>创建一个空背包</td>
  </tr>
  <tr>
    <td style="text-align:right">void</td>
    <td>add(Item item)</td>
    <td>添加一个元素</td>
  </tr>
  <tr>
    <td style="text-align:right">boolean</td>
    <td>isEmpty()</td>
    <td>背包是否为空</td>
  </tr>
  <tr>
    <td style="text-align:right">int</td>
    <td>size()</td>
    <td>背包中的元素数量</td>
  </tr>
  <tr><td><br /></td></tr>
  <tr>
    <th colspan="3">先进先出（FIFO）队列</th>
  </tr>
  <tr>
    <td colspan="3">public class <b>Queue&lt;Item&gt;</b> implements Iterable&lt;Item&gt;</td>
  </tr>
  <tr>
    <td></td>
    <td>Queue()</td>
    <td>创建空队列</td>
  </tr>
  <tr>
    <td style="text-align:right">void</td>
    <td>enqueue(Item item)</td>
    <td>添加一个元素</td>
  </tr>
  <tr>
    <td style="text-align:right">Item</td>
    <td>dequeue()</td>
    <td>删除最早添加的元素</td>
  </tr>
  <tr>
    <td style="text-align:right">boolean</td>
    <td>isEmpty()</td>
    <td>队列是否为空</td>
  </tr>
  <tr>
    <td style="text-align:right">int</td>
    <td>size()</td>
    <td>队列中的元素数量</td>
  </tr>
  <tr><td><br /></td></tr>
  <tr>
    <th colspan="3">下压（后进先出，LIFO）栈</th>
  </tr>
  <tr>
    <td colspan="3">public class <b>Stack&lt;Item&gt;</b> implements Iterable&lt;Item&gt;</td>
  </tr>
  <tr>
    <td></td>
    <td>Stack()</td>
    <td>创建一个空栈</td>
  </tr>
  <tr>
    <td style="text-align:right">void</td>
    <td>push(Item item)</td>
    <td>添加一个元素</td>
  </tr>
  <tr>
    <td style="text-align:right">Item</td>
    <td>pop()</td>
    <td>删除最近添加的元素</td>
  </tr>
  <tr>
    <td style="text-align:right">boolean</td>
    <td>isEmpty()</td>
    <td>栈是否为空</td>
  </tr>
  <tr>
    <td style="text-align:right">int</td>
    <td>size()</td>
    <td>栈中的元素数量</td>
  </tr>
</table>

<br />

### 泛型

泛型也叫参数化类型。看个例子：

```java
Stack<String> stack = new Stack<String>();

Queue<Date> queue = new Queue<Date>();
```

如果向 stack 中添加 Date 对象（或是任何非 String 类型数据），或者是向 queue 中添加 String 对象（或是任何非 Date 类型数据），都会得到一个编译时的错误。

### 自动装箱

类型参数必须被实例化为引用类型，在处理赋值语句、方法的参数和算术或逻辑表达式时，Java 会自动在引用类型和对应的原始数据类型之间进行转换。例如：

```java
Stack<Integer> stack = enw Stack<Integer>();
stack.push(17);         // 自动装箱 (int -> Integer)
int i = stack.pop();    // 自动拆箱 (Integer -> int)
```

### 可迭代的集合类型

直接上例子：

```java
// 假设在 Queue 中维护一个交易集合
Queue<Transaction> queue = new Queue<Transaction>();

// 如果集合是可迭代的，打印交易列表
for (Transaction t : queue) {
    StdOut.println(t);
}
```

### 背包

背包不支持从中删除元素，迭代的顺序不确定且与用例无关。使用 Bag 就说明元素的处理顺序不重要，典型用例就是计算平均值和标准差:

```java
public class Stats {
    public static void main(String[] args) {
        Bag<Double> numbers = new Bag<>();
        while (!StdIn.isEmpty())
            numbers.add(StdIn.readDouble());
        int N = numbers.size();

        double sum = 0.0;
        for (double x : numbers)
            sum += x;
        double mean = sum / N;

        sum = 0.0;
        for (double x : numbers)
            sum += (x - mean) * (x - mean);
        double std = Math.sqrt(sum / (N - 1));

        StdOut.printf("Mean: %.2f\n", mean);
        StdOut.printf("Std dev: %.2f\n", std);
    }
}
```

### 先进先出（FIFIO）队列

使用 foreach 语句迭代访问队列中的元素时，元素的处理顺序就是它们被添加到队列中的顺序。在应用程序中使用队列的主要原因就是它在保存元素的同时也保存了顺序，也就保证了入列和出列顺序相同。

- 队列的用例
```java
public static int[] readInts(String name) {
    // 在这里 in 对象的作用就是来保存传入的整型数组的元素作为 System.in 输入
    In in = new In(name);
    Queue<Integer> q = new Queue<>();
    while (!in.isEmpty())
        q.enqueue(in.readInt());
    int N = q.size();
    int[] a = new int[N];
    for (int i = 0; i < N; i++)
        a[i] = q.dequeue();
    return a;
}
```

### 下压栈

下压栈是一种基于后进先出（LIFO）策略的集合类型，叠书就是一个栈的例子。浏览网页时会遇到栈的另一个例子：单击“回退”按钮可以重新访问之前的页面。栈另一个特点就是将存入的数据逆序排列，下面的代码就是一个典型的例子：

```java
public class Reverse {
    public static void main(String[] args) {
        Stack<Integer> s = new Stack<>();
        while (!StdIn.isEmpty())
            s.push(StdIn.readInt());

        for (int i : s)
            StdOut.println(i);
    }
}
```

### 算术表达式求值

E.W.Dijkstra 发明了一个非常简单的算法，用到了两个栈（操作数栈、运算符栈）。具体思路：

- 将操作数压入（push）操作数栈；
- 将运算符压入运算符栈；
- 忽略左括号；
- 在遇到右括号时，弹出一个运算符，弹出所需数量的操作数，并将运算符和操作数的运算结果压入操作数栈。

在处理完最后一个括号后，操作数栈上就只剩一个表达式的值。运算顺序：

```
(1 + ((2 + 3) * (4 * 5)))
(1 + (5 * (4 * 5)))
(1 + (5 * 20))
(1 + 100)
101
```

- Dijkstra 的双栈算术表达式求值算法
```java
public class Evaluate {
    public static void main(String[] args) {
        Stack<String> ops = new Stack<>();     // 运算符栈
        Stack<Double> vals = new Stack<>();    // 操作数栈
        while (!StdIn.isEmpty()) {
            // readString() 方法使用了 Scanner.next() 来读取字符串
            String s = StdIn.readString();
            if (s.equals("("));
            else if (s.equals("+")) ops.push(s);
            else if (s.equals("-")) ops.push(s);
            else if (s.equals("*")) ops.push(s);
            else if (s.equals("/")) ops.push(s);
            else if (s.equals("sqrt")) ops.push(s);
            else if (s.equals(")")) {
                String op = ops.pop();
                double val = vals.pop();
                if (op.equals("+")) val = vals.pop() + val;
                else if (op.equals("-")) val = vals.pop() - val;
                else if (op.equals("*")) val = vals.pop() * val;
                else if (op.equals("/")) val = vals.pop() / val;
                else if (op.equals("sqrt")) val = Math.sqrt(val);
                vals.push(val);
            } else vals.push(Double.parseDouble(s));
        }
        StdOut.println(vals.pop());
    }
}
```

没有弄清楚这个用例很容易就会出现异常，或是计算出错误的结果。这是一个简单的算法的实现，它有几点要求：

- 输入的每个字符串都以空格隔开；
- 必须要有右括号（否则不能实现计算）；
- 每次只能进行两个操作数的运算；
- 开平方也是要需要加括号的。

所以为了保险起见，还是要清晰一点。来个稍复杂一些的，比如要计算 12/(6-sqrt(2*8))，我们就这样写： `( 12 / ( 6 - ( sqrt ( 2 * 8 ) ) ) )`

用过 Emacs 学习了 emacs-lisp 或者学过 lisp 语言的同学可能会想，如果用 lisp 语法进行输入可不可以得到正确结果呢 🧐？同样计算上面式子，输入下面的内容，自己试一试：`( / 12 ( - 6 ( sqrt ( * 2 8 ) ) ) )`

> 另外，如果使用 IDE 工具，想要结束输入需要按组合键 Ctrl + D（Windows）或 Command + D（Mac）。

## 集合类数据类型的实现

### 定容栈

<br />

<table>
  <caption>表 1.3.2&emsp;一种表示定容字符串栈的抽象数据类型</caption>
  <tr>
    <td><b>API</b></td>
    <td colspan="3">public class <b>FixedCapacityStackOfStrings</b></td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td>FixedCapacityStackOfStrings(int cap)</td>
    <td>创建一个容量为 cap 的空栈</td>
  </tr>
  <tr>
    <td></td>
    <td style="text-align:right">void</td>
    <td>push(String item)</td>
    <td>添加一个字符串</td>
  </tr>
  <tr>
    <td></td>
    <td style="text-align:right">String</td>
    <td>pop()</td>
    <td>删除最近添加的字符串</td>
  </tr>
  <tr>
    <td></td>
    <td style="text-align:right">boolean</td>
    <td>isEmpty()</td>
    <td>栈是否为空</td>
  </tr>
  <tr>
    <td></td>
    <td style="text-align:right">int</td>
    <td>size()</td>
    <td>栈中的字符串数量</td>
  </tr>
</table>

<br />

上面这个数据类型与栈的区别就在于它只能处理 String 值，要求用例指定一个容量且不支持迭代。

它的实例变量为一个用于保存栈中元素的数组 a[] ，和一个用于保存栈中元素数量的整数 N 。 添加和删除元素时 N 的值都会发生变化。

- 数据类型的实现
```java
public class FixedCapacityStackOfStrings {
    private String[] a;  // 存储栈中元素的数组
    private int N;       // 记录数组大小

    public FixedCapacityStackOfStrings(int cap) { a = new String[cap]; }

    public boolean isEmpty() { return N == 0; }

    public int size() { return N; }

    public void push(String item) { a[N++] = item; }
    // 缩减数组大小，丢弃最后一个元素
    public String pop() { return a[--N]; }
}
```

理解了上面的实现，请务必完全理解下面的内容：

- 测试用例
```java
public static void main(String[] args) {
    FixedCapacityStackOfStrings s = new FixedCapacityStackOfStrings(100);
    while (!StdIn.isEmpty()) {
        String item = StdIn.readString();
        if (!item.equals("-")) s.push(item);
        else if (!s.isEmpty()) StdOut.print(s.pop() + " ");
    }
    StdOut.println("(" + s.size() + " left on stack)");
}
```

输入数据 `to be or not to - be - - that - - - is`

会得到结果
```
to be not that or be
```

那么符号 ` - ` 的作用究竟是什么呢？请仔细思考。

### 泛型

上面的例子可以使用泛型进行处理，这样做是有好处的，使用泛型就可以处理不仅仅是 String 类型的数据。

```java
public class FixedCapacityStack<Item> {...}
```

Item 是一个类型参数，可以理解为象征性的占位符。前面也有提到过泛型，这里需要记住的一点就是：Java 会使用类型参数 Item 来检查类型不匹配的错误。

如果我们想要创建一个泛型的数组，会想要这样写：

```java
Item[] a = new Item[cap];
```

但其实这样做是不行的。由于某些历史和技术原因，Java 中并不允许创建泛型数组，感兴趣的同学可以：
- 参考知乎上的 <a href="https://www.zhihu.com/question/20928981" target="_blank" >回答</a> ；
- 阅读掘金上的文章 <a href="https://juejin.im/post/5b28ca4e51882574e10df32c" target="_blank" >泛型的基本原理</a> ；
- 自行搜索（<a href="https://www.google.com/search?q=Java+中为什么不允许创建泛型数组" target="_blank" >Google</a>、<a href="https://www.baidu.com/s?wd=Java中为什么不允许创建泛型数组" target="_blank" >百度</a>、<a href="https://www.dogedoge.com/results?q=Java%20中为什么不允许创建泛型数组" target="_blank" >多吉搜索</a>、<a href="https://mijisou.com/?q=Java+中为什么不允许创建泛型数组" target="_blank" >秘迹搜</a>）。

我们可以使用类型转换来解决这个问题：

```java
Item[] a = (Item[]) new Object[cap];
```

这段代码能够达到我们需要的效果，但 Java 编译器会给出一条类似于这样的警告：`Unchecked cast: 'java.lang.Object[]' to 'Item[]'` ，不过可以忽略它。

<br />

<table>
  <caption>表 1.3.4&emsp;一种表示泛型定容栈的抽象数据类型</caption>
  <tr>
    <td><b>API</b></td>
    <td colspan="3">public class <b>FixedCapacityStack</b>&lt;Item&gt;</td>
  </tr>
  <tr>
    <td></td>
    <td></td>
    <td>FixedCapacityStack(int cap)</td>
    <td>创建一个容量为 cap 的空栈</td>
  </tr>
  <tr>
    <td></td>
    <td style="text-align:right">void</td>
    <td>push(<b>Item</b> item)</td>
    <td>添加一个元素</td>
  </tr>
  <tr>
    <td></td>
    <td style="text-align:right"><b>Item</b></td>
    <td>pop()</td>
    <td>删除最近添加的元素</td>
  </tr>
  <tr>
    <td></td>
    <td style="text-align:right">boolean</td>
    <td>isEmpty()</td>
    <td>栈是否为空</td>
  </tr>
  <tr>
    <td></td>
    <td style="text-align:right">int</td>
    <td>size()</td>
    <td>栈中的元素数量</td>
  </tr>
</table>

<br />

使用泛型来实现数据类型后，代码也改变了：

- 数据类型的实现
```java
public class FixedCapacityStack<Item> {
    private Item[] a;
    private int N;

    public FixedCapacityStack(int cap) { a = (Item[]) new Object[cap]; }

    public boolean isEmpty() { return N == 0; }

    public int size() { return N; }

    public void push(Item item) { a[N++] = item; }

    public Item pop() {
        // 如果要避免数据游离，就参考下面的 pop() 实现
        return a[--N];
    }
}
```

- 测试用例
```java
public static void main(String[] args) {
    FixedCapacityStack<String> s = new FixedCapacityStack<>(100);
    while (!StdIn.isEmpty()) {
        String item = StdIn.readString();
        if (!item.equals("-")) s.push(item);
        else if (!s.isEmpty()) StdOut.print(s.pop() + " ");
    }
    StdOut.println("(" + s.size() + " left on stack)");
}
```

### 调整数组的大小

在 Java 中，数组一旦创建，就不可以改变它的大小，栈使用的空间只能是这个最大容量的一部分。正常来说应该有一个 isFull() 的方法来判断栈是否已满，避免数据溢出。先不实现这个方法，我们通过修改数组的实现来动态调用数组的大小以保证数据存储的最大利用率。

- 实现一个将栈移动到另一个大小为 max 的数组中的方法：
```java
// 这里不允许使用其他类调用这个方法，所以使用了 private 修饰符
private void resize(int max) {
    Item[] temp = (Item[]) new Object[max];
    for (int i = 0; i < N; i++) {
        temp[i] = a[i];
    }
    a = temp;
}
```

这个方法所起到的间接作用就是扩大了数组 a[] 的大小。

- 修改 push() 方法，在数据量达到数组最大容量时，将数组的容量扩大到 2 倍。
```java
public void push(Item item) {
    if (N == a.length) resize(2 * a.length);
    a[N++] = item;
}
```

- 修改 pop() 方法，在数据量不及总容量的 1/4 时，缩减数组容量到 1/2：
```java
public Item pop() {
    Item item = a[--N];
    // 避免对象游离（见下节）
    a[N] = null;
    if (N > 0 && N == a.length / 4) resize(a.length / 2);
    return item;
}
```

这里将最小数据量控制在 1/4 ，栈永远不会溢出，使用率不会低于 1/4（除了栈为空的情况）。

**PS:** 这里请自行理解。

### 对象游离

在上面使用 pop() 方法弹出了一个元素，它就不会再被访问了，但数组中的引用仍可以让它继续存在。像这种保存一个不需要的对象的引用的情况就称之为游离。

为了避免游离，我们只需要将弹出的元素值设置为 null，这会覆盖无用的引用，并使系统在该元素使用完毕后回收它的内存。

### 迭代

我们知道 foreach 语句可以迭代访问集合中的数据，其实它本质上与 while 语句是等价的，看这两组代码：
```java
Stack<String> collection = new Stack<>();

for (String s : collection)
    StdOut.println(s);
```

```java
Stack<String> collection = new Stack<>();
Iterator<String> i = collection.iterator();

while (i.hasNext()) {
    String s = i.next();
    StdOut.println(s);
}
```

可迭代的集合的集合数据类型需要：
- 实现一个 iterator() 方法并返回一个 Iterator 对象；
- Iterator 类必须包含两个方法：hasNext()（返回一个 boolean 值）和 next()（返回一个集合中的泛型元素）。

对与可迭代的数据类型，Java 中定义了所需接口 java.lang.Iterable ：
```java
public interface Iterable<Item> {
    Iterator<Item> iterator();
}
```

我们使用接口机制来指定一个类所必须实现的方法，所以在一个需要迭代的集合中我们必须实现接口中的 iterator() 方法。

要逆序迭代遍历整个数组，我们定义一个名为 ReverseArrayIterator 的迭代器的类来实现 Iterator 接口（意味着需要实现 Iterator 接口中的方法），作为实现了 Iterable 接口的集合中 iterator() 方法的返回值。

- 接口 Iterator
```java
public interface Iterator<Item> {
    boolean hasNext();
    Item next();
    void remove();
}
```

- 迭代器 ReverseArrayIterator
```java
private class ReverseArrayIterator implements Iterator<Item> {
    private int i = N;

    @Override
    public boolean hasNext() { return i > 0; }

    @Override
    public Item next() {
        if (i == 0) throw new NoSuchElementException();
        return a[--i];
    }

    @Override
    public void remove() { throw new UnsupportedOperationException(); }
}
```

我们这里没有给出 remove() 方法的实现，因为我们希望避免在迭代中穿插能够修改数据结构的操作。这个类是实现在栈类的一个嵌套类中（栈类可以是上面提到的类 FixedCapacityStack<Item> ），我们来重写一下这个栈类：

```java
import java.util.Iterator;
import java.util.NoSuchElementException;

public class FixedCapacityStack<Item> implements Iterable {
    private Item[] a;
    private int N;

    public FixedCapacityStack(int cap) {
        a = (Item[]) new Object[cap];
    }

    private void resize(int max) {
        Item[] temp = (Item[]) new Object[max];
        for (int i = 0; i < N; i++)
            temp[i] = a[i];
        a = temp;
    }

    @Override
    public Iterator<Item> iterator() {
        return new ReverseArrayIterator();
    }

    private class ReverseArrayIterator implements Iterator<Item> {
        private int i = N;

        @Override
        public boolean hasNext() {
            return i > 0;
        }

        @Override
        public Item next() {
            if (i == 0) throw new NoSuchElementException();
            return a[--i];
        }

        @Override
        public void remove() {
            throw new UnsupportedOperationException();
        }
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public int size() {
        return N;
    }

    public void push(Item item) {
        if (N == a.length) resize(2 * a.length);
        a[N++] = item;
    }

    public Item pop() {
        Item item = a[--N];
        a[N] = null;
        if (N > 0 && N == a.length / 4) resize(a.length / 2);
        return item;
    }
}
```

如果不想让 ReverseArrayIterator 作为这个栈类的嵌套类，你也可以单独新建一个类来实现这个迭代器。但要注意的是，ReverseArrayIterator 实现了 Iterator 接口的同时实现了 next() 方法，这个方法拥有一个泛型的返回值。所以我们要这样去定义这个类：

```java
public class ReverseArrayIterator<Item> implements Iterator<Item> {...}
```

其实在你在试过之后就会发现这样是不行的，因为在这个迭代器中使用到了栈类中的数据（存储栈中元素的数组和这个数组所存元素个数）。所以还是就让它做栈类的嵌套类吧！

另外要注意的就是 Iterable 在 `java.lang` 中，而 Iterator 在 `java.util` 中（因为某些历史原因）。

算法 1.1 是 Stack API 的一种能够动态改变数组大小的实现， 它几乎达到了任意集合类数据类型的实现的最佳性能：
- 每项操作的用时都与集合大小无关；
- 空间需求总是不超过集合大小乘以一个常数。

缺点在于某些 push() 和 pop() 操作会调整数组的大小：这项操作的耗时和栈大小成正比。我们会学习克服该缺陷的办法，使用一种不同的方式来组织数据。

**算法 1.1&emsp;下压（LIFO）栈（能够动态调整数组大小的实现）**

```java
import java.util.Iterator;
import java.util.NoSuchElementException;

public class ResizingArrayStack<Item> implements Iterable {
    private Item[] a = (Item[]) new Object[1];
    private int N = 0;

    public boolean isEmpty() {
        return N == 0;
    }

    public int size() {
        return N;
    }

    private void resize(int max) {
        Item[] temp = (Item[]) new Object[max];
        for (int i = 0; i < N; i++) {
            temp[i] = a[i];
        }
        a = temp;
    }

    public void push(Item item) {
        if (N == a.length) resize(2 * a.length);
        a[N++] = item;
    }

    public Item pop() {
        Item item = a[--N];
        a[N] = null;
        if (N > 0 && N == a.length / 4) resize(a.length / 2);
        return item;
    }

    @Override
    public Iterator<Item> iterator() {
        return new ReverseArrayIterator();
    }

    private class ReverseArrayIterator implements Iterator<Item> {
        private int i = N;

        @Override
        public boolean hasNext() {
            if (i == 0) throw new NoSuchElementException();
            return i > 0;
        }

        @Override
        public Item next() {
            return a[--i];
        }

        @Override
        public void remove() {
            throw new UnsupportedOperationException();
        }
    }
}
```

**PS:** 好像跟我们之前写的那个没有什么不同 😅

## 链表

链表是一种递归的数据结构，他或者为空（null），或者指向一个结点（node）的引用，该结点含有一个泛型的与与元素和一个指向另一条链表的引用。

### 结点记录

我们可以在嵌套类中定义结点的抽象数据类型。

```java
private class Node {
    Item item;
    Node next;
}
```

我们会通过 `new Node()` 触发无参的构造方法来创建一个 Node 类型的对象，它会是一个实例化变量初始为 null 的指向某个 Node 对象的引用。这种类型的类有时也被称为记录。

### 构造链表

我们尝试构造一个含有元素 to、be 和 or 的链表，先为每个元素创造结点：

```java
Node first = new Node();
Node second = new Node();
Node third = new Node();
```

我们在 item 域中存放 String 类型的数据：

```java
first.item = "to";
second.item = "be";
third.item = "or";
```

再设置 next 域来构造链表：

```java
first.next = second;
second.next = third;
```

这里 third.next 还是 null 的值，也就是说 third 指向的是 null ，我们看这个图：

<img src="{{ site.baseurl }}{% link /assets/images/1.3/Node.png %}" width="360"/>

链表表示的是一列元素，它与数组的区别就在于：在链表中向序列插入和删除元素更为方便。

### 在表头插入结点

两种方式：
- 创建一个结点，将它的 next 域设置为 first；
- 将 first 保存在 oldfirst 中，再将一个新结点赋予 first ，这里我们用 first 的 item 域存放 “not” ，把它的 next 域设置为 oldfirst ：
```java
Node oldfirst = first;
first = new Node();
first.item = "not";
first.next = oldfirst;
```

### 从表头删除结点

我们使 first 指向 first.next ，这样改变了 first 的值（变成了之前 first.next 结点对象的一个引用），就无法访问它曾经指向的结点了。曾经的结点对象变成了一个孤儿，Java 的内存管理系统最终将回收它所占用的内存。

```java
first = first.next;
```

这条赋值语句的运行时间和链表的长度无关。

### 在表尾插入结点

类似于在表头插入结点，还是两种方式：
- 创建一个结点，将之前的 last 结点的 next 域设置为这个结点；
- 将之前的 last 保存在 oldlast 中，设置 oldlast 的 next 域为 last ，再将一个新结点赋予 last ，这里我们用 last 的 item 域存放 “not” ：
```java
Node oldlast = last;
last = new Node();
last.item = "not";
oldlast.next = last;
```

> **PS:** 这里需要强调一点，和在表头插入结点相同，我们创建了一个结点 oldxxx 来保存 xxx ，就是复制了一个 xxx 对象的引用 oldxxx 指向 xxx 对象的地址，相当于创建了一个别名，而不是单纯的值复制，如此一来就有 oldxxx 和 xxx 同时指向同一个对象（我们 [前面](../pa_2/#item) 有提到过只有原始数据类型是值的复制，其他都复制的是对象的引用）。然后我们又使用 new 关键字来更新 xxx 对象，是重新为 xxx 分配了内存空间，而之前的内存空间并没有被弃用，所以不会被系统回收。

### 其他位置的插入和删除操作

例如，我们要删除链表的尾结点就需要将它的前一个结点的 next 域设置为 null 。但我们没有其他的信息来找出这个位置，所以唯一的办法就是遍历整条链表并找出指向 last 的结点，如此所需的时间就同链表的长度成正比了。

实现任意插入和删除操作的标准解决方案就是使用 **双向链表** ，每一个结点都有两个链接指向不同的方向。详见 [练习 1.3.31]() 。

### 遍历

我们可以这样来实现链表的迭代访问：
```java
for (Node x = first; x != null; x = x.next()) { x.item }
```

### 栈的实现

在进行 push() 操作时，我们将元素添加在表头；当使用 pop() 删除一个元素时，我们将它从表头删除。

链表的使用可以达到最优设计目标：
- 可以处理任意类型的数据；
- 所需空间总是和集合大小成正比；
- 操作所需时间与集合大小无关。

**算法 1.2&emsp;下压堆栈（链表实现）**

```java
import java.util.Iterator;

public class Stack<Item> implements Iterable<Item> {
    private Node first;
    private int N;

    @Override
    public Iterator<Item> iterator() {
        return new ListIterator();
    }

    private class ListIterator implements Iterator<Item> {
        private Node current = first;

        @Override
        public boolean hasNext() {
            return current != null;
        }

        @Override
        public Item next() {
            Item item = current.item;
            current = current.next;
            return item;
        }

        @Override
        public void remove() {
            // 这里是接口里默认实现的方法，展示出来
            throw new UnsupportedOperationException("remove");
        }
    }

    private class Node {
        Item item;
        Node next;
    }

    public boolean isEmpty() {
        return first == null;
    }

    public int size() {
        return N;
    }

    public void push(Item item) {
        Node oldFirst = first;
        first = new Node();
        first.item = item;
        first.next = oldFirst;
        N++;
    }

    public Item pop() {
        Item item = first.item;
        first = first.next;
        N--;
        return item;
    }
}
```

自己动手写一个测试用例吧！

### 队列的实现

在结构化储存数据集时，**链表是数组的一种重要的替代方式** 。

**算法 1.3&emsp;先进先出队列**

```java
import java.util.Iterator;

public class Queue<Item> implements Iterable {
    private Node first;
    private Node last;
    private int N;

    @Override
    public Iterator<Item> iterator() {
        return new ListIterator();
    }

    private class ListIterator implements Iterator<Item> {
        private Node current = first;

        @Override
        public boolean hasNext() {
            return current != null;
        }

        @Override
        public Item next() {
            Item item = current.item;
            current = current.next;
            return item;
        }

        @Override
        public void remove() {
            // 这里是接口里默认实现的方法，展示出来
            throw new UnsupportedOperationException("remove");
        }
    }

    private class Node {
        Item item;
        Node next;
    }

    public boolean isEmpty() {
        return first == null;
    }

    public int size() {
        return N;
    }

    public void enqueue(Item item) {
        Node oldLast = last;
        last.item = item;
        if (isEmpty()) first = last;
        else oldLast.next = last;
        N++;
    }

    public Item dequeue() {
        Item item = first.item;
        first = first.next;
        N--;
        if (isEmpty()) last = null; // 避免对象游离
        return item;
    }
}
```

自己动手写一个测试用例吧！

### 背包的实现

**算法 1.4&emsp;背包**

```java
import java.util.Iterator;

public class Bag<Item> implements Iterable<Item> {
    private Node first;
    private int N;

    @Override
    public Iterator<Item> iterator() {
        return new ListIterator();
    }

    private class ListIterator implements Iterator<Item> {
        private Node current = first;

        @Override
        public boolean hasNext() {
            return current != null;
        }

        @Override
        public Item next() {
            Item item = current.item;
            current = current.next;
            return item;
        }

        @Override
        public void remove() {
            // 这里是接口里默认实现的方法，展示出来
            throw new UnsupportedOperationException("remove");
        }
    }

    private class Node {
        Item item;
        Node next;
    }

    public boolean isEmpty() {
        return first == null;
    }

    public int size() {
        return N;
    }

    private void add(Item item) {
        Node oldFirst = first;
        first = new Node();
        first.item = item;
        first.next = oldFirst;
        N++;
    }
}
```

背包的链表实现就是 Stack 去掉 pop() 方法，再将它的 push() 更名为 add() 。

## 综述

- 这些数据类型是其他高级数据结构的基础；
- 它们展示了满足性能目标所带来的挑战；
- 重点要支持对对象集合的强大操作。

### 数据结构

数组和链表常被称为顺序存储和链式存储。

预告
- 二叉树的数据结构是含有两个链接的结点组成；
- 复合型数据结构：用背包储存栈，用队列储存数组；
- 我们可以用数组的背包来表示图。

<br />

| 数据结构 | 优点     | 缺点     |
| :------------- | :------------- | :------------- |
| 数组       | 通过索引可以直接访问任意元素       | 在初始化时就需要知道元素的数量 |
| 链表       | 使用的空间和元素数量成正比 | 需要通过引用访问任意元素 |

<br />

使用抽象想数据类型解决问题：
- 定义 API；
- 根据特定场景开发用例代码；
- 描述一种数据结构，根据 API 定义类的实例变量；
- 描述算法，根据它实现类中的实例方法；
- 分析算法的性能特点。

## 补充

- 除了使用泛型，我们还可以构造一个 Object 对象的栈，并在 pop() 时将对象转换为所需类型。问题就是类型不匹配的错误只能在运行时发现，而使用泛型则会得到一个编译错误。
- Java 中泛型数组：先了解 <a href="https://zhuanlan.zhihu.com/p/35387281" target="_blank" >协变数组（convariant array）</a>和 <a href="https://blog.csdn.net/wisgood/article/details/11762427" target="_blank" >类型擦除（type erasure）</a>。
- 创建一个字符串栈的数组（需要进行类型转换）
```java
Stack<String>[] a = (Stack<String>[]) new Stack[N];
```
- 在我们写的代码中，栈为空时调用 pop() 会得到一个 NullPointerException 异常。
- 用例在迭代中调用 push() 或 pop() ，Stack 的迭代器会立即抛出一个 java.util.ConcurrentModificationException 异常。
- String 没有实现 Iterable 接口，不能使用 foreach 循环访问。

## 习题

见 [习题 1-3](/independent-pages/src/algs4/1/1.3/)
