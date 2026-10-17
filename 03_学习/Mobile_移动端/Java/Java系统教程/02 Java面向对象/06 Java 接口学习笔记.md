## 一、接口概述

### 1.1 什么是接口？

**接口(Interface)**：一个抽象类型，是抽象方法的集合，使用 `interface` 关键字声明。

### 1.2 接口的特点

| 特点 | 说明 |
|------|------|
| **抽象方法集合** | 包含类要实现的方法 |
| **不能实例化** | 但可以被实现 |
| **多重继承** | 一个类可以实现多个接口 |
| **规范定义** | 定义行为规范，不关心实现 |

### 1.3 接口的作用

- ✅ 定义规范和标准
- ✅ 实现多重继承
- ✅ 降低耦合度
- ✅ 支持多态性

---

## 二、接口与类的对比

### 2.1 相似点

| 相似点 | 说明 |
|--------|------|
| 多个方法 | 接口可以有多个方法 |
| 文件格式 | 保存在 `.java` 文件中 |
| 字节码 | 编译后生成 `.class` 文件 |
| 包结构 | 必须在匹配的目录结构中 |

### 2.2 区别

| 特性 | 类 | 接口 |
|------|----|----|
| **实例化** | 可以实例化 | 不能实例化 |
| **构造方法** | 有构造方法 | 没有构造方法 |
| **方法** | 可以有具体实现 | 只能有抽象方法（Java 8前） |
| **成员变量** | 可以有各种变量 | 只能有 static final 常量 |
| **继承** | 单继承（extends） | 多继承（extends） |
| **实现** | 不适用 | 被类实现（implements） |

---

## 三、接口的声明

### 3.1 基本语法

```java
[可见度] interface 接口名称 [extends 其他接口名] {
    // 常量声明（public static final）
    // 抽象方法声明（public abstract）
}
```

### 3.2 简单示例

```java
/* 文件名: NameOfInterface.java */
import java.lang.*;

public interface NameOfInterface {
    // 常量（隐式 public static final）
    int MAX_VALUE = 100;
    String DEFAULT_NAME = "Default";
    
    // 抽象方法（隐式 public abstract）
    void method1();
    int method2(String param);
}
```

### 3.3 接口的特性

1. **隐式抽象**：接口是隐式抽象的，不需要 `abstract` 关键字
2. **方法隐式抽象**：接口中的方法隐式为 `public abstract`
3. **常量隐式修饰**：变量隐式为 `public static final`

```java
public interface Example {
    // 以下两种写法等价
    void method1();
    public abstract void method1();
    
    // 以下两种写法等价
    int MAX = 100;
    public static final int MAX = 100;
}
```

---

## 四、接口的实现

### 4.1 基本语法

```java
public class 类名 implements 接口1, 接口2, ... {
    // 实现接口中的所有方法
}
```

### 4.2 完整示例

#### 1. 定义接口

```java
/* 文件名: Animal.java */
public interface Animal {
    void eat();
    void travel();
}
```

#### 2. 实现接口

```java
/* 文件名: MammalInt.java */
public class MammalInt implements Animal {
    
    @Override
    public void eat() {
        System.out.println("Mammal eats");
    }
    
    @Override
    public void travel() {
        System.out.println("Mammal travels");
    }
    
    // 类自己的方法
    public int noOfLegs() {
        return 0;
    }
    
    public static void main(String args[]) {
        MammalInt m = new MammalInt();
        m.eat();
        m.travel();
    }
}
```

**输出：**
```
Mammal eats
Mammal travels
```

---

## 五、接口实现的规则

### 5.1 方法重写规则

| 规则 | 说明 |
|------|------|
| **异常处理** | 不能抛出比接口方法更多的强制性异常 |
| **方法名** | 必须保持一致 |
| **返回值** | 必须相同或兼容（协变返回类型） |
| **访问修饰符** | 必须是 public |
| **抽象类** | 抽象类可以不实现接口方法 |

### 5.2 实现规则

```java
// ✅ 正确：实现所有方法
public class Dog implements Animal {
    @Override
    public void eat() {
        System.out.println("Dog eats");
    }
    
    @Override
    public void travel() {
        System.out.println("Dog runs");
    }
}

// ✅ 正确：抽象类可以不实现
public abstract class AbstractAnimal implements Animal {
    // 可以不实现接口方法
}

// ❌ 错误：普通类必须实现所有方法
public class Cat implements Animal {
    // 编译错误：没有实现所有方法
}
```

### 5.3 多接口实现

```java
public interface Flyable {
    void fly();
}

public interface Swimmable {
    void swim();
}

// 一个类可以实现多个接口
public class Duck implements Animal, Flyable, Swimmable {
    @Override
    public void eat() {
        System.out.println("Duck eats");
    }
    
    @Override
    public void travel() {
        System.out.println("Duck travels");
    }
    
    @Override
    public void fly() {
        System.out.println("Duck flies");
    }
    
    @Override
    public void swim() {
        System.out.println("Duck swims");
    }
}
```

---

## 六、接口的继承

### 6.1 单继承

```java
// 父接口
public interface Sports {
    void setHomeTeam(String name);
    void setVisitingTeam(String name);
}

// 子接口继承父接口
public interface Football extends Sports {
    void homeTeamScored(int points);
    void visitingTeamScored(int points);
    void endOfQuarter(int quarter);
}
```

**说明**：实现 Football 接口的类需要实现 5 个方法（2个继承 + 3个自己的）

### 6.2 多重继承

接口支持多重继承，类不支持。

```java
public interface Event {
    void start();
    void end();
}

public interface Sports {
    void setHomeTeam(String name);
    void setVisitingTeam(String name);
}

// 接口可以继承多个接口
public interface Hockey extends Sports, Event {
    void homeGoalScored();
    void visitingGoalScored();
    void endOfPeriod(int period);
    void overtimePeriod(int ot);
}
```

**说明**：实现 Hockey 接口的类需要实现 8 个方法（2+2+4）

### 6.3 完整示例

```java
// 实现 Hockey 接口
public class IceHockey implements Hockey {
    @Override
    public void setHomeTeam(String name) {
        System.out.println("Home team: " + name);
    }
    
    @Override
    public void setVisitingTeam(String name) {
        System.out.println("Visiting team: " + name);
    }
    
    @Override
    public void start() {
        System.out.println("Game started");
    }
    
    @Override
    public void end() {
        System.out.println("Game ended");
    }
    
    @Override
    public void homeGoalScored() {
        System.out.println("Home goal!");
    }
    
    @Override
    public void visitingGoalScored() {
        System.out.println("Visiting goal!");
    }
    
    @Override
    public void endOfPeriod(int period) {
        System.out.println("End of period " + period);
    }
    
    @Override
    public void overtimePeriod(int ot) {
        System.out.println("Overtime period " + ot);
    }
}
```

---

## 七、标记接口

### 7.1 什么是标记接口？

**标记接口(Marker Interface)**：没有任何方法和属性的接口，用于标识某个类属于特定类型。

### 7.2 标记接口示例

```java
package java.util;

// 标记接口：没有任何方法
public interface EventListener {
}
```

```java
package java.io;

// 标记接口：表示类可以被序列化
public interface Serializable {
}
```

### 7.3 标记接口的作用

| 作用 | 说明 | 示例 |
|------|------|------|
| **建立公共父接口** | 为一组接口建立父接口 | EventListener |
| **添加数据类型** | 通过多态变成接口类型 | Serializable |
| **标识特权** | 给对象打标记，拥有特权 | Cloneable |

### 7.4 标记接口使用示例

```java
import java.io.Serializable;

// 实现 Serializable 标记接口
public class Person implements Serializable {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    // getter 和 setter 方法
}

// 使用
public class Test {
    public static void main(String[] args) {
        Person person = new Person("John", 30);
        
        // 检查是否实现了 Serializable
        if (person instanceof Serializable) {
            System.out.println("Person can be serialized");
        }
    }
}
```

---

## 八、接口的高级特性（Java 8+）

### 8.1 默认方法（Default Methods）

Java 8 开始，接口可以有默认方法实现。

```java
public interface Vehicle {
    // 抽象方法
    void start();
    
    // 默认方法
    default void stop() {
        System.out.println("Vehicle stopped");
    }
    
    // 默认方法
    default void honk() {
        System.out.println("Beep! Beep!");
    }
}

public class Car implements Vehicle {
    @Override
    public void start() {
        System.out.println("Car started");
    }
    
    // 可以选择重写默认方法
    @Override
    public void honk() {
        System.out.println("Car horn: Honk! Honk!");
    }
    
    // stop() 方法使用接口的默认实现
}
```

### 8.2 静态方法（Static Methods）

Java 8 开始，接口可以有静态方法。

```java
public interface MathUtils {
    // 静态方法
    static int add(int a, int b) {
        return a + b;
    }
    
    static int multiply(int a, int b) {
        return a * b;
    }
}

// 使用
public class Test {
    public static void main(String[] args) {
        int sum = MathUtils.add(5, 3);        // 8
        int product = MathUtils.multiply(5, 3); // 15
    }
}
```

### 8.3 私有方法（Private Methods - Java 9+）

Java 9 开始，接口可以有私有方法。

```java
public interface Logger {
    // 默认方法
    default void logInfo(String message) {
        log(message, "INFO");
    }
    
    default void logError(String message) {
        log(message, "ERROR");
    }
    
    // 私有方法：被默认方法调用
    private void log(String message, String level) {
        System.out.println("[" + level + "] " + message);
    }
}
```

---

## 九、接口的实际应用

### 9.1 定义规范

```java
// 定义数据库操作规范
public interface DatabaseOperations {
    void connect();
    void disconnect();
    void executeQuery(String query);
    void executeUpdate(String query);
}

// MySQL 实现
public class MySQLDatabase implements DatabaseOperations {
    @Override
    public void connect() {
        System.out.println("Connecting to MySQL");
    }
    
    @Override
    public void disconnect() {
        System.out.println("Disconnecting from MySQL");
    }
    
    @Override
    public void executeQuery(String query) {
        System.out.println("Executing MySQL query: " + query);
    }
    
    @Override
    public void executeUpdate(String query) {
        System.out.println("Executing MySQL update: " + query);
    }
}

// Oracle 实现
public class OracleDatabase implements DatabaseOperations {
    @Override
    public void connect() {
        System.out.println("Connecting to Oracle");
    }
    
    @Override
    public void disconnect() {
        System.out.println("Disconnecting from Oracle");
    }
    
    @Override
    public void executeQuery(String query) {
        System.out.println("Executing Oracle query: " + query);
    }
    
    @Override
    public void executeUpdate(String query) {
        System.out.println("Executing Oracle update: " + query);
    }
}
```

### 9.2 回调机制

```java
// 定义回调接口
public interface ClickListener {
    void onClick();
}

// 按钮类
public class Button {
    private ClickListener listener;
    
    public void setClickListener(ClickListener listener) {
        this.listener = listener;
    }
    
    public void click() {
        if (listener != null) {
            listener.onClick();
        }
    }
}

// 使用
public class Test {
    public static void main(String[] args) {
        Button button = new Button();
        
        // 使用匿名类实现接口
        button.setClickListener(new ClickListener() {
            @Override
            public void onClick() {
                System.out.println("Button clicked!");
            }
        });
        
        // 或使用 Lambda 表达式（Java 8+）
        button.setClickListener(() -> System.out.println("Button clicked!"));
        
        button.click();
    }
}
```

### 9.3 策略模式

```java
// 排序策略接口
public interface SortStrategy {
    void sort(int[] array);
}

// 冒泡排序
public class BubbleSort implements SortStrategy {
    @Override
    public void sort(int[] array) {
        System.out.println("Using Bubble Sort");
        // 冒泡排序实现
    }
}

// 快速排序
public class QuickSort implements SortStrategy {
    @Override
    public void sort(int[] array) {
        System.out.println("Using Quick Sort");
        // 快速排序实现
    }
}

// 排序器
public class Sorter {
    private SortStrategy strategy;
    
    public void setStrategy(SortStrategy strategy) {
        this.strategy = strategy;
    }
    
    public void sort(int[] array) {
        strategy.sort(array);
    }
}

// 使用
public class Test {
    public static void main(String[] args) {
        int[] array = {5, 2, 8, 1, 9};
        Sorter sorter = new Sorter();
        
        sorter.setStrategy(new BubbleSort());
        sorter.sort(array);
        
        sorter.setStrategy(new QuickSort());
        sorter.sort(array);
    }
}
```

---

## 十、重点总结

### 10.1 接口的核心特点

| 特点 | 说明 |
|------|------|
| **抽象** | 定义规范，不关心实现 |
| **多实现** | 一个类可以实现多个接口 |
| **多继承** | 接口可以继承多个接口 |
| **常量** | 只能有 public static final 常量 |
| **方法** | 默认为 public abstract（Java 8前） |

### 10.2 接口 vs 抽象类

| 特性 | 接口 | 抽象类 |
|------|------|--------|
| 多继承 | 支持 | 不支持 |
| 构造方法 | 无 | 有 |
| 成员变量 | 只能常量 | 可以有普通变量 |
| 方法实现 | 默认无（Java 8后可有） | 可以有 |
| 访问修饰符 | public | 任意 |
| 设计理念 | 定义规范 | 代码复用 |

### 10.3 使用场景

```java
// ✅ 使用接口：定义规范
public interface Drawable {
    void draw();
}

// ✅ 使用接口：多重继承
public class Bird implements Flyable, Swimmable {
}

// ✅ 使用抽象类：代码复用
public abstract class Animal {
    protected String name;
    
    public void sleep() {
        System.out.println(name + " is sleeping");
    }
    
    public abstract void makeSound();
}
```

### 10.4 最佳实践

1. ✅ 面向接口编程，而非面向实现
2. ✅ 接口命名使用形容词（Runnable, Comparable）
3. ✅ 保持接口简单，单一职责
4. ✅ 使用接口实现解耦
5. ❌ 避免接口过于庞大

### 10.5 记忆口诀

```
接口定规范，抽象方法全
不能实例化，可以被实现
类实现接口，implements 连
多个接口实现，逗号来分隔
接口继承接口，extends 来扩展
支持多重继承，这是大特点
```