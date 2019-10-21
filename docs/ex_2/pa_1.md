---
layout: default
title: 2-1
nav_order: 1
parent: 习题 二
---

## 习题 -> 2.1&ensp;初级排序算法
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 练习

### 1. 排序过程

<div style="text-align: center;">
  <img src="http://foyoodo.oss-cn-beijing.aliyuncs.com/image_src/algs4/2.1/selection-2.1.1.png" width="300" />
</div>

<br />

### 2. 交换次数

交换的平均次数为 1，最多被交换 \\(N\\) 次。

最多交换次数：

<div style="text-align: center;">
  <img src="http://foyoodo.oss-cn-beijing.aliyuncs.com/image_src/algs4/2.1/selection-max.png" width="300" />
</div>

<br />

### 3. 构造数组

若使 a[j] < a[min] 成功次数最大，可以让它是一个完全逆序排列的数组。

### 4. 排序过程

<div style="text-align: center;">
  <img src="http://foyoodo.oss-cn-beijing.aliyuncs.com/image_src/algs4/2.1/insertion-2.1.4.png" width="300" />
</div>

<br />

### 5. 构造数组

内层 for 循环的判断条件是 `j > 0 && less(a[j], a[j - 1])` ，我们可以使 `less()` 方法每一次的判断都是假的，那么数组就需要顺序排列，或是部分主键相同。

### 6. 谁更快 ？

在主键相同时，插入排序的时间是线性的。而对于选择排序则是平方级别的。

### 7. 谁更快 ？

数组是逆序的，对于插入排序来说就意味着内层 for 循环每一次都要进行到底，时间是平方级别的（交换次数为平方级别）。而对于选择排序来说，数组元素的交换次数在 \\(N/2\\) 左右。

### 8. 运行时间

平方级别。随机数组意味着三种值出现的概率相同，那么外层 for 循环遍历到的每个元素 \\(N\\) 都有可能进行 \\(N/3\\) 次交换。

### 9. 排序过程

<div style="text-align: center;">
  <img src="http://foyoodo.oss-cn-beijing.aliyuncs.com/image_src/algs4/2.1/shell-2.1.9.png" width="500" />
</div>

### 10. 为什么不使用选择排序 ？

对于 h 有序，插入排序在部分有序的条件下要比选择排序好很多。

### 11. 预先计算

将 h 的序列保存在一个数组中即可。

### 12. 打印

创建一个 \\(N\\) 个元素的数组，对每个元素的比较次数进行记录。

---
