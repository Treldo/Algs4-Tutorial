---
layout: default
title: 2.5&ensp;应用
nav_order: 4
parent: 第二章&emsp;排序
---

## 2.5&ensp;应用
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

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

---
