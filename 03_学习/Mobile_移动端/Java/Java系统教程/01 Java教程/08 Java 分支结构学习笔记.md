## 一、分支结构概述

### 1.1 为什么需要分支结构

- 顺序结构只能顺序执行，不能进行判断和选择
- 需要根据不同条件执行不同的代码

### 1.2 Java 的两种分支结构

1. **if 语句**
2. **switch 语句**

---

## 二、if 语句

### 2.1 基本 if 语句

#### 语法格式

```java
if (布尔表达式) {
    // 如果布尔表达式为 true 将执行的语句
}
```

#### 执行流程

- 如果布尔表达式的值为 `true`，执行 if 语句中的代码块
- 否则跳过 if 语句块，执行后面的代码

#### 示例代码

```java
public class Test {
    public static void main(String args[]) {
        int x = 10;
        
        if (x < 20) {
            System.out.print("这是 if 语句");
        }
    }
}
```

**输出**：

```
这是 if 语句
```

---

## 三、if...else 语句

### 3.1 语法格式

```java
if (布尔表达式) {
    // 如果布尔表达式的值为 true
} else {
    // 如果布尔表达式的值为 false
}
```

### 3.2 执行流程

- 如果布尔表达式为 `true`，执行 if 块
- 如果布尔表达式为 `false`，执行 else 块

### 3.3 示例代码

```java
public class Test {
    public static void main(String args[]) {
        int x = 30;
        
        if (x < 20) {
            System.out.print("这是 if 语句");
        } else {
            System.out.print("这是 else 语句");
        }
    }
}
```

**输出**：

```
这是 else 语句
```

---

## 四、if...else if...else 语句

### 4.1 语法格式

```java
if (布尔表达式1) {
    // 如果布尔表达式1的值为 true 执行代码
} else if (布尔表达式2) {
    // 如果布尔表达式2的值为 true 执行代码
} else if (布尔表达式3) {
    // 如果布尔表达式3的值为 true 执行代码
} else {
    // 如果以上布尔表达式都不为 true 执行代码
}
```

### 4.2 重要规则

1. `if` 语句至多有 1 个 `else` 语句，`else` 语句在所有 `else if` 语句之后
2. `if` 语句可以有若干个 `else if` 语句，它们必须在 `else` 语句之前
3. 一旦其中一个 `else if` 语句检测为 `true`，其他的 `else if` 以及 `else` 语句都将跳过执行


### 4.3 示例代码

```java
public class Test {
    public static void main(String args[]) {
        int x = 30;
        
        if (x == 10) {
            System.out.print("Value of X is 10");
        } else if (x == 20) {
            System.out.print("Value of X is 20");
        } else if (x == 30) {
            System.out.print("Value of X is 30");
        } else {
            System.out.print("这是 else 语句");
        }
    }
}
```

**输出**：

```
Value of X is 30
```

---

## 五、嵌套的 if...else 语句

### 5.1 语法格式

```java
if (布尔表达式1) {
    // 如果布尔表达式1的值为 true 执行代码
    if (布尔表达式2) {
        // 如果布尔表达式2的值为 true 执行代码
    }
}
```

### 5.2 特点

- 可以在 `if` 或 `else if` 语句中使用另一个 `if` 或 `else if` 语句
- 可以嵌套 `else if...else` 语句

### 5.3 示例代码

```java
public class Test {
    public static void main(String args[]) {
        int x = 30;
        int y = 10;
        
        if (x == 30) {
            if (y == 10) {
                System.out.print("X = 30 and Y = 10");
            }
        }
    }
}
```

**输出**：

```
X = 30 and Y = 10
```

---

## 六、switch 语句

### 6.1 语法格式

```java
switch (expression) {
    case value1:
        // 语句
        break;  // 可选
    case value2:
        // 语句
        break;  // 可选
    // 可以有任意数量的 case 语句
    default:  // 可选
        // 语句
}
```

### 6.2 switch 语句规则

#### 1. 变量类型限制

- `byte`、`short`、`int`、`char`
- 从 Java SE 7 开始，支持 `String` 类型
- `case` 标签必须为字符串常量或字面量

#### 2. case 语句规则

- 可以拥有多个 `case` 语句
- 每个 `case` 后面跟一个要比较的值和冒号
- `case` 语句中的值必须与变量的数据类型相同
- 只能是常量或字面常量

#### 3. break 语句

- 当变量的值与 `case` 语句的值相等时，执行 `case` 之后的语句
- 直到 `break` 语句出现才会跳出 `switch` 语句
- 如果没有 `break` 语句，程序会继续执行下一条 `case` 语句（穿透）

#### 4. default 分支

- `switch` 语句可以包含一个 `default` 分支
- 该分支必须是 `switch` 语句的最后一个分支
- 在没有 `case` 语句的值和变量值相等时执行
- `default` 分支不需要 `break` 语句

### 6.3 示例代码

```java
public class Test {
    public static void main(String args[]) {
        char grade = 'C';
        
        switch (grade) {
            case 'A':
                System.out.println("优秀");
                break;
            case 'B':
            case 'C':
                System.out.println("良好");
                break;
            case 'D':
                System.out.println("及格");
            case 'F':
                System.out.println("你需要继续努力");
                break;
            default:
                System.out.println("无效等级");
        }
        System.out.println("你的等级是 " + grade);
    }
}
```

**输出**：

```
良好
你的等级是 C
```

### 6.4 case 穿透示例

```java
public class Test {
    public static void main(String args[]) {
        int day = 2;
        
        switch (day) {
            case 1:
                System.out.println("星期一");
                break;
            case 2:
                System.out.println("星期二");
                // 没有 break，会继续执行
            case 3:
                System.out.println("星期三");
                break;
            default:
                System.out.println("其他");
        }
    }
}
```

**输出**：

```
星期二
星期三
```

---

## 七、if 和 switch 的对比

### 7.1 对比表

| 特性      | if...else | switch                        |
| ------- | --------- | ----------------------------- |
| 判断类型    | 任何布尔表达式   | 等值判断                          |
| 支持的数据类型 | 任何类型      | byte、short、int、char、String、枚举 |
| 范围判断    | 支持        | 不支持                           |
| 代码可读性   | 多条件时较差    | 多等值判断时较好                      |
| 性能      | 顺序判断      | 跳转表（可能更快）                     |

### 7.2 使用场景

#### 使用 if...else

```java
// 范围判断
if (score >= 90) {
    System.out.println("优秀");
} else if (score >= 80) {
    System.out.println("良好");
} else if (score >= 60) {
    System.out.println("及格");
} else {
    System.out.println("不及格");
}

// 复杂条件
if (age > 18 && hasLicense) {
    System.out.println("可以开车");
}
```

#### 使用 switch

```java
// 等值判断（多个固定值）
switch (month) {
    case 1:
    case 3:
    case 5:
    case 7:
    case 8:
    case 10:
    case 12:
        System.out.println("31天");
        break;
    case 4:
    case 6:
    case 9:
    case 11:
        System.out.println("30天");
        break;
    case 2:
        System.out.println("28或29天");
        break;
    default:
        System.out.println("无效月份");
}
```

---

## 八、分支结构最佳实践

### 8.1 if 语句最佳实践

#### 1. 使用大括号

```java
// ❌ 不推荐：没有大括号
if (condition)
    statement1;
    statement2;  // 不属于 if 块

// ✅ 推荐：使用大括号
if (condition) {
    statement1;
    statement2;
}
```

#### 2. 避免过深的嵌套

```java
// ❌ 不推荐：嵌套过深
if (condition1) {
    if (condition2) {
        if (condition3) {
            // 代码
        }
    }
}

// ✅ 推荐：提前返回
if (!condition1) return;
if (!condition2) return;
if (!condition3) return;
// 代码
```

#### 3. 条件表达式的可读性

```java
// ❌ 不推荐：复杂条件
if (age > 18 && age < 65 && hasJob && !isStudent) {
    // 代码
}

// ✅ 推荐：使用变量提高可读性
boolean isWorkingAge = age > 18 && age < 65;
boolean isEmployed = hasJob && !isStudent;
if (isWorkingAge && isEmployed) {
    // 代码
}
```

### 8.2 switch 语句最佳实践

#### 1. 始终包含 default

```java
// ✅ 推荐：包含 default
switch (value) {
    case 1:
        // 代码
        break;
    case 2:
        // 代码
        break;
    default:
        System.out.println("未知值");
}
```

#### 2. 不要忘记 break

```java
// ❌ 错误：忘记 break
switch (day) {
    case 1:
        System.out.println("星期一");
    case 2:  // 会继续执行
        System.out.println("星期二");
        break;
}

// ✅ 正确：添加 break
switch (day) {
    case 1:
        System.out.println("星期一");
        break;
    case 2:
        System.out.println("星期二");
        break;
}
```

#### 3. 利用 case 穿透

```java
// ✅ 合理使用穿透
switch (month) {
    case 12:
    case 1:
    case 2:
        System.out.println("冬季");
        break;
    case 3:
    case 4:
    case 5:
        System.out.println("春季");
        break;
    // ...
}
```

---

## 九、常见错误和陷阱

### 9.1 if 语句常见错误

```java
// ❌ 错误 1：使用 = 而不是 ==
if (x = 10) {  // 编译错误
    // 代码
}

// ✅ 正确
if (x == 10) {
    // 代码
}

// ❌ 错误 2：空语句
if (condition);  // 注意分号
    statement;   // 总是执行

// ✅ 正确
if (condition) {
    statement;
}

// ❌ 错误 3：浮点数比较
if (0.1 + 0.2 == 0.3) {  // 可能为 false
    // 代码
}

// ✅ 正确：使用误差范围
if (Math.abs((0.1 + 0.2) - 0.3) < 0.0001) {
    // 代码
}
```

### 9.2 switch 语句常见错误

```java
// ❌ 错误 1：case 值不是常量
int x = 10;
switch (value) {
    case x:  // 编译错误
        break;
}

// ✅ 正确：使用常量
final int X = 10;
switch (value) {
    case X:
        break;
}

// ❌ 错误 2：类型不匹配
switch (value) {
    case 1.5:  // 编译错误（不支持 double）
        break;
}

// ❌ 错误 3：重复的 case
switch (value) {
    case 1:
        break;
    case 1:  // 编译错误
        break;
}
```

---

## 十、重要知识点总结

### 10.1 if 语句要点

1. 布尔表达式必须返回 `boolean` 类型
2. `else if` 可以有多个，`else` 最多一个
3. 一旦某个条件为 `true`，后续条件不再判断
4. 可以嵌套使用，但避免过深嵌套
5. 始终使用大括号，提高代码可读性

### 10.2 switch 语句要点

1. 支持类型：`byte`、`short`、`int`、`char`、`String`（Java 7+）、枚举
2. `case` 值必须是常量或字面量
3. `break` 用于跳出 `switch`，没有 `break` 会穿透
4. `default` 是可选的，但建议总是包含
5. `default` 必须放在最后

### 10.3 选择建议

|场景|推荐|
|---|---|
|范围判断|if...else|
|复杂条件|if...else|
|多个等值判断|switch|
|字符串匹配|switch（Java 7+）|
|布尔逻辑|if...else|

### 10.4 性能考虑

- `switch` 在多个等值判断时可能比 `if...else` 更快（使用跳转表）
- `if...else` 是顺序判断，条件越多越慢
- 将最可能的条件放在前面

### 10.5 代码风格

```java
// ✅ 推荐的代码风格
if (condition) {
    // 代码
} else if (condition2) {
    // 代码
} else {
    // 代码
}

switch (value) {
    case 1:
        // 代码
        break;
    case 2:
        // 代码
        break;
    default:
        // 代码
}
```