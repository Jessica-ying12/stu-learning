## 一、多态概述

### 1.1 什么是多态？

**多态(Polymorphism)**：同一个行为具有多个不同表现形式或形态的能力，是面向对象编程的三大特征之一。

### 1.2 生活中的多态例子

**宠物店例子**：

- 你说："请给我一只宠物"
- 服务员可以给你：小猫 🐱、小狗 🐶、蜥蜴 🦎
- "宠物"这个对象具备多态性

### 1.3 多态的优势

|优势|说明|
|---|---|
|**可扩展性**|容易添加新的子类|
|**可替换性**|子类对象可以替换父类对象|
|**灵活性**|同一接口，不同实现|
|**解耦**|降低代码耦合度|

---

## 二、多态的基本示例

### 2.1 简单的多态例子

```java
// 接口
public interface Vegetarian {
    void eatPlants();
}

// 父类
public class Animal {
    public void breathe() {
        System.out.println("Animal is breathing");
    }
}

// 子类：实现多重继承
public class Deer extends Animal implements Vegetarian {
    @Override
    public void eatPlants() {
        System.out.println("Deer eats plants");
    }
    
    public void run() {
        System.out.println("Deer is running");
    }
}
```

### 2.2 IS-A 关系分析

因为 Deer 类具有多重继承，所以它具有多态性：

|IS-A 关系|说明|
|---|---|
|`Deer IS-A Animal`|Deer 是一个 Animal ✅|
|`Deer IS-A Vegetarian`|Deer 是一个 Vegetarian ✅|
|`Deer IS-A Deer`|Deer 是一个 Deer ✅|
|`Deer IS-A Object`|Deer 是一个 Object ✅|

### 2.3 引用类型的多态性

```java
public class TestPolymorphism {
    public static void main(String[] args) {
        Deer d = new Deer();
        
        // 所有这些引用都指向堆中同一个 Deer 对象
        Animal a = d;        // 向上转型
        Vegetarian v = d;    // 向上转型
        Object o = d;        // 向上转型
        
        // 验证引用
        System.out.println(d instanceof Deer);       // true
        System.out.println(a instanceof Animal);     // true
        System.out.println(v instanceof Vegetarian); // true
        System.out.println(o instanceof Object);     // true
    }
}
```

---

## 三、虚方法（方法重写）

### 3.1 什么是虚方法？

**虚方法**：被子类重写的方法，在运行时根据对象的实际类型调用相应的方法。

### 3.2 完整示例

#### 1. 父类 Employee

```java
/* 文件名: Employee.java */
public class Employee {
    private String name;
    private String address;
    private int number;
    
    public Employee(String name, String address, int number) {
        System.out.println("Constructing an Employee");
        this.name = name;
        this.address = address;
        this.number = number;
    }
    
    public void mailCheck() {
        System.out.println("Mailing a check to " + this.name 
            + " " + this.address);
    }
    
    public String toString() {
        return name + " " + address + " " + number;
    }
    
    public String getName() {
        return name;
    }
    
    public String getAddress() {
        return address;
    }
    
    public void setAddress(String newAddress) {
        address = newAddress;
    }
    
    public int getNumber() {
        return number;
    }
}
```

#### 2. 子类 Salary

```java
/* 文件名: Salary.java */
public class Salary extends Employee {
    private double salary; // 年薪
    
    public Salary(String name, String address, int number, double salary) {
        super(name, address, number);
        setSalary(salary);
    }
    
    // 重写父类方法
    @Override
    public void mailCheck() {
        System.out.println("Within mailCheck of Salary class");
        System.out.println("Mailing check to " + getName() 
            + " with salary " + salary);
    }
    
    public double getSalary() {
        return salary;
    }
    
    public void setSalary(double newSalary) {
        if (newSalary >= 0.0) {
            salary = newSalary;
        }
    }
    
    public double computePay() {
        System.out.println("Computing salary pay for " + getName());
        return salary / 52;
    }
}
```

#### 3. 测试类

```java
/* 文件名: VirtualDemo.java */
public class VirtualDemo {
    public static void main(String[] args) {
        // 使用 Salary 引用
        Salary s = new Salary("Mohd Mohtashim", "Ambehta, UP", 3, 3600.00);
        
        // 使用 Employee 引用指向 Salary 对象
        Employee e = new Salary("John Adams", "Boston, MA", 2, 2400.00);
        
        System.out.println("Call mailCheck using Salary reference --");
        s.mailCheck();
        
        System.out.println("\nCall mailCheck using Employee reference--");
        e.mailCheck();  // 多态：调用的是 Salary 的 mailCheck()
    }
}
```

**输出结果：**

```
Constructing an Employee
Constructing an Employee
Call mailCheck using Salary reference --
Within mailCheck of Salary class
Mailing check to Mohd Mohtashim with salary 3600.0

Call mailCheck using Employee reference--
Within mailCheck of Salary class
Mailing check to John Adams with salary 2400.0
```

### 3.3 虚方法调用机制

|阶段|行为|
|---|---|
|**编译时**|检查引用类型中是否有该方法|
|**运行时**|调用对象实际类型的方法|

```java
Employee e = new Salary(...);
e.mailCheck();  
// 编译时：检查 Employee 类是否有 mailCheck() 方法 ✅
// 运行时：调用 Salary 类的 mailCheck() 方法 ✅
```

---

## 四、多态的实现方式

### 4.1 方式一：方法重写（Override）

**定义**：子类重写父类的方法，实现不同的行为。

```java
public class Animal {
    public void makeSound() {
        System.out.println("Animal makes a sound");
    }
}

public class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Dog barks: Woof! Woof!");
    }
}

public class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("Cat meows: Meow! Meow!");
    }
}

// 测试
public class TestOverride {
    public static void main(String[] args) {
        Animal animal1 = new Dog();
        Animal animal2 = new Cat();
        
        animal1.makeSound();  // 输出: Dog barks: Woof! Woof!
        animal2.makeSound();  // 输出: Cat meows: Meow! Meow!
    }
}
```

### 4.2 方式二：接口实现

**定义**：不同的类实现同一个接口，提供不同的实现。

```java
// 定义接口
public interface Animal {
    void eat();
    void sleep();
    void makeSound();
}

// 实现类1：狗
public class Dog implements Animal {
    @Override
    public void eat() {
        System.out.println("Dog eats dog food");
    }
    
    @Override
    public void sleep() {
        System.out.println("Dog sleeps in kennel");
    }
    
    @Override
    public void makeSound() {
        System.out.println("Dog barks");
    }
}

// 实现类2：猫
public class Cat implements Animal {
    @Override
    public void eat() {
        System.out.println("Cat eats cat food");
    }
    
    @Override
    public void sleep() {
        System.out.println("Cat sleeps on sofa");
    }
    
    @Override
    public void makeSound() {
        System.out.println("Cat meows");
    }
}

// 测试
public class TestInterface {
    public static void main(String[] args) {
        Animal animal1 = new Dog();
        Animal animal2 = new Cat();
        
        animal1.eat();        // Dog eats dog food
        animal1.makeSound();  // Dog barks
        
        animal2.eat();        // Cat eats cat food
        animal2.makeSound();  // Cat meows
    }
}
```

### 4.3 方式三：抽象类和抽象方法

**定义**：抽象类定义抽象方法，子类必须实现这些方法。

```java
// 抽象类
public abstract class Animal {
    protected String name;
    
    public Animal(String name) {
        this.name = name;
    }
    
    // 抽象方法
    public abstract void makeSound();
    public abstract void move();
    
    // 具体方法
    public void sleep() {
        System.out.println(name + " is sleeping");
    }
}

// 具体类1：狗
public class Dog extends Animal {
    public Dog(String name) {
        super(name);
    }
    
    @Override
    public void makeSound() {
        System.out.println(name + " barks: Woof!");
    }
    
    @Override
    public void move() {
        System.out.println(name + " runs on four legs");
    }
}

// 具体类2：鸟
public class Bird extends Animal {
    public Bird(String name) {
        super(name);
    }
    
    @Override
    public void makeSound() {
        System.out.println(name + " chirps: Tweet!");
    }
    
    @Override
    public void move() {
        System.out.println(name + " flies in the sky");
    }
}

// 测试
public class TestAbstract {
    public static void main(String[] args) {
        Animal dog = new Dog("Buddy");
        Animal bird = new Bird("Tweety");
        
        dog.makeSound();   // Buddy barks: Woof!
        dog.move();        // Buddy runs on four legs
        
        bird.makeSound();  // Tweety chirps: Tweet!
        bird.move();       // Tweety flies in the sky
    }
}
```

---

## 五、多态的应用场景

### 5.1 场景一：统一处理不同对象

```java
public class AnimalManager {
    public void feedAnimals(Animal[] animals) {
        for (Animal animal : animals) {
            animal.eat();  // 多态：调用各自的 eat() 方法
        }
    }
    
    public static void main(String[] args) {
        Animal[] animals = {
            new Dog("Buddy"),
            new Cat("Whiskers"),
            new Bird("Tweety")
        };
        
        AnimalManager manager = new AnimalManager();
        manager.feedAnimals(animals);
    }
}
```

### 5.2 场景二：策略模式

```java
// 策略接口
public interface PaymentStrategy {
    void pay(double amount);
}

// 具体策略1：信用卡支付
public class CreditCardPayment implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        System.out.println("Paid " + amount + " using Credit Card");
    }
}

// 具体策略2：支付宝支付
public class AlipayPayment implements PaymentStrategy {
    @Override
    public void pay(double amount) {
        System.out.println("Paid " + amount + " using Alipay");
    }
}

// 上下文
public class ShoppingCart {
    private PaymentStrategy paymentStrategy;
    
    public void setPaymentStrategy(PaymentStrategy strategy) {
        this.paymentStrategy = strategy;
    }
    
    public void checkout(double amount) {
        paymentStrategy.pay(amount);  // 多态
    }
}

// 测试
public class TestStrategy {
    public static void main(String[] args) {
        ShoppingCart cart = new ShoppingCart();
        
        cart.setPaymentStrategy(new CreditCardPayment());
        cart.checkout(100.0);  // Paid 100.0 using Credit Card
        
        cart.setPaymentStrategy(new AlipayPayment());
        cart.checkout(200.0);  // Paid 200.0 using Alipay
    }
}
```

### 5.3 场景三：工厂模式

```java
// 产品接口
public interface Shape {
    void draw();
}

// 具体产品
public class Circle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing Circle");
    }
}

public class Rectangle implements Shape {
    @Override
    public void draw() {
        System.out.println("Drawing Rectangle");
    }
}

// 工厂类
public class ShapeFactory {
    public Shape getShape(String shapeType) {
        if (shapeType == null) {
            return null;
        }
        if (shapeType.equalsIgnoreCase("CIRCLE")) {
            return new Circle();
        } else if (shapeType.equalsIgnoreCase("RECTANGLE")) {
            return new Rectangle();
        }
        return null;
    }
}

// 测试
public class TestFactory {
    public static void main(String[] args) {
        ShapeFactory factory = new ShapeFactory();
        
        Shape shape1 = factory.getShape("CIRCLE");
        shape1.draw();  // Drawing Circle
        
        Shape shape2 = factory.getShape("RECTANGLE");
        shape2.draw();  // Drawing Rectangle
    }
}
```

---

## 六、多态的注意事项

### 6.1 向上转型和向下转型

```java
// 向上转型（自动）
Animal animal = new Dog();  // ✅ 安全

// 向下转型（需要强制转换）
Dog dog = (Dog) animal;     // ✅ 安全（如果 animal 确实是 Dog）

// 错误的向下转型
Animal animal2 = new Cat();
Dog dog2 = (Dog) animal2;   // ❌ 运行时异常：ClassCastException

// 安全的向下转型
if (animal2 instanceof Dog) {
    Dog dog3 = (Dog) animal2;
} else {
    System.out.println("Cannot cast to Dog");
}
```

### 6.2 多态的限制

```java
public class Animal {
    public void eat() {
        System.out.println("Animal eats");
    }
}

public class Dog extends Animal {
    @Override
    public void eat() {
        System.out.println("Dog eats");
    }
    
    public void bark() {
        System.out.println("Dog barks");
    }
}

public class Test {
    public static void main(String[] args) {
        Animal animal = new Dog();
        animal.eat();    // ✅ 可以调用
        // animal.bark(); // ❌ 编译错误：Animal 类没有 bark() 方法
        
        // 需要向下转型
        if (animal instanceof Dog) {
            Dog dog = (Dog) animal;
            dog.bark();  // ✅ 可以调用
        }
    }
}
```

---

## 七、重点总结

### 7.1 多态的三个必要条件

1. **继承**：必须有继承关系
2. **重写**：子类重写父类方法
3. **向上转型**：父类引用指向子类对象

### 7.2 多态的实现方式对比

|方式|关键字|特点|适用场景|
|---|---|---|---|
|**方法重写**|extends|继承父类，重写方法|有明确的继承关系|
|**接口实现**|implements|实现接口方法|定义规范，多实现|
|**抽象类**|abstract|部分实现，部分抽象|有共同实现和抽象|

### 7.3 多态的优缺点

**优点：**

- ✅ 提高代码的可扩展性
- ✅ 提高代码的可维护性
- ✅ 降低代码的耦合度
- ✅ 支持开闭原则（对扩展开放，对修改关闭）

**缺点：**

- ❌ 不能调用子类特有的方法（需要向下转型）
- ❌ 可能出现类型转换异常

### 7.4 最佳实践

```java
// ✅ 推荐：使用多态
public void processAnimals(Animal[] animals) {
    for (Animal animal : animals) {
        animal.makeSound();  // 多态调用
    }
}

// ✅ 推荐：使用 instanceof 检查
if (animal instanceof Dog) {
    Dog dog = (Dog) animal;
    dog.bark();
}

// ✅ 推荐：面向接口编程
List<String> list = new ArrayList<>();  // 而不是 ArrayList<String> list

// ❌ 避免：过度使用向下转型
Animal animal = new Dog();
((Dog) animal).bark();  // 不推荐
```

### 7.5 记忆口诀

```
多态三要素：继承、重写、向上转
编译看左边，运行看右边
父类引用指子类，方法调用看实际
instanceof 检查类型，向下转型要小心
```