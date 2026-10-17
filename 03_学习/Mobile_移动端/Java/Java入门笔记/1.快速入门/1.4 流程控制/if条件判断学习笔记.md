# if 条件判断学习笔记

## 1. 基本语法

```java
if (条件) {
    // 条件为 true 时执行
}
```

```java
int n = 70;
if (n >= 60) {
    System.out.println("及格了");
}
```

> 始终使用花括号 `{}`，即使只有一行语句。省略花括号容易在后续修改时引入 bug。

---

## 2. if...else

```java
if (n >= 60) {
    System.out.println("及格了");
} else {
    System.out.println("挂科了");
}
```

- `else` 不是必须的
- 条件为 true 执行 if 块，为 false 执行 else 块

---

## 3. if...else if...else 串联

```java
if (n >= 90) {
    System.out.println("优秀");
} else if (n >= 60) {
    System.out.println("及格了");
} else {
    System.out.println("挂科了");
}
```

### ⚠️ 判断顺序很重要

```java
// ❌ 错误：n=100 时输出"及格了"而不是"优秀"
if (n >= 60) {
    System.out.println("及格了");
} else if (n >= 90) {
    System.out.println("优秀");  // 永远不会执行到这里
}

// ✅ 正确：从大到小判断
if (n >= 90) { ... }
else if (n >= 60) { ... }
else { ... }
```

**原则：** if 从上到下执行，一旦匹配就不再判断后续条件。所以要把范围大（更严格）的条件放前面。

---

## 4. 注意事项

### 边界条件

```java
// n=90 时输出"及格了"，因为 > 不包含等于
if (n > 90) { ... }    // 不包含 90
if (n >= 90) { ... }   // 包含 90
```

> 仔细区分 `>` 和 `>=`，明确边界值该归哪一档。

### 浮点数判断不能用 ==

```java
double x = 1 - 9.0 / 10;

// ❌ 错误
if (x == 0.1) { ... }

// ✅ 正确：用差值绝对值判断
if (Math.abs(x - 0.1) < 0.00001) { ... }
```

---

## 5. 引用类型判断相等

### == vs equals()

| 运算 | 含义 | 适用场景 |
|------|------|----------|
| `==` | 判断是否是同一个对象（引用相等） | 基本类型比较值 |
| `.equals()` | 判断内容是否相等 | 引用类型比较内容 |

```java
String s1 = "hello";
String s2 = "HELLO".toLowerCase();

s1 == s2        // false（不同对象）
s1.equals(s2)   // true（内容相同）
```

### 避免 NullPointerException

```java
String s1 = null;

// ❌ s1 为 null 时会报错
s1.equals("hello")

// ✅ 方法一：先判断 null（利用短路运算）
if (s1 != null && s1.equals("hello")) { ... }

// ✅ 方法二：把确定不为 null 的对象放前面
if ("hello".equals(s1)) { ... }
```

---

## 6. 练习：BMI 计算

```java
import java.util.Scanner;

public class Main {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        System.out.print("体重(kg): ");
        double weight = scanner.nextDouble();
        System.out.print("身高(m): ");
        double height = scanner.nextDouble();

        double bmi = weight / (height * height);

        if (bmi < 18.5) {
            System.out.println("过轻");
        } else if (bmi < 25) {
            System.out.println("正常");
        } else if (bmi < 28) {
            System.out.println("过重");
        } else if (bmi < 32) {
            System.out.println("肥胖");
        } else {
            System.out.println("非常肥胖");
        }
    }
}
```

---

## 7. 关键总结

| 要点 | 说明 |
|------|------|
| 花括号 | 始终写 `{}`，不要省略 |
| 判断顺序 | 串联 if 时，严格条件放前面 |
| 边界条件 | 注意 `>` 和 `>=` 的区别 |
| 浮点数比较 | 不用 `==`，用差值绝对值 |
| 引用类型比较 | 用 `.equals()` 比较内容，不用 `==` |
| 防 NPE | 用 `&&` 短路或把非 null 对象放前面调用 equals |
