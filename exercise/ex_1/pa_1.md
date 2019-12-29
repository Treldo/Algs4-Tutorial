---

知乎上有一篇总结的还不错, 如果觉得不够详细，或者想要参考题目以及其他答案的同学，可以在 [这里](https://zhuanlan.zhihu.com/p/31127610) 找到。

---

## 练习

### 1. 表达式的值

&emsp;a. `7`
&emsp;b. `200.0000002`
&emsp;c. `true`

### 2. 类型和值

&emsp;a. `double, 1.618`
&emsp;b. `double, 10.0`
&emsp;c. `boolean, true`
&emsp;d. `String, 33`

### 3. equal 判断

```java
import java.util.Scanner;

public class Test {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        int a = sc.nextInt();
        int b = sc.nextInt();
        int c = sc.nextInt();
        if (a == b && b == c) {
            System.out.println("equal");
        } else {
            System.out.println("not equal");
        }
    }
}
```

### 4. 语句修正

```java
if (a > b) c = 0; else b = 0;
```

### 5. 范围判断

```java
import java.util.Scanner;

public class Test {
    public static boolean right(double d) {
        if (d >= 0.0 && d <= 1.0) {
            return true;
        } else {
            return false;
        }
    }

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        double x = sc.nextDouble();
        double y = sc.nextDouble();
        if (right(x) && right(y)) {
            System.out.println("true");
        } else {
            System.out.println("false");
        }
    }
}
```

<div id="Fibonacci"></div>

### 6. Fibnonacci 数列

```java
// 斐波那契数列的非递归算法
int f = 0;
int g = 1;
for (int i = 0; i <= 15; i++) {
    System.out.println(f);
    f = f + g;
    g = f - g;
}
```
- 结果
```java
0
1
1
2
3
5
8
13
21
34
55
89
144
233
377
610
```

### 7. 打印的值

&emsp;a. `3.00009`
&emsp;b. `499500`
&emsp;c. `10000`

### 8. 打印结果

&emsp;a. `b`
&emsp;b. `197`
&emsp;c. `e`

### 9. 正整数二进制

- Java 中有内置方法 `Integer.toBinaryString();`

```java
// 一个简洁的实现方法
String s = "";
for (int n = N; n > 0; n /= 2) {
    s = (n % 2) + s;
}
```

### 10. 代码存在的问题

没有用 new 为 a[] 分配内存，会产生一个 `variable a might not have been initialized` 的编译错误。

```java
int[] a;
for (int i = 0; i < 10; i++) {
    a[i] = i * i;
}
```

### 11. 打印二维布尔数组

```java
// 已初始化 boolean 类型二维数组 b
for (boolean[] booleans : b) {
    System.out.print(lineNumber++ + "| ");
    for (boolean aBoolean : booleans) {
        if (aBoolean) {
            System.out.print("*");
        } else {
            System.out.print(" ");
        }
    }
    System.out.println();
}
```

### 12. 打印结果

此处书中有误，代码段应为：
```java
int[] a = new int[10];
for (int i = 0; i < 10; i++)
    a[i] = 9 - i;
for (int i = 0; i < 10; i++)
    a[i] = a[a[i]];
for (int i = 0; i < 10; i++)
    System.out.println(i);
```
- 结果
```java
0
1
2
3
4
4
3
2
1
0
```

### 13. 二维数组的转置

```java
// 使用 int 类型数组举例，已初始化数组 a[M][N]
int[][] temp = new int[N][M];
for (int i = 0; i < N; i++) {
    for (int j = 0; j < M; j++) {
        temp[i][j] = a[j][i];
        System.out.print(temp[i][j] + " ");
    }
    System.out.println();
}
```

### 14. 返回不大于 log₂N 的最大整数

```java
public static int lg(int N) {
    int result = 0;
    int temp = 2;
    while (temp < N) {
        temp *= 2;
        result++;
    }
    return result;
}
```

### 15. 返回长度为 M 的数组

```java
public static int[] histogram(int[] a, int M) {
    int[] temp = new int[M];
    for (int i = 0; i < M; i++) {
        for (int value : a) {
            if (i == value) temp[i]++;
        }
    }
    return  temp;
}
```

### 16. exR1(6) 的返回值

```java
311361142246
```

### 17. 递归函数的问题

这段代码中的基础情况永远不会被访问。调用 `exR2(3)` 会产生调用 `exR2(0)`、`exR2(-3)` 和 `exR2(-6)`，循环往复直到发生 `StackOverflowError`。

### 18. 考察乘法与乘方的递归实现

- 修改前：
```java
public static int mystery(int a, int b) {
    if (b == 0) return 0;
    if (b % 2) return mystery(a + a, b / 2);
    return mystery(a + a, b / 2) + a;
}
```
- 返回值
```java
mystery(2, 25) = 50
mystery(3, 11) = 33
mystery(a, b) = a * b
```

**核心思想：** `x * y = (x * 2 * 2 * ...) * (y / 2 / 2 / ...)`
***PS:*** 此处的 x 可能为偶数，但 y 必为奇数。

- 修改后：
```java
public static int mystery(int a, int b) {
    if (b == 0) return 0;
    if (b % 2) return mystery(a * a, b / 2);
    return mystery(a * a, b / 2) * a;
}
```
- 返回值
```java
mystery(2, 25) = 33554432
mystery(3, 11) = 177147
mystery(a, b) = aᵇ
```

**核心思想：** `x ^ y = (x ^ 2) ^ (y / 2)`

### 19. 改进 F(N) 的实现

书上的递归方法探究一小时之内能够得到 `F(N)` 结果最大的 `N` 值因为耗时比较长这里就不做尝试了。改进的方法可以参考 6 中的 [斐波那契数列的非递归算法](#Fibonacci) ，下面的是按书上要求利用数组保存值的改进方法：

```java
public static long F(int N) {
    int[] a = new int[N];
    a[0] = 0;
    a[1] = 1;
    for (int i = 2; i < N; i++) {
        a[i] = a[i - 1] + a[i - 2];
    }
    return a[N - 1];
}
```

### 20. ln(N!) 的递归求解

```java
public static double ln(int N) {
    if (N == 0 || N == 1) {
        return;
    } else {
        return ln(N - 1) + Math.log(N);
    }
}
```

### 21. 输入数据打印表格

这里用到了 `printf()` 的格式化输出，不懂的同学请自行参考 [菜鸟教程](https://www.runoob.com/cprogramming/c-function-printf.html) 。
```java
System.out.print("Please input data count: ");
Scanner scanner  = new Scanner(System.in);
int n = scanner.nextInt();

String[] nameArray = new String[n];
int[] integerArray1 = new int[n];
int[] integerArray2 = new int[n];

for (int i = 0; i < n; i++) {
    nameArray[i] = scanner.next();
    integerArray1[i] = scanner.nextInt();
    integerArray2[i] = scanner.nextInt();
}

System.out.println("|----------|-----|-----|-----|");
for (int i = 0; i < n; i++) {
    System.out.printf("|%-10s|%-5d|%-5d|%.3f|\n", nameArray[i], integerArray1[i], integerArray2[i], ((double) integerArray1[i] / integerArray2[i]));
    System.out.println("|----------|-----|-----|-----|");
}
```
- 看到有一个比较有意思的解题方法，思路是用 `split()` 方法将得到的两行数据进行拆分。缺点是如果输入的数据不是以单个空格为间隔就会报 `NumberFormatException` 的异常。

```java
Scanner sc = new Scanner(System.in);
String[] a = new String[2];
int index = 0;

while (index < a.length) {
    a[index] = sc.nextLine();
    index++;
}

for (String s : a) {
    String[] stringArr = s.split(" ");
    int num1 = Integer.parseInt(stringArr[1]);
    int num2 = Integer.parseInt(stringArr[2]);
    System.out.printf("%-10s%-5d%-5d%.3f\n", stringArr[0], num1, num2, ((double) num1 / num2));
}
```

### 22. BinarySearch-按递归深度缩进

如果你忘记了之前的 `rank()` 方法，可以在 [这里](/ch_1/pa_1?id=递归) 找到它。下面来对它进行改写：

```java
public static int rank(int key, int[] a) {
    return rank(key, a, 0, a.length - 1, 1);
}
// 如果是在某一限定范围内进行查找，则需要手动传入参数 `deep` 的值
// `deep` 的值需要设定在大于等于 1 的整数群中，否则在第 3 - deep 行中才会出现缩进效果
public static int rank(int key, int[] a, int lo, int hi, int deep) {
    // 实现缩进
    for (int i = 1; i < deep; i++) {
        System.out.print("  ");
    }
    System.out.printf("%d.  lo: %d  hi: %d\n", deep, lo, hi);

    // 如果 key 存在于 a[] 中，它的索引不会小于 lo 且不会大于 hi
    if (lo > hi) return -1;
    int mid = lo + (hi - lo) / 2;
    if (key < a[mid]) {
        return rank(key, a, lo, mid - 1, deep + 1);
    } else if (key > a[mid]) {
        return rank(key, a, mid + 1, hi, deep + 1);
    } else {
        return mid;
    }
}
```

### 23. BinarySearch 的测试用例

```java
public static void main(String[] args) {
    int[] whiteList = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
    System.out.print("Please input \"+\" or \"-\": ");
    // 为避免读入多余字符使用了 edu.princeton.cs.algs4 包中 StdIn 类里的 readChar() 方法
    char flag = StdIn.readChar();
    // 读入第一个字符以外的其余字符（不作用途）
    StdIn.readLine();
    System.out.print("Please input keys(Split with space): ");
    String[] keyList = StdIn.readLine().split(" ");
    for (String s : keyList) {
        if (flag == '+') {
            if (rank(Integer.parseInt(s), whiteList) == -1) {
                System.out.printf("%s ", s);
            }
        } else if (flag == '-') {
            if (rank(Integer.parseInt(s), whiteList) != -1) {
                System.out.printf("%s ", s);
            }
        }
    }
    System.out.println("\nDone!");
}
```

### 24. 欧几里得算法计算最大公约数

```java
public class Euclid {
  public static int euclid(int p, int q) {
      if (q == 0) {
          return p;
      }
      System.out.printf("p = %d, q = %d\n", p, q);
      // 交换值的目的是为了减小递归的运算次数
      if (p < q) {
          int temp = p;
          p = q;
          q = temp;
      }
      return euclid(q, p % q);
  }

    public static void main(String[] args) {
        int p = StdIn.readInt();
        int q = StdIn.readInt();
        System.out.println("\nresult = " + euclid(p, q));
    }
}
```

### 25. 数学归纳法证明欧几里得算法

**参考：**

&emsp;简书 - [欧几里得算法心得(辗转相除法)](https://www.jianshu.com/p/7876eb2dff89?utm_campaign=maleskine&utm_content=note&utm_medium=seo_notes&utm_source=recommendation)

&emsp;百度文库 - [辗转相除法的证明](https://wenku.baidu.com/view/908b4f7059fafab069dc5022aaea998fcc2240e2.html)

## 提高题

### 26. 三个数字排序

第一行：a > b
第二行：a > c
第三行：b > c

得到：a ≧ b ≧ c

### 27. 二项分布