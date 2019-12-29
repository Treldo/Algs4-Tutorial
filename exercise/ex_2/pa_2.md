## 练习

### 1. 原地归并的排序过程

<div style="text-align: center;">
  <p><font color="#dcdde1">A&emsp;E&emsp;Q&emsp;S&emsp;U&emsp;Y&emsp;E&emsp;I&emsp;N&emsp;O&emsp;S&emsp;T</font></p>
  <p>A&emsp;<font color="#dcdde1">E&emsp;Q&emsp;S&emsp;U&emsp;Y&emsp;E&emsp;I&emsp;N&emsp;O&emsp;S&emsp;T</font></p>
  <p>A&emsp;E&emsp;<font color="#dcdde1">Q&emsp;S&emsp;U&emsp;Y&emsp;E&emsp;I&emsp;N&emsp;O&emsp;S&emsp;T</font></p>
  <p>A&emsp;E&emsp;E&emsp;<font color="#dcdde1">S&emsp;U&emsp;Y&emsp;E&emsp;I&emsp;N&emsp;O&emsp;S&emsp;T</font></p>
  <p>A&emsp;E&emsp;E&emsp;I&emsp;<font color="#dcdde1">U&emsp;Y&emsp;E&emsp;I&emsp;N&emsp;O&emsp;S&emsp;T</font></p>
  <p>A&emsp;E&emsp;E&emsp;I&emsp;N&emsp;<font color="#dcdde1">Y&emsp;E&emsp;I&emsp;N&emsp;O&emsp;S&emsp;T</font></p>
  <p>A&emsp;E&emsp;E&emsp;I&emsp;N&emsp;O&emsp;<font color="#dcdde1">E&emsp;I&emsp;N&emsp;O&emsp;S&emsp;T</font></p>
  <p>A&emsp;E&emsp;E&emsp;I&emsp;N&emsp;O&emsp;Q&emsp;<font color="#dcdde1">I&emsp;N&emsp;O&emsp;S&emsp;T</font></p>
  <p>A&emsp;E&emsp;E&emsp;I&emsp;N&emsp;O&emsp;Q&emsp;S&emsp;<font color="#dcdde1">N&emsp;O&emsp;S&emsp;T</font></p>
  <p>A&emsp;E&emsp;E&emsp;I&emsp;N&emsp;O&emsp;Q&emsp;S&emsp;U&emsp;<font color="#dcdde1">O&emsp;S&emsp;T</font></p>
  <p>A&emsp;E&emsp;E&emsp;I&emsp;N&emsp;O&emsp;Q&emsp;S&emsp;U&emsp;Y&emsp;<font color="#dcdde1">S&emsp;T</font></p>
  <p>A&emsp;E&emsp;E&emsp;I&emsp;N&emsp;O&emsp;Q&emsp;S&emsp;U&emsp;Y&emsp;S&emsp;<font color="#dcdde1">T</font></p>
  <p>A&emsp;E&emsp;E&emsp;I&emsp;N&emsp;O&emsp;Q&emsp;S&emsp;U&emsp;Y&emsp;S&emsp;T</p>
</div>

### 2. 自顶向下排序过程

<div style="text-align: center;">

![](https://algs4.cs.princeton.edu/22mergesort/images/merge-3.2.2.png ':size=300')

</div>

### 3. 自底向上的排序过程

<div style="text-align: center;">

![](https://algs4.cs.princeton.edu/22mergesort/images/mergebu-3.2.2.png ':size=300')

</div>

### 4. 原地归并的结果

原地归并只是将整个数组分成了两个部分，这两个部分中左半边始终小于等于右半边。所以得到的结果可能是有序的，但却不一定，取决于调用方法前的数组排列顺序。

### 5. 子数组大小和顺序

- 自顶向下：
```
2 3 2 5 2 3 2 5 10 2 3 2 5 2 3 2 5 10 20 2 3 2 5 2 3 2 5 10 2 3 2 5 2 2 4 9 19 39
```

- 自底向上：
```
2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 2 4 4 4 4 4 4 4 4 4 3 8 8 8 8 7 16 16 32 39
```

See the code <a href="https://algs4.cs.princeton.edu/22mergesort/MergeSizes.java.html">MergeSizes.java<i class="fab fa-java"></i></a> .

### 6. 访问数组的次数

### 7. 比较次数单调递增

### 8. 修改 -> 有序数组线性级别

$T(N) = 2T(N / 2) + 1$ , 且 $T(1) = 0$

### 9. 辅助数组作为参数

See the code <a href="https://algs4.cs.princeton.edu/22mergesort/Merge.java.html">Merge.java <i class="fab fa-java"></i></a> .
