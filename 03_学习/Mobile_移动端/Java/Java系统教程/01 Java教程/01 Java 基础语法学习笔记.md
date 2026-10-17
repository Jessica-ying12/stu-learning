## 一、Java 核心概念

### 1.1 基本组成部分

Java 程序由一系列对象的集合组成，这些对象通过调用彼此的方法来协同工作。

- **对象（Object）**：类的实例，具有状态和行为
    - 示例：一条狗是对象
    - 状态：颜色、名字、品种
    - 行为：摇尾巴、叫、吃
- **类（Class）**：描述一类对象行为和状态的模板
- **方法（Method）**：对象的行为，完成逻辑运算、数据修改等操作
- **实例变量（Instance Variables）**：对象的状态由实例变量的值决定

---

## 二、第一个 Java 程序

### 2.1 Hello World 示例

```java
public class MyFirstJavaProgram {
    /* 第一个Java程序
     * 它将打印字符串 Hello World
     */
    public static void main(String[] args) {
        System.out.println("Hello World"); // 打印 Hello World
    }
}
```

### 2.2 运行步骤

1. 保存文件为 `MyFirstJavaProgram.java`
2. 打开命令行，进入文件所在目录
3. 编译：`javac MyFirstJavaProgram.java`
4. 运行：`java MyFirstJavaProgram`
5. 输出：`Hello World`

---

## 三、基本语法规则

### 3.1 命名规范

- **大小写敏感**：`Hello` 和 `hello` 是不同的标识符
- **类名**：首字母大写，多个单词每个首字母大写
    - 示例：`MyFirstJavaClass`
- **方法名**：首字母小写，后续单词首字母大写
    - 示例：`getUserName()`
- **源文件名**：必须与类名相同，后缀为 `.java`
- **主方法入口**：所有程序从 `public static void main(String[] args)` 开始执行

---

## 四、标识符规则

### 4.1 命名要求

- 以字母（A-Z 或 a-z）、美元符（$）或下划线（ _ ）开始
- 后续字符可以是字母、数字、美元符或下划线
- 关键字不能用作标识符
- 大小写敏感

### 4.2 示例

**合法标识符**：

- `age`
- `$salary`
- `_value`
- `__1_value`

**非法标识符**：

- `123abc`（数字开头）
- `-salary`（特殊字符）

---

## 五、修饰符

使用修饰符来修饰类中方法和属性

### 5.1 访问控制修饰符

- `default`
- `public`
- `protected`
- `private`

### 5.2 非访问控制修饰符

- `final`
- `abstract`
- `static`
- `synchronized`
- `volatile`

---

## 六、变量类型

Java 中主要有以下几种变量：

1. **局部变量**：方法内部定义的变量
2. **类变量（静态变量）**：用 `static` 修饰的变量
3. **成员变量（非静态变量）**：类中定义的实例变量

---

## 七、数组

数组是存储在堆上的对象，可以保存多个同类型变量。

---

## 八、枚举（Enum）

### 8.1 概念

Java 5.0 引入，限制变量只能是预先设定好的值，减少代码中的 bug。

### 8.2 示例

```java
class FreshJuice {
    enum FreshJuiceSize { SMALL, MEDIUM, LARGE }
    FreshJuiceSize size;
}

public class FreshJuiceTest {
    public static void main(String[] args) {
        FreshJuice juice = new FreshJuice();
        juice.size = FreshJuice.FreshJuiceSize.MEDIUM;
    }
}
```

**注意**：枚举可以单独声明或在类中声明，可以包含方法、变量和构造函数。

---

## 九、Java 关键字

### 9.1 常用关键字表

| 关键字            | 描述                |
| -------------- | ----------------- |
| `abstract`     | 抽象方法、抽象类的修饰符      |
| `boolean`      | 布尔数据类型            |
| `break`        | 跳出循环或代码段          |
| `byte`         | 8-bit 有符号数据类型     |
| `case`         | switch 语句的条件      |
| `catch`        | 捕捉异常信息            |
| `char`         | 16-bit Unicode 字符 |
| `class`        | 定义类               |
| `continue`     | 跳过循环体剩余部分         |
| `default`      | switch 语句默认分支     |
| `do`           | do-while 循环       |
| `double`       | 64-bit 双精度浮点数     |
| `else`         | if 条件不成立时的分支      |
| `enum`         | 枚举类型              |
| `extends`      | 表示继承关系            |
| `final`        | 常量、不可重写方法、不可继承类   |
| `finally`      | 无论是否异常都执行的代码块     |
| `float`        | 32-bit 单精度浮点数     |
| `for`          | for 循环            |
| `if`           | 条件语句              |
| `implements`   | 实现接口              |
| `import`       | 导入类               |
| `instanceof`   | 测试对象类型            |
| `int`          | 32 位整型            |
| `interface`    | 定义接口              |
| `long`         | 64 位整型            |
| `native`       | 非 Java 代码实现       |
| `new`          | 创建新对象             |
| `package`      | 定义包               |
| `private`      | 私有访问修饰符           |
| `protected`    | 受保护访问修饰符          |
| `public`       | 公共访问修饰符           |
| `return`       | 方法返回值             |
| `short`        | 16 位整型            |
| `static`       | 静态成员              |
| `super`        | 调用父类              |
| `switch`       | 选择语句              |
| `synchronized` | 同步代码块             |
| `this`         | 当前实例引用            |
| `throw`        | 抛出异常              |
| `throws`       | 声明方法可能抛出的异常       |
| `transient`    | 不序列化的字段           |
| `try`          | 异常处理代码块           |
| `void`         | 无返回值              |
| `volatile`     | 多线程共享字段           |
| `while`        | while 循环          |

---

## 十、注释

### 10.1 单行注释

```java
// 这是单行注释
```

### 10.2 多行注释

```java
/* 这是多行注释
 * 可以写多行
 */
```

### 10.3 示例

```java
public class MyFirstJavaProgram {
    /* 这是第一个Java程序
     * 它将打印Hello World
     * 这是一个多行注释的示例
     */
    public static void main(String[] args) {
        // 这是单行注释的示例
        /* 这个也是单行注释的示例 */
        System.out.println("Hello World");
    }
}
```

---

## 十一、空行

空白行或只有注释的行会被 Java 编译器忽略。

---

## 十二、继承（Inheritance）

### 12.1 概念

- 一个类可以由其他类派生
- 重用已存在类的方法和属性
- **超类（Super Class）**：被继承的类
- **子类（Subclass）**：派生类

### 12.2 优势

- 代码复用
- 避免重复编写相同代码
- 建立类之间的层次关系

---

## 十三、接口（Interface）

### 13.1 概念

- 对象间相互通信的协议
- 只定义方法签名，不实现具体逻辑
- 具体实现由派生类完成

### 13.2 作用

- 在继承中扮演重要角色
- 定义规范和契约
- 实现多态性

---

## 学习要点总结

1. **语法严格**：大小写敏感，命名规范明确
2. **面向对象**：类、对象、继承、接口是核心
3. **类型安全**：强类型语言，变量类型明确
4. **异常处理**：try-catch-finally 机制
5. **访问控制**：public、private、protected 控制访问权限
6. **代码规范**：注释、命名、格式化很重要