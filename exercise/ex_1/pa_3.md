## 练习

### 1. isFull() 方法

```java
public boolean isFull() { return N == a.length; }
```

### 2. java Stack 的输出

```
was best times of the was the it
```

### 3. 不可能产生的出栈返回值

- [x] a.

`0 1 2 3 4 - - - - - 5 6 7 8 9 - - - - -`

- [ ] b.

```
0 1 2 3 4 - 5 6 - 7 8 - - - - - 9 - - -
4 6 8 7 5 3 2 9 1 0
```
- [x] c.

`0 1 2 - 3 4 5 - 6 - 7 - - 8 - 9 - - - -`
- [x] d.

`0 1 2 3 4 - - - - - 5 - 6 - 7 - 8 - 9 -`
- [x] e.

`0 1 - 2 - 3 - 4 - 5 - 6 - 7 8 9 - - - -`
- [ ] f.

```
0 - 1 2 3 4 - 5 6 - - - 7 8 - - - - 9 -
0 4 6 5 3 8 7 2 1 9
```
- [ ] g.

```
0 1 - 2 3 4 - 5 6 7 - 8 9 - - - - - - -
1 4 7 9 8 6 5 3 2 0
```

- [x] h.

`0 1 2 - - 3 4 - - 5 6 - - 7 8 - - 9 - -`

### 4. 用例 Parentheses

```java
public class Parentheses {
    public static void main(String[] args) {
        Stack<Character> stack = new Stack<>();
        while_loop:
        while (!StdIn.isEmpty()) {
            char c = StdIn.readChar();
            if (c == '{' || c == '[' || c == '(') {
                stack.push(c);
            } else if (c == '}' || c == ']' || c == ')') {
                if (!stack.isEmpty()) {
                    switch (c) {
                        case '}':
                            if (stack.pop() != '{') {
                                StdOut.println(false);
                                System.exit(0);
                            }
                        case ']':
                            if (stack.pop() != '[') {
                                StdOut.println(false);
                                System.exit(0);
                            }
                        case ')':
                            if (stack.pop() != '(') {
                                StdOut.println(false);
                                System.exit(0);
                            }
                        default:
                            break;
                    }
                } else {
                    StdOut.println(false);
                    System.exit(0);
                }
            }
        }
        StdOut.println(true);
    }
}
```

**简述：**
- 在用例中 stack 栈只存入左半部分的括号；
- 根据括号匹配的原则，每一个右括号都应该同栈中 pop() 出来的左括号相匹配；
- 这个用例在使用时，如果右括号检查匹配失败，会自动结束输入；
- 如果括号完全匹配，最后栈为空；
- 如果输入中没有括号的存在，同样返回 true 值。

### 5. 打印结果

打印 N 的二进制表示。

### 6. 对 q 的操作

将 q 中的与元素逆序排列。

### 7. 方法 peek()

```java
public Item peek() {
    return first.item;
}
```

### 8. 数组内容和大小

`it` ，大小为 1 。

### 9. 中序表达式

```java
public class Infix {
    public static void main(String[] args) {
        Stack<String> ops = new Stack<>();
        Stack<String> vals = new Stack<>();
        while (!StdIn.isEmpty()) {
            String s = StdIn.readString();
            if (s.equals("+")) ops.push(s);
            else if (s.equals("-")) ops.push(s);
            else if (s.equals("*")) ops.push(s);
            else if (s.equals("/")) ops.push(s);
            else if (s.equals(")")) {
                if (!vals.isEmpty()) {
                    String s2 = vals.pop();
                    String s1 = vals.pop();
                    vals.push("( " + s1 + " " + ops.pop() + " " + s2 + " )");
                }
            } else {
                vals.push(s);
            }
        }
        for (String s : vals)
            StdOut.print(s);
    }
}
```

**简述：**
- ops 栈用来保存操作符号；
- vals 栈用来保存非操作符的内容（数值或数值操作符括号组合）；
- 正常执行的前提是按格式标准输入。

### 10. 过滤器 InfixToPostfix

```java
public class InfixToPostfix {
    public static void main(String[] args) {
        Stack<String> ops = new Stack<>();
        Stack<String> vals = new Stack<>();
        while (!StdIn.isEmpty()) {
            String s = StdIn.readString();
            if (s.equals("("));
            else if (s.equals("+")) ops.push(s);
            else if (s.equals("-")) ops.push(s);
            else if (s.equals("*")) ops.push(s);
            else if (s.equals("/")) ops.push(s);
            else if (s.equals(")")) {
                String s2 = vals.pop();
                String s1 = vals.pop();
                vals.push(s1 + " " + s2 + " " + ops.pop());
            } else {
                vals.push(s);
            }
        }
        for (String s : vals)
            StdOut.print(s);
    }
}
```

请按如下格式进行输入：`( ( ( 2 * 3 ) / ( 2 - 1 ) ) + ( 3 * ( 4 - 1 ) ) )`

输出结果：`2 3 * 2 1 - / 3 4 1 - * +`

### 11. 后序表达式计算 EvaluatePostfix

```java
public class EvaluatePostfix {
    public static void main(String[] args) {
        Stack<String> ops = new Stack<>();
        Stack<Double> vals = new Stack<>();
        while (!StdIn.isEmpty()) {
            String s = StdIn.readString();
            if (s.equals("+")) {
                double val = vals.pop();
                vals.push(vals.pop() + val);
            } else if (s.equals("-")) {
                double val = vals.pop();
                vals.push(vals.pop() - val);
            } else if (s.equals("*")) {
                double val = vals.pop();
                vals.push(vals.pop() * val);
            } else if (s.equals("/")) {
                double val = vals.pop();
                vals.push(vals.pop() / val);
            } else vals.push(Double.parseDouble(s));
        }
        StdOut.println(vals.pop());
    }
}
```

### 12. 有静态方法 copy() 的 Stack 用例

这里使用了迭代器和 foreach 循环。

```java
public static Stack<String> copy(Stack<String> stack) {
    Iterator<String> iterator = stack.iterator();
    Stack<String> res = new Stack<>();
    Stack<String> temp = new Stack<>();
    if (iterator.hasNext()) temp.push(iterator.next());
    for (String s : stack) res.push(s);
    return res;
}
```

### 13. 不可能产生的出列返回值

根据队列先进先出的特点，所以只有 a 是可能实现的。

### 14. 类 ResizingArrayQueueOfStrings

```java
public class ResizingArrayQueueOfStrings {
    private String[] a;
    private int N;
    private int first;
    private int last;

    public ResizingArrayQueueOfStrings() {
        a = new String[2];
        N = 0;
        first = 0;
        last = 0;
    }

    public boolean isEmpty() {
        return N == 0;
    }

    public int size() {
        return N;
    }

    private void resize(int max) {
        String[] temp = new String[max];
        for (int i = 0; i < N; i++) {
            temp[i] = a[(first + 1) % a.length];
        }
        a = temp;
        first = 0;
        last = N;
    }

    public void enqueue(String s) {
        if (N == a.length) resize(2 * a.length);
        a[last++] = s;
        N++;
    }

    public String dequeue() {
        String s = a[first];
        a[first] = null;
        N--;
        first++;
        if (N > 0 && N == a.length / 4) resize(a.length / 2);
        return s;
    }
}
```

### 15. Queue 用例

```java
public static void main(String[] args) {
    Queue<String> q = new Queue<>();
    int k = Integer.parseInt(args[0]);
    while (!StdIn.isEmpty()) {
        String s = StdIn.readString();
        q.enqueue(s);
    }
    for (int i = 0; i < (q.size() + 1 - k); i++) {
        if (i == q.size() - k) {
            StdOut.println(q.dequeue());
            break;
        }
        q.dequeue();
    }
}
```

### 16. 静态方法 readDates()

```java
public static Date[] readDates() {
    Queue<Date> q = new Queue<>();
    while (!StdIn.isEmpty()) {
        String[] str = StdIn.readString().split("/");
        int month = Integer.parseInt(str[0]);
        int day = Integer.parseInt(str[1]);
        int year = Integer.parseInt(str[2]);
        q.enqueue(new Date(year, month, day));
    }
    int N = q.size();
    Date[] a = new Date[N];
    for (int i = 0; i < N; i++)
        a[i] = q.dequeue();
    return a;
}
```

### 17. 对 Transaction 的实现

我在 Date 里写了一个静态方法 readDate(String s) 用来对读入的字符串进行处理，返回一个 Date 对象：

```java
public static Date readDate(String s) {
    String[] str = s.split("/");
    int month = Integer.parseInt(str[0]);
    int day = Integer.parseInt(str[1]);
    int year = Integer.parseInt(str[2]);
    return new Date(year, month, day);
}
```

Transaction 中的静态方法 readTransactions() 是这样写的，同上面的 readDates() 方法类似，比它稍微复杂了一点：

```java
public static Transaction[] readTransactions() {
    Queue<Transaction> q = new Queue<>();
    while (!StdIn.isEmpty()) {
        String name = StdIn.readString();
        Date date = Date.readDate(StdIn.readString());
        double amount = Double.parseDouble(StdIn.readString());
        q.enqueue(new Transaction(name, date, amount));
    }
    int N = q.size();
    Transaction[] a = new Transaction[N];
    for (int i = 0; i < N; i++)
        a[i] = q.dequeue();
    return a;
}
```

## 链表练习

### 18. 语句的效果

x.next 指向了 x.next.next ，之前的 x.next 就变成了孤儿，会被 Java 回收，相当于删除了 x 的后续结点。

### 19. 删除链表的尾结点

```java
Node current = first;
while (current.next.next != null) {
    current = current.next;
}
current.next = null;
```

### 20. 方法 delete()

```java
public void delete(int k) {
    Node current = first;
    for (int i = 0; i < k - 2; i++) {
        current = current.next;
        if (current == null) break;
    }
    if (current.next != null) current.next = current.next.next;
}
```

### 21. 方法 find()

```java
public boolean find(Chain<String> c, String s) {
    // 前提是链表中的 first 属性为 public
    Node current = c.first;
    for (int i = 0; i < c.size(); i++) {
        if (s.equals(current.item)) return true;
        if (i != c.size() - 1) current = current.next;
    }
    return false;
}
```

### 22. 代码做了什么

将 t 插入 x 与 x.next 之间。

### 23. 为什么效果不同

x.next 已经替换为 t ，x.next 指向 t 本身，不再与曾经的后续结点有关。

### 24. 方法 removeAfter()

```java
public void removeAfter(Node node) {
    if (node != null && node.next != null)
        node.next = null;
}
```

### 25. 方法 insertAfter()

```java
public void insertAfter(Node node_1, Node node_2) {
    if (node_1 != null && node_2 != null) {
        node_2.next = node_1.next;
        node_1.next = node_2;
    }
}
```

### 26. 方法 remove()

```java
public void remove(Chain<String> c, String s) {
    Node current = c.first;
    for (int i = 0; i < c.size(); i++) {
        if (current.next == null) break;
        if (item.equals(current.next.item)) current.next = current.next.next;
    }
}
```

---
