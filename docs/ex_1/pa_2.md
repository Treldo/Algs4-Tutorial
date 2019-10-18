---
layout: default
title: PA_2
nav_order: 2
parent: EX_1
---

## 习题 -> 1.2&ensp;数据抽象
{: .no_toc }

## Table of contents
{: .no_toc .text-delta }

1. TOC
{:toc}

---

## 练习

- 部分用例的用法
```shell
% javac Testxxx.java
% java Testxxx [args]
```

- 以下出现 `Integer.parseInt(args[x])` 的地方均可以替换为 `StdIn.readxxx()` ，不过这样变成了从标准输入中读取数据。题中要求从命令行接受数据，所以最好避免从标准输入中进行数据的获取。

---

### 1. Point2D 用例

```java
public class TestPoint2D {
    public static void main(String[] args) {
        int N = Integer.parseInt(args[0]);
        Point2D[] points = new Point2D[N];
        for (int i = 0; i < N; i++) {
            points[i] = new Point2D(Math.random(), Math.random());
            points[i].draw();
        }
        if (N > 1) {
            double min = points[0].distanceTo(points[1]);
            for (int i = 0; i < N - 1; i++) {
                for (int j = i + 1; j < N; j++) {
                    if (points[i].distanceTo(points[j]) < min) {
                        min = points[i].distanceTo(points[j]);
                    }
                }
            }
            System.out.println("The min distance: " + min);
        }
    }
}
```

### 2. Interval1D 用例

```java
public class TestInterval1D {
    public static void main(String[] args) {
        int N = Integer.parseInt(args[0]);
        Interval1D[] intervals = new Interval1D[N];
        for (int i = 0; i < N; i++) {
            intervals[i] = new Interval1D(StdIn.readDouble(), StdIn.readDouble());
        }
        for (int i = 0; i < N - 1; i++) {
            for (int j = i + 1; j < N; j++) {
                if (intervals[i].intersects(intervals[j])) {
                    StdOut.println(intervals[i] + " -- " + intervals[j]);
                }
            }
        }
    }
}
```

### 3. Interval2D 用例

```java
public class TestInterval2D {
    private static int intersectCount = 0;
    private static int containCount = 0;

    private static double max(double a, double b) {
        return Math.max(a, b);
    }

    private static double min(double a, double b) {
        return Math.min(a, b);
    }

    public static void main(String[] args) {
        int N = Integer.parseInt(args[0]);
        double min = Double.parseDouble(args[1]);
        double max = Double.parseDouble(args[2]);
        // 设置绘图范围（可以保证所有矩形都绘制在窗格内）
        StdDraw.setXscale(min, max);
        StdDraw.setYscale(min, max);
        // 供判断 contains 条件的点集合
        Point2D[] leftTopPoints = new Point2D[N];
        Point2D[] rightBottomPoints = new Point2D[N];
        Interval2D[] interval2DS = new Interval2D[N];
        for (int i = 0; i < N; i++) {
            double x1 = StdRandom.uniform(min, max);
            double x2 = StdRandom.uniform(min, max);
            double y1 = StdRandom.uniform(min, max);
            double y2 = StdRandom.uniform(min, max);
            leftTopPoints[i] = new Point2D(min(x1, x2), min(y1, y2));
            rightBottomPoints[i] = new Point2D(max(x1, x2), max(y1, y2));
            interval2DS[i] = new Interval2D(new Interval1D(min(x1, x2), max(x1, x2)), new Interval1D(min(y1, y2), max(y1, y2)));
            interval2DS[i].draw();
        }

        for (int i = 0; i < N - 1; i++) {
            for (int j = 1; j < N; j++) {
                if (interval2DS[i].intersects(interval2DS[j])) {
                    intersectCount++;
                }
                if (interval2DS[i].contains(leftTopPoints[j]) && interval2DS[i].contains(rightBottomPoints[j])) {
                    containCount++;
                }
            }
        }
        StdOut.println("Intersect count: " + intersectCount);
        StdOut.println("Contain count: " + containCount);
    }
}
```

### 4. 单纯的值传递

```shell
world
hello
```

### 5. String 对象不可变

所有的字符串方法都不会改变参数对象的值，而是返回一个新的 String 对象。

### 6. 回环变位

- 书中提示的方法
```java
if ((s + s).indexOf(t) >= 0) {};
```
- 使用了字符串拼接的方法
```java
if (s.concat(s).contains(t)) {};
```

### 7. 递归函数的返回值

返回一个倒序的字符串。

### 8. 数组引用

复制的是引用，不是数据。原始数据类型才会将数据复制下来，其余复制的都是对象的引用。

### 9. BinarySearch 中增加 Counter

我这里又定义了一个 Counter 类，以便于对记录值进行修改。

```java
class Counter {
    private int val = 0;

    public int getVal() {
        return val;
    }

    public void add() {
        val++;
    }
}

public class BinarySearch {
    public static int rank(int key, int[] a, Counter counter) {
        return rank(key, a, 0, a.length - 1, counter);
    }

    public static int rank(int key, int[] a, int lo, int hi, Counter counter) {
        // 如果 key 存在于 a[] 中，它的索引不会小于 lo 且不会大于 hi
        if (lo > hi) return -1;

        int mid = lo + (hi - lo) / 2;
        if (key < a[mid]) {
            counter.add();
            return rank(key, a, lo, mid - 1, counter);
        } else if (key > a[mid]) {
            counter.add();
            return rank(key, a, mid + 1, hi, counter);
        } else {
            counter.add();
            return mid;
        }
    }

    public static void main(String[] args) {
        Counter counter = new Counter();
        int[] a = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
        System.out.println(rank(6, a, counter));
        System.out.println(counter.getVal());
    }
}
```

### 10. 类 VisualCounter

- 实现：
```java
public class VisualCounter {
    private int val;
    private int N;
    private int max;
    // 用来记录操作次数
    private int counter = 0;

    public VisualCounter(int N, int max) {
        this.N = N;
        this.max = max;
        StdDraw.setXscale(0, N);
        StdDraw.setYscale(0, max);
        StdDraw.setPenColor(StdDraw.RED);
        StdDraw.setPenRadius(.01);
    }

    public void add() {
        if (val < max && counter < N) val++;
        StdDraw.point(counter, val);
        counter++;
    }

    public void reduce() {
        if (counter < N) val--;
        StdDraw.point(counter, val);
        counter++;
    }
}
```

- 用例：
```java
public class TestVisualCounter {
    public static void main(String[] args) {
        VisualCounter counter = new VisualCounter(100, 100);
        for (int i = 0; i < 50; i++) {
            counter.add();
        }
        for (int i = 0; i < 30; i++) {
            counter.reduce();
        }
        for (int i = 0; i < 20; i++) {
            counter.add();
        }
    }
}
```

### 11. SmartDate 类

这里定义了 `年-月-日-时-分-秒` 这六个变量，设置了四个构造器，提供了对变量进行操作和得到当前星期的方法。因为代码比较易懂，所以这里就不会多作说明了。书上 表1.2.12 中 Date 类的另一种实现还挺有意思的，有兴趣的同学可以看一下，另外还可以阅读 <font color="#16a085">edu.princeton.cs.algs4</font> 包中 Date 类的 [源码](https://algs4.cs.princeton.edu/code/edu/princeton/cs/algs4/Date.java.html) 。

> 当日期信息错误时，会报异常：`java.lang.IllegalArgumentException: Illegal date message.`
> 当时间信息错误时，会报异常：`java.lang.IllegalArgumentException: Illegal time message.`

**PS:** 没有注意到书中提供的 Date 的 API 😂，所以可能同书中的要求有所不同，变化不大，就不作修改了。

- 实现：
```java
public class SmartDate {
    private int year;
    private int month;
    private int date;
    private int hrs;
    private int min;
    private int sec;

    public SmartDate(int year, int month, int date) {
        this(year, month, date, 0, 0, 0);
    }

    public SmartDate(int year, int month, int date, int hrs) {
        this(year, month, date, hrs, 0, 0);
    }

    public SmartDate(int year, int month, int date, int hrs, int min) {
        this(year, month, date, hrs, min, 0);
    }

    public SmartDate(int year, int month, int date, int hrs, int min, int sec) {
        if (year >= 0) {
            switch (month) {
                default:
                    if (hrs >= 0 && hrs <= 23) {
                        if (min >= 0 && min <= 59) {
                            if (sec >= 0 && sec <= 59) {
                                this.hrs = hrs;
                                this.min = min;
                                this.sec = sec;
                            }
                        }
                    } else {
                        throw new IllegalArgumentException("Illegal time message.");
                    }
                case 1:
                case 3:
                case 5:
                case 7:
                case 8:
                case 10:
                case 12:
                    if (date >= 1 && date <= 31) {
                        if (hrs >= 0 && hrs <= 23) {
                            this.year = year;
                            this.month = month;
                            this.date = date;
                        }
                    } else {
                        throw new IllegalArgumentException("Illegal date message.");
                    }
                    break;
                case 4:
                case 6:
                case 9:
                case 11:
                    if (date >= 1 && date <= 30) {
                        this.year = year;
                        this.month = month;
                        this.date = date;
                    } else {
                        throw new IllegalArgumentException("Illegal date message.");
                    }
                    break;
                case 2:
                    if (year % 4 == 0 && year % 100 != 0 || year % 400 == 0) {
                        if (date >= 1 && date <= 29) {
                            this.year = year;
                            this.month = month;
                            this.date = date;
                        } else {
                            throw new IllegalArgumentException("Illegal date message.");
                        }
                    } else {
                        if (date >= 1 && date <= 28) {
                            this.year = year;
                            this.month = month;
                            this.date = date;
                        } else {
                            throw new IllegalArgumentException("Illegal date message.");
                        }
                    }
            }
        } else {
            throw new IllegalArgumentException("Illegal date message.");
        }
    }

    public int getDayOfWeek() {
        return (date + 2 * month + 3 * (month + 1) / 5 + year + year / 4 - year / 100 + year / 400) % 7;
    }

    public int getYear() {
        return year;
    }

    public void setYear(int year) {
        this.year = year;
    }

    public int getMonth() {
        return month;
    }

    public void setMonth(int month) {
        this.month = month;
    }

    public int getDate() {
        return date;
    }

    public void setDate(int date) {
        this.date = date;
    }

    public int getHrs() {
        return hrs;
    }

    public void setHrs(int hrs) {
        this.hrs = hrs;
    }

    public int getMin() {
        return min;
    }

    public void setMin(int min) {
        this.min = min;
    }

    public int getSec() {
        return sec;
    }

    public void setSec(int sec) {
        this.sec = sec;
    }
}
```

- 测试用例：
```java
public class TestSmartDate {
    public static void main(String[] args) {
        SmartDate date = new SmartDate(2019, 8, 27);
        System.out.println(date.getDayOfWeek());
    }
}
```

### 12. 方法 dayOfTheWeek()

上面已经有了 SmartDate 类的源码，这里只给出方法的实现。

```java
public String dayOfTheWeek() {
    int index = (date + 1 + 2 * month + 3 * (month + 1) / 5 + year + year / 4 - year / 100 + year / 400) % 7;
    switch (index) {
        case 0: return "Sunday";
        case 1: return "Monday";
        case 2: return "Tuesday";
        case 3: return "Wednesday";
        case 4: return "Thursday";
        case 5: return "Friday";
        case 6: return "Saturday";
        default: return null;
    }
}
```

### 13. Transaction 类型

参考了一下 edu.princeton.cs.algs4 包里的 Transaction 类，发现写这个类型是为了记录交易信息，类中包含了 `人-时间-交易数目` 。有兴趣的同学可以阅读 <font color="#16a085">edu.princeton.cs.algs4</font> 包中 Transaction 类的 [源码](https://algs4.cs.princeton.edu/code/edu/princeton/cs/algs4/Transaction.java.html) 。
```java
// 刚刚才写了 SmartDate 类，那我们就使用它来代替 Date 吧！
public class Transaction {
    private final String who;
    private final SmartDate date;
    private final double amount;
    public Transaction(String name, SmartDate date, double amount) {
        who = name;
        this.date = date;
        this.amount = amount;
    }

    public String who() {
        return who;
    }
    public SmartDate date() {
        return date;
    }
    public double amount() {
        return amount;
    }
    public String toString() {
        return who() + "/" + date() + "/" + amount();
    }

}
```

### 14. Transaction 类的 equals() 方法

这个方法是完全仿照书上 Date 类中的 equals() 方法写的。

```java
public boolean equals(Object x) {
    if (this == x) return true;
    if (x == null) return false;
    // 如果两者的类型不同则返回 false 值
    if (this.getClass() != x.getClass()) return false;
    // 类型转换
    Transaction that = (Transaction) x;
    if (this.who != that.who) return false;
    if (this.date != that.date) return false;
    if (this.amount != that.amount) return false;
    return true;
}
```

---

## 提高题

- [ ] 待更新

---
