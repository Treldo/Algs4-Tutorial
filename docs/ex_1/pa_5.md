---
layout: default
title: PA_5
nav_order: 5
parent: EX_1
---

## 习题 -> 1.5&ensp;案例研究：union-find 算法
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 练习

### 1. 给出数组内容和访问次数

只需将方法这样改写一下：

```java
private int n = 0;

public int find(int index) {
    n++;
    return id[index];
}

public void union(int p, int q) {
    int pID = find(p);
    int qID = find(q);
    if (pID == qID) {
        for (int i = 0; i < id.length; i++)
            System.out.print(id[i] + " ");
        System.out.printf("[%d]\n", n);
        return;
    }
    for (int i = 0; i < id.length; i++) {
        if (id[i] == pID)
            id[i] = qID;
        n++;
    }
    for (int i = 0; i < id.length; i++)
        System.out.print(id[i] + " ");
    System.out.printf("[%d]\n", n);
    count--;
}
```

测试用例可以这样弄：

```java
public static void main(String[] args) {
    int N = StdIn.readInt();
    UF uf = new EX1(N);
    while (!StdIn.isEmpty()) {
        int p = StdIn.readInt();
        int q = StdIn.readInt();
        uf.union(p, q);
    }
    StdOut.println(uf.count + "components");
}
```

### 2. 完成 quick-union 算法

与 练习1.5.1 用例相同

```java
private int n = 0;

public int find(int p) {
    while (p != id[p]){
        p = id[p];
        n++;
    }
    return p;
}

public void union(int p, int q) {
    int pRoot = find(p);
    int qRoot = find(q);
    if (pRoot == qRoot){
        for (int i = 0; i < id.length; i++)
            System.out.print(id[i] + " ");
        System.out.printf("[%d]\n", n);
        return;
    }
    id[pRoot] = qRoot;
    n++;
    for (int i = 0; i < id.length; i++)
        System.out.print(id[i] + " ");
    System.out.printf("[%d]\n", n);
    count--;
}
```

画出每次 id[] 数组表示的森林吧～

### 3. 完成加权 quick-union 算法

```java
private int n = 0;

public int find(int p) {
    int current = p, t;
    while (p != id[p]) {
        p = id[p];
        n++;
    }
    while (current != p) {
        t = id[current];
        id[current] = p;
        current = t;
        n += 2;
    }
    return p;
}

public void union(int p, int q) {
    int i = find(p);
    int j = find(q);
    if (i == j) {
        for (int index = 0; index < id.length; index++)
            System.out.print(id[index] + " ");
        System.out.printf("[%d]\n", n);
        return;
    }
    if (sz[i] < sz[j]) {
        id[i] = j;
        sz[j] += sz[i];
        n++;
        for (int index = 0; index < id.length; index++)
            System.out.print(id[index] + " ");
        System.out.printf("[%d]\n", n);
    } else {
        id[j] = i;
        sz[i] += sz[j];
        n++;
        for (int index = 0; index < id.length; index++)
            System.out.print(id[index] + " ");
        System.out.printf("[%d]\n", n);
    }
    count--;
}
```

### 4. 添加 sz[] 数组的操作

```java
private int n1 = 0. n2 = 0;

public int find(int p) {
    int current = p, t;
    while (p != id[p]) {
        p = id[p];
        n1++;
    }
    while (current != p) {
        t = id[current];
        id[current] = p;
        current = t;
        n1 += 2;
    }
    return p;
}

public void union(int p, int q) {
    int i = find(p);
    int j = find(q);
    if (i == j) {
        for (int index = 0; index < id.length; index++)
            System.out.print(id[index] + " ");
        System.out.printf("[%d]\n", n1);
        for (int index = 0; index < sz.length; index++)
            System.out.print(sz[index] + " ");
        System.out.printf("[%d]\n", n2);
        return;
    }
    if (sz[i] < sz[j]) {
        id[i] = j;
        sz[j] += sz[i];
        n1++;
        n2 += 2;
        for (int index = 0; index < id.length; index++)
            System.out.print(id[index] + " ");
        System.out.printf("[%d]\n", n1);
        for (int index = 0; index < sz.length; index++)
            System.out.print(sz[index] + " ");
        System.out.printf("[%d]\n", n2);
    } else {
        id[j] = i;
        sz[i] += sz[j];
        n1++;
        n2 += 2;
        for (int index = 0; index < id.length; index++)
            System.out.print(id[index] + " ");
        System.out.printf("[%d]\n", n1);
        for (int index = 0; index < sz.length; index++)
            System.out.print(sz[index] + " ");
        System.out.printf("[%d]\n", n2);
    }
    count--;
}
```

### 5. 估算时间

\\(sum \approx 10^9 \times 10^6 \times 10 \times 10^9 = 10^{25}\\)

\\(T = sum \div 10^9 \div 3600 \div 24 \approx 116\ days\\)

### 6. 估算时间

\\(sum \approx lg10^9 \times 10 \times 10^6 = 9 \times 10^7\\)

\\(T = sum \div 10^9 \approx 0.1\ s\\)

### 7. 类的实现

前面都有提到，这里就不再赘述了。

### 8. 错在哪里

没有将 id[i] 同 p 的根节点 ID 进行比较，不能实现将两条多触点链连接在一起。

### 9. 可不可能

已经有同学将对应的树画出来了：

<div style="text-align: center; ">
  <img src="http://foyoodo.oss-cn-beijing.aliyuncs.com/image_src/algs4/1.5/weight-union-1.5.9.png" width="160" />
</div>

<br />

<font size="2px"><b>原文链接</b>：<a href="https://www.cnblogs.com/songdechiu/p/6561636.html">https://www.cnblogs.com/songdechiu/p/6561636.html</a></font>

<br />

对于加权的 quick-union 算法来说，需要将小的树连接到大的树上。如果是先将 6 和成型的 5 建立连接，那么 6 应该连接到 5 上面才对；而如果现将 5 和成型的 6 建立连接，那么 5 应该与 6 的根节点 1 建立连接。按这种思路进行推理...

### 10. 是否正确

这样做是正确的，不过可能会将一个小的树直接连接在另一个树的树叶上，增加了树的高度，不能保证性能。

### 11. 影响

这样做增加了 find() 操作的成本，增加了数组的访问次数，每一次 find() 操作都会消耗大量的时间成本。

---
