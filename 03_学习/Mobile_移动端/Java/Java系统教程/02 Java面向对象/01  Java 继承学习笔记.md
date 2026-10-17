## 一、继承概述

### 1.1 什么是继承？

**继承(Inheritance)**：一个对象从另一个对象获取属性的过程，是面向对象编程的三大特征之一。

### 1.2 继承的作用

- ✅ 实现代码复用
- ✅ 建立类之间的层次关系
- ✅ 提高代码的可维护性
- ✅ 支持多态性

### 1.3 Java 继承的特点

|特点|说明|
|---|---|
|**单一继承**|一个子类只能有一个直接父类|
|**多层继承**|子类可以继承父类，父类也可以有父类|
|**Object 根类**|所有类都继承自 java.lang.Object|
|**接口多实现**|一个类可以实现多个接口|

---

## 二、继承的关键字

### 2.1 extends 关键字

用于类继承类（包括普通类和抽象类）。

#### 基本语法

```java
public class 子类 extends 父类 {
    // 子类成员
}
```

#### 示例

```java
// 父类 A
public class A {
    private int i;      // 私有成员
    protected int j;    // 受保护成员
    
    public void func() {
        // 方法实现
    }
}

// 子类 B 继承 A
public class B extends A {
    public int z;       // 子类自己的成员
    
    public void fund() {
        // 子类自己的方法
    }
}
```

**说明**：

- 子类 B 继承了父类 A 的所有成员
- 子类无法访问父类的 private 成员
- 子类可以访问父类的 protected 和 public 成员

### 2.2 implements 关键字

用于类实现接口。

#### 基本语法

```java
public class 类名 implements 接口1, 接口2, ... {
    // 实现接口方法
}
```

#### 示例

```java
// 接口
public interface Animal {
    void eat();
    void sleep();
}

// 实现接口
public class Mammal implements Animal {
    @Override
    public void eat() {
        System.out.println("Mammal eating");
    }
    
    @Override
    public void sleep() {
        System.out.println("Mammal sleeping");
    }
}
```

---

## 三、IS-A 关系

### 3.1 什么是 IS-A？

**IS-A 关系**：表示"是一个"的关系，用于描述继承关系。

### 3.2 继承层次示例

```java
public class Animal {
    // 动物类
}

public class Mammal extends Animal {
    // 哺乳动物类
}

public class Reptile extends Animal {
    // 爬行动物类
}

public class Dog extends Mammal {
    // 狗类
}
```

### 3.3 IS-A 关系分析

根据上面的继承关系：

|关系|说明|
|---|---|
|`Mammal IS-A Animal`|哺乳动物是动物 ✅|
|`Reptile IS-A Animal`|爬行动物是动物 ✅|
|`Dog IS-A Mammal`|狗是哺乳动物 ✅|
|`Dog IS-A Animal`|狗是动物 ✅（传递性）|

### 3.4 instanceof 运算符

用于检查对象是否是某个类的实例。

```java
public class Dog extends Mammal {
    public static void main(String args[]) {
        Animal a = new Animal();
        Mammal m = new Mammal();
        Dog d = new Dog();
        
        System.out.println(m instanceof Animal);  // true
        System.out.println(d instanceof Mammal);  // true
        System.out.println(d instanceof Animal);  // true
    }
}
```

**输出：**

```
true
true
true
```

---

## 四、接口实现的 IS-A 关系

### 4.1 接口继承示例

```java
// 定义接口
public interface Animal {
    void makeSound();
}

// 实现接口
public class Mammal implements Animal {
    @Override
    public void makeSound() {
        System.out.println("Mammal sound");
    }
}

// 继承类并实现接口
public class Dog extends Mammal {
    @Override
    public void makeSound() {
        System.out.println("Woof!");
    }
}
```

### 4.2 instanceof 验证

```java
interface Animal {}

class Mammal implements Animal {}

public class Dog extends Mammal {
    public static void main(String args[]) {
        Mammal m = new Mammal();
        Dog d = new Dog();
        
        System.out.println(m instanceof Animal);  // true
        System.out.println(d instanceof Mammal);  // true
        System.out.println(d instanceof Animal);  // true
    }
}
```

**输出：**

```
true
true
true
```

---

## 五、HAS-A 关系

### 5.1 什么是 HAS-A？

**HAS-A 关系**：表示"有一个"的关系，用于描述组合/聚合关系。

### 5.2 HAS-A 示例

```java
public class Vehicle {
    // 车辆类
}

public class Speed {
    private int speed;
    
    public void accelerate() {
        speed += 10;
    }
}

public class Van extends Vehicle {
    private Speed sp;  // Van HAS-A Speed
    
    public Van() {
        sp = new Speed();
    }
    
    public void speedUp() {
        sp.accelerate();
    }
}
```

### 5.3 HAS-A 的优势

|优势|说明|
|---|---|
|**代码复用**|不需要复制 Speed 类的代码|
|**降低耦合**|Speed 类可以独立修改|
|**提高灵活性**|Speed 类可以被多个类使用|
|**封装性**|隐藏实现细节|

---

## 六、继承的限制

### 6.1 单继承限制

Java 只支持单继承，一个类只能继承一个父类。

```java
// ❌ 错误：不能继承多个类
public class Dog extends Animal, Mammal {
    // 编译错误！
}

// ✅ 正确：只继承一个类
public class Dog extends Mammal {
    // 正确
}
```

### 6.2 多接口实现

虽然不能继承多个类，但可以实现多个接口。

```java
// ✅ 正确：可以实现多个接口
public class Apple extends Fruit implements Fruit1, Fruit2 {
    // 正确
}
```

### 6.3 继承规则总结

|类型|关键字|数量限制|
|---|---|---|
|继承类|`extends`|只能一个|
|实现接口|`implements`|可以多个|
|组合使用|`extends` + `implements`|先 extends 后 implements|

---

## 七、继承中的访问控制

### 7.1 访问修饰符继承规则

|修饰符|同类|同包|子类|其他|
|---|---|---|---|---|
|`private`|✅|❌|❌|❌|
|`default`|✅|✅|❌|❌|
|`protected`|✅|✅|✅|❌|
|`public`|✅|✅|✅|✅|

### 7.2 访问示例

```java
public class Parent {
    private int privateVar = 1;      // 子类不可访问
    int defaultVar = 2;              // 同包子类可访问
    protected int protectedVar = 3;  // 子类可访问
    public int publicVar = 4;        // 子类可访问
    
    private void privateMethod() {}      // 子类不可访问
    void defaultMethod() {}              // 同包子类可访问
    protected void protectedMethod() {}  // 子类可访问
    public void publicMethod() {}        // 子类可访问
}

public class Child extends Parent {
    public void test() {
        // System.out.println(privateVar);    // ❌ 编译错误
        System.out.println(defaultVar);       // ✅ 同包可访问
        System.out.println(protectedVar);     // ✅ 可访问
        System.out.println(publicVar);        // ✅ 可访问
    }
}
```

---

## 八、继承的完整示例

### 8.1 动物继承体系

```java
// 1. 顶层父类
public class Animal {
    protected String name;
    protected int age;
    
    public Animal(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public void eat() {
        System.out.println(name + " is eating");
    }
    
    public void sleep() {
        System.out.println(name + " is sleeping");
    }
}

// 2. 中间层：哺乳动物
public class Mammal extends Animal {
    private boolean hasFur;
    
    public Mammal(String name, int age, boolean hasFur) {
        super(name, age);  // 调用父类构造器
        this.hasFur = hasFur;
    }
    
    public void giveBirth() {
        System.out.println(name + " gives birth to live young");
    }
}

// 3. 具体类：狗
public class Dog extends Mammal {
    private String breed;
    
    public Dog(String name, int age, String breed) {
        super(name, age, true);
        this.breed = breed;
    }
    
    public void bark() {
        System.out.println(name + " says: Woof! Woof!");
    }
    
    @Override
    public void eat() {
        System.out.println(name + " the " + breed + " is eating dog food");
    }
}

// 4. 测试类
public class TestInheritance {
    public static void main(String[] args) {
        Dog dog = new Dog("Buddy", 3, "Golden Retriever");
        
        // 调用继承的方法
        dog.eat();        // 重写的方法
        dog.sleep();      // 继承自 Animal
        dog.giveBirth();  // 继承自 Mammal
        dog.bark();       // Dog 自己的方法
        
        // instanceof 测试
        System.out.println(dog instanceof Dog);     // true
        System.out.println(dog instanceof Mammal);  // true
        System.out.println(dog instanceof Animal);  // true
        System.out.println(dog instanceof Object);  // true
    }
}
```

---

## 九、继承 vs 组合

### 9.1 对比表

|特性|继承 (IS-A)|组合 (HAS-A)|
|---|---|---|
|关系|"是一个"|"有一个"|
|关键字|extends|成员变量|
|耦合度|高|低|
|灵活性|低|高|
|代码复用|自动继承|需要委托|
|适用场景|明确的层次关系|功能组合|

### 9.2 选择建议

```java
// ✅ 使用继承：明确的 IS-A 关系
public class Dog extends Animal {
    // Dog IS-A Animal
}

// ✅ 使用组合：功能组合
public class Car {
    private Engine engine;  // Car HAS-A Engine
    private Wheel[] wheels; // Car HAS-A Wheels
}

// ❌ 避免：不恰当的继承
public class Stack extends ArrayList {
    // Stack 不是 ArrayList！应该用组合
}
```

---

## 十、重点总结

### 10.1 核心概念

1. **单继承**：Java 类只能继承一个父类
2. **多实现**：Java 类可以实现多个接口
3. **IS-A 关系**：继承表示"是一个"的关系
4. **HAS-A 关系**：组合表示"有一个"的关系
5. **Object 根类**：所有类都继承自 Object

### 10.2 关键字用法

```java
// extends：继承类
public class 子类 extends 父类 {}

// implements：实现接口
public class 类 implements 接口1, 接口2 {}

// 组合使用
public class 子类 extends 父类 implements 接口1, 接口2 {}
```

### 10.3 访问规则

- `private`：子类不可访问
- `default`：同包子类可访问
- `protected`：所有子类可访问
- `public`：所有子类可访问

### 10.4 最佳实践

1. ✅ 优先使用组合而非继承
2. ✅ 继承用于明确的 IS-A 关系
3. ✅ 使用 protected 允许子类访问
4. ✅ 使用 @Override 注解重写方法
5. ❌ 避免过深的继承层次（一般不超过3层）

### 10.5 常见模式

```java
// 模板方法模式
public abstract class Template {
    public final void process() {
        step1();
        step2();
        step3();
    }
    
    protected abstract void step1();
    protected abstract void step2();
    protected void step3() {
        // 默认实现
    }
}

// 策略模式（使用接口）
public interface Strategy {
    void execute();
}

public class ConcreteStrategy implements Strategy {
    @Override
    public void execute() {
        // 具体实现
    }
}
```