## 一、修饰符概述

### 1.1 修饰符分类

Java 提供两大类修饰符：

1. **访问修饰符**（Access Modifiers）
2. **非访问修饰符**（Non-Access Modifiers）

### 1.2 修饰符的位置

修饰符通常放在语句的最前端：

```java
public class ClassName {
    // ...
}

private boolean myFlag;
static final double weeks = 9.5;
protected static final int BOXWIDTH = 42;

public static void main(String[] arguments) {
    // 方法体
}
```

---

## 二、访问控制修饰符

### 2.1 四种访问权限

| 修饰符         | 说明   | 可见范围             |
| ----------- | ---- | ---------------- |
| `public`    | 公有的  | 对所有类可见           |
| `protected` | 受保护的 | 同一包内的类和所有子类可见    |
| `default`   | 默认的  | 同一包内可见（不使用任何修饰符） |
| `private`   | 私有的  | 仅在同一类内可见         |

### 2.2 访问权限范围表

| 修饰符         | 当前类 | 同一包内 | 子类（同包） | 子类（不同包） | 其他包 |
| ----------- | --- | ---- | ------ | ------- | --- |
| `public`    | ✅   | ✅    | ✅      | ✅       | ✅   |
| `protected` | ✅   | ✅    | ✅      | ✅       | ❌   |
| `default`   | ✅   | ✅    | ✅      | ❌       | ❌   |
| `private`   | ✅   | ❌    | ❌      | ❌       | ❌   |

---

## 三、默认访问修饰符（default）

### 3.1 特点

- 不使用任何关键字
- 对同一个包内的类可见
- 接口中的变量隐式声明为 `public static final`
- 接口中的方法默认为 `public`

### 3.2 示例

```java
String version = "1.5.1";

boolean processOrder() {
    return true;
}
```

---

## 四、私有访问修饰符（private）

### 4.1 特点

- 最严格的访问级别
- 只能被所属类访问
- 类和接口不能声明为 `private`
- 主要用于隐藏类的实现细节和保护数据

### 4.2 示例：封装

```java
public class Logger {
    private String format;
    
    public String getFormat() {
        return this.format;
    }
    
    public void setFormat(String format) {
        this.format = format;
    }
}
```

**说明**：

- `format` 变量为私有，外部类不能直接访问
- 通过 `public` 的 getter/setter 方法访问

---

## 五、公有访问修饰符（public）

### 5.1 特点

- 被声明为 `public` 的类、方法、构造方法和接口能被任何其他类访问
- 如果 `public` 类分布在不同包中，需要导入相应的包
- 由于继承性，所有公有方法和变量都能被子类继承

### 5.2 示例

```java
public static void main(String[] arguments) {
    // ...
}
```

**重要**：Java 程序的 `main()` 方法必须设置成 `public`，否则 Java 解释器无法运行该类。

---

## 六、受保护的访问修饰符（protected）

### 6.1 特点

- 能被同一个包中的任何其他类访问
- 能被不同包中的子类访问
- 不能修饰类和接口（只能修饰方法和成员变量）
- 接口的成员变量和方法不能声明为 `protected`
- 保护不相关的类使用这些方法和变量

### 6.2 示例

```java
class AudioPlayer {
    protected boolean openSpeaker(Speaker sp) {
        // 实现细节
    }
}

class StreamingAudioPlayer extends AudioPlayer {
    @Override
    boolean openSpeaker(Speaker sp) {
        // 实现细节
    }
}
```

**说明**：

- 如果声明为 `private`：只有 `AudioPlayer` 能访问
- 如果声明为 `public`：所有类都能访问
- 声明为 `protected`：只对子类可见

---

## 七、访问控制和继承规则

### 7.1 方法继承规则

1. 父类中声明为 `public` 的方法在子类中也必须为 `public`
2. 父类中声明为 `protected` 的方法在子类中要么为 `protected`，要么为 `public`，不能为 `private`
3. 父类中声明为 `private` 的方法不能被继承

### 7.2 访问权限只能扩大不能缩小

```java
class Parent {
    protected void method() { }
}

class Child extends Parent {
    // ✅ 正确：扩大访问权限
    public void method() { }
    
    // ❌ 错误：缩小访问权限
    // private void method() { }
}
```

---

## 八、非访问修饰符

### 8.1 非访问修饰符列表

| 修饰符            | 用途              |
| -------------- | --------------- |
| `static`       | 创建类方法和类变量       |
| `final`        | 修饰类、方法和变量（不可改变） |
| `abstract`     | 创建抽象类和抽象方法      |
| `synchronized` | 线程同步            |
| `volatile`     | 线程编程（变量可见性）     |
| `transient`    | 序列化时跳过该变量       |

---

## 九、static 修饰符

### 9.1 静态变量

- 独立于对象的静态变量
- 无论实例化多少对象，只有一份拷贝
- 也称为类变量
- 局部变量不能声明为 `static`

### 9.2 静态方法

- 独立于对象的静态方法
- 不能使用类的非静态变量
- 从参数列表得到数据

### 9.3 访问方式

```java
ClassName.variableName
ClassName.methodName()
```

### 9.4 示例

```java
public class InstanceCounter {
    private static int numInstances = 0;
    
    protected static int getCount() {
        return numInstances;
    }
    
    private static void addInstance() {
        numInstances++;
    }
    
    InstanceCounter() {
        InstanceCounter.addInstance();
    }
    
    public static void main(String[] arguments) {
        System.out.println("Starting with " + 
            InstanceCounter.getCount() + " instances");
        
        for (int i = 0; i < 500; ++i) {
            new InstanceCounter();
        }
        
        System.out.println("Created " + 
            InstanceCounter.getCount() + " instances");
    }
}
```

**输出**：

```
Started with 0 instances
Created 500 instances
```

---

## 十、final 修饰符

### 10.1 final 变量

- 能被显式初始化，且只能初始化一次
- `final` 对象的引用不能改变，但对象内部的值可以改变
- 通常和 `static` 一起使用创建类常量

### 10.2 final 变量示例

```java
public class Test {
    final int value = 10;
    
    // 声明常量
    public static final int BOXWIDTH = 6;
    static final String TITLE = "Manager";
    
    public void changeValue() {
        value = 12;  // ❌ 编译错误：不能修改 final 变量
    }
}
```

### 10.3 final 方法

- 可以被子类继承
- 不能被子类修改（重写）
- 主要目的是防止方法内容被修改

```java
public class Test {
    public final void changeName() {
        // 方法体
    }
}
```

### 10.4 final 类

- 不能被继承
- 没有类能够继承 `final` 类的任何特性

```java
public final class Test {
    // 类体
}
```

---

## 十一、abstract 修饰符

### 11.1 抽象类

- 不能用来实例化对象
- 声明的唯一目的是为了将来扩充
- 不能同时被 `abstract` 和 `final` 修饰
- 如果包含抽象方法，类必须声明为抽象类
- 可以包含抽象方法和非抽象方法

### 11.2 抽象类示例

```java
abstract class Caravan {
    private double price;
    private String model;
    private String year;
    
    public abstract void goFast();        // 抽象方法
    public abstract void changeColor();   // 抽象方法
}
```

### 11.3 抽象方法

- 没有任何实现的方法
- 具体实现由子类提供
- 不能被声明为 `final` 和 `static`
- 继承抽象类的子类必须实现所有抽象方法（除非子类也是抽象类）
- 声明以分号结尾

### 11.4 抽象方法示例

```java
public abstract class SuperClass {
    abstract void m();  // 抽象方法
}

class SubClass extends SuperClass {
    // 实现抽象方法
    @Override
    void m() {
        // 具体实现
    }
}
```

---

## 十二、synchronized 修饰符

### 12.1 特点

- 声明的方法同一时间只能被一个线程访问
- 可以应用于四个访问修饰符
- 用于线程同步

### 12.2 示例

```java
public synchronized void showDetails() {
    // 方法体
}
```

---

## 十三、transient 修饰符

### 13.1 特点

- 序列化对象时，JVM 跳过该变量
- 用于预处理类和变量的数据类型
- 该变量不会被持久化

### 13.2 示例

```java
public transient int limit = 55;  // 不会被持久化
public int b;                      // 会被持久化
```

---

## 十四、volatile 修饰符

### 14.1 特点

- 每次被线程访问时，强制从共享内存中重读该变量的值
- 变量发生变化时，强制线程将变化值回写到共享内存
- 保证不同线程总是看到变量的同一个值
- 用于多线程编程

### 14.2 示例

```java
public class MyRunnable implements Runnable {
    private volatile boolean active;
    
    public void run() {
        active = true;
        while (active) {  // line 1
            // 代码
        }
    }
    
    public void stop() {
        active = false;   // line 2
    }
}
```

**说明**：

- 如果 `active` 不是 `volatile`，line 1 可能使用缓存值
- 即使 line 2 设置为 `false`，循环可能不会停止
- 使用 `volatile` 确保线程间的可见性

---

## 十五、修饰符组合使用

### 15.1 常见组合

```java
public class Example {
    // 公有静态常量
    public static final int MAX_SIZE = 100;
    
    // 私有静态变量
    private static int count = 0;
    
    // 私有实例变量
    private String name;
    
    // 公有静态方法
    public static void staticMethod() { }
    
    // 公有实例方法
    public void instanceMethod() { }
    
    // 私有 final 方法
    private final void finalMethod() { }
    
    // 公有同步方法
    public synchronized void syncMethod() { }
}
```

### 15.2 不能组合的修饰符

| 组合                          | 说明                        |
| --------------------------- | ------------------------- |
| `abstract` + `final`        | 抽象类需要被继承，final 类不能被继承     |
| `abstract` + `private`      | 抽象方法需要被重写，private 方法不能被继承 |
| `abstract` + `static`       | 抽象方法需要被重写，static 方法属于类    |
| `abstract` + `synchronized` | 抽象方法没有实现，无法同步             |

---

## 十六、修饰符使用最佳实践

### 16.1 访问控制原则

1. **最小权限原则**：默认使用最严格的访问级别
2. **成员变量私有化**：使用 `private`，提供 getter/setter
3. **方法根据需要开放**：只对外暴露必要的方法
4. **常量公开**：使用 `public static final`

### 16.2 示例

```java
public class BestPractice {
    // ✅ 常量：public static final
    public static final String VERSION = "1.0.0";
    
    // ✅ 私有实例变量
    private String name;
    private int age;
    
    // ✅ 公有 getter/setter
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    // ✅ 私有辅助方法
    private void helperMethod() {
        // 内部使用
    }
    
    // ✅ 公有业务方法
    public void publicMethod() {
        helperMethod();
    }
}
```

---

## 十七、重要知识点总结

### 17.1 访问修饰符记忆口诀

- `public`：公开透明，人人可见
- `protected`：家族企业，子孙可见
- `default`：邻里关系，同包可见
- `private`：私人物品，自己可见

### 17.2 关键要点

1. **访问权限从严到宽**：private → default → protected → public
2. **继承时权限只能扩大不能缩小**
3. **static 成员属于类，非 static 成员属于对象**
4. **final 表示不可改变**：final 类不可继承，final 方法不可重写，final 变量不可修改
5. **abstract 和 final 互斥**
6. **synchronized 用于线程同步**
7. **volatile 保证线程间变量可见性**

### 17.3 常见错误

```java
// ❌ 抽象类不能是 final
public abstract final class Test { }

// ❌ 抽象方法不能是 private
private abstract void method();

// ❌ 子类缩小访问权限
class Parent {
    public void method() { }
}
class Child extends Parent {
    private void method() { }  // 错误
}

// ❌ final 变量不能修改
final int x = 10;
x = 20;  // 错误
```