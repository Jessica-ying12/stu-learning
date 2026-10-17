# switch 多重选择学习笔记

## 1. 基本语法

根据表达式的结果跳转到匹配的 case 执行：

```java
int option = 1;
switch (option) {
case 1:
    System.out.println("Selected 1");
    break;
case 2:
    System.out.println("Selected 2");
    break;
case 3:
    System.out.println("Selected 3");
    break;
default:
    System.out.println("Selected other");
    break;
}
```

- `default`：没有匹配到任何 case 时执行
- `break`：跳出 switch，终止执行

---

## 2. 穿透性（fall-through）

case 语句**没有花括号**，漏写 `break` 会从匹配处一直执行到底：

```java
int option = 2;
switch (option) {
case 1:
    System.out.println("Selected 1");
case 2:
    System.out.println("Selected 2");  // ← 从这里开始
case 3:
    System.out.println("Selected 3");  // ← 继续执行
default:
    System.out.println("Selected other"); // ← 继续执行
}
// 输出：Selected 2、Selected 3、Selected other
```

> ⚠️ 任何时候都不要忘记写 `break`！

### 利用穿透合并 case

```java
case 2:
case 3:
    System.out.println("Selected 2 or 3");
    break;
```

---

## 3. switch 支持的类型

| 类型 | 说明 |
|------|------|
| 整型 | `byte`、`short`、`int`、`char` |
| 字符串 | `String`（比较内容相等） |
| 枚举 | `enum` 类型 |

```java
String fruit = "apple";
switch (fruit) {
case "apple":
    System.out.println("Selected apple");
    break;
case "pear":
    System.out.println("Selected pear");
    break;
default:
    System.out.println("No fruit selected");
    break;
}
```

---

## 4. IDE 编译检查建议

| 检查项 | 作用 |
|--------|------|
| missing 'default' case | 缺少 default 时警告 |
| case fall-through | 某个 case 缺少 break 时警告 |

> 建议在 IDE 中开启这两个警告，避免逻辑错误。

---

## 5. switch 表达式（Java 14+）

### 新语法：箭头 `->`

```java
String fruit = "apple";
switch (fruit) {
case "apple" -> System.out.println("Selected apple");
case "pear" -> System.out.println("Selected pear");
case "mango" -> {
    System.out.println("Selected mango");
    System.out.println("Good choice!");
}
default -> System.out.println("No fruit selected");
}
```

| 对比 | 传统 switch | 新 switch 表达式 |
|------|-------------|-----------------|
| 分隔符 | `:` | `->` |
| break | 必须手动写 | 不需要，无穿透 |
| 多条语句 | 直接写 | 用 `{}` 包裹 |
| 合并 case | 穿透写法 | `case "a", "b" ->` |

### 作为表达式返回值

```java
String fruit = "apple";
int opt = switch (fruit) {
    case "apple" -> 1;
    case "pear", "mango" -> 2;
    default -> 0;
};  // 注意分号
System.out.println("opt = " + opt);  // opt = 1
```

### yield 关键字

当 case 块有复杂逻辑时，用 `yield` 返回值：

```java
int opt = switch (fruit) {
    case "apple" -> 1;
    case "pear", "mango" -> 2;
    default -> {
        int code = fruit.hashCode();
        yield code;  // 返回值
    }
};
```

---

## 6. switch vs if...else if

| 场景 | 推荐 |
|------|------|
| 多个 `==` 等值判断 | `switch` 更清晰 |
| 范围判断（>=、<） | `if...else if` |
| 复杂条件组合 | `if...else if` |

---

## 7. 关键总结

| 要点 | 说明 |
|------|------|
| 传统 switch | 必须写 `break`，否则穿透 |
| default | 始终写上，处理未匹配情况 |
| 支持类型 | 整型、字符串、枚举（不支持浮点和 long） |
| 新语法（Java 14+） | 用 `->` 替代 `:`，无穿透，可返回值 |
| yield | switch 表达式中复杂块的返回值关键字 |
| case 顺序 | 不影响逻辑，但建议自然顺序便于阅读 |
