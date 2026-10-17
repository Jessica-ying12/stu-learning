# do while 循环学习笔记

## 1. 基本语法

```java
do {
    执行循环语句
} while (条件表达式);  // 注意末尾有分号
```

**执行流程：**

```
执行循环体 → 判断条件 → true → 再次执行循环体
                │
                └→ false → 跳出循环
```

> do while 是**先执行后判断**，循环体**至少执行一次**。

---

## 2. while vs do while

| 对比 | while | do while |
|------|-------|----------|
| 执行顺序 | 先判断，后执行 | 先执行，后判断 |
| 最少执行次数 | 0 次 | 1 次 |
| 适用场景 | 可能不需要执行的循环 | 至少需要执行一次的循环 |

```java
// while：条件一开始为 false，循环体不执行
int n = 101;
while (n <= 100) {
    System.out.println(n);  // 不会执行
}

// do while：条件一开始为 false，循环体仍执行一次
int n = 101;
do {
    System.out.println(n);  // 会执行一次，输出 101
} while (n <= 100);
```

---

## 3. 示例：累加 1 到 100

```java
int sum = 0;
int n = 1;
do {
    sum = sum + n;
    n++;
} while (n <= 100);
System.out.println(sum);  // 5050
```

**执行过程：**
- 先执行：n=1，sum=1，n 变为 2
- 判断：2 <= 100 → true，继续
- ...
- 先执行：n=100，sum=5050，n 变为 101
- 判断：101 <= 100 → false，退出

---

## 4. 练习：计算 m 到 n 的和

```java
int sum = 0;
int m = 20;
int n = 100;
do {
    sum = sum + m;
    m++;
} while (m <= n);
System.out.println(sum);  // 4860
```

---

## 5. 关键总结

| 要点 | 说明 |
|------|------|
| 语法 | `do { 循环体 } while (条件);` |
| 执行特点 | 先执行，后判断；至少执行一次 |
| 分号 | `while (条件)` 后面必须有 `;` |
| 使用场景 | 需要先执行一次再判断的逻辑（如用户输入验证） |
