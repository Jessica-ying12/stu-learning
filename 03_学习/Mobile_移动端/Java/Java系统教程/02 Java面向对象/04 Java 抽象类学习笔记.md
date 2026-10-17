## 一、抽象类概述

### 1.1 什么是抽象类？

- **定义**：不包含足够信息来描绘具体对象的类
- **特点**：不能实例化对象，但保留类的其他功能
- **目的**：作为父类被继承，定义子类的通用结构

### 1.2 抽象类的特征

|特性|说明|
|---|---|
|**实例化**|❌ 不能直接创建对象|
|**成员变量**|✅ 可以有成员变量|
|**成员方法**|✅ 可以有普通方法和抽象方法|
|**构造方法**|✅ 可以有构造方法|
|**继承**|✅ 必须被继承才能使用|
|**访问方式**|✅ 与普通类相同|

## 二、抽象类的定义

### 2.1 基本语法

```java
public abstract class ClassName {
    // 成员变量
    // 构造方法
    // 普通方法
    // 抽象方法
}
```

### 2.2 完整示例

```java
/* 文件名: Employee.java */
public abstract class Employee {
    // 成员变量
    private String name;
    private String address;
    private int number;
    
    // 构造方法
    public Employee(String name, String address, int number) {
        System.out.println("Constructing an Employee");
        this.name = name;
        this.address = address;
        this.number = number;
    }
    
    // 普通方法
    public double computePay() {
        System.out.println("Inside Employee computePay");
        return 0.0;
    }
    
    public void mailCheck() {
        System.out.println("Mailing a check to " + this.name + 
                          " " + this.address);
    }
    
    public String toString() {
        return name + " " + address + " " + number;
    }
    
    // Getter 和 Setter 方法
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

### 2.3 抽象类不能实例化

```java
/* 文件名: AbstractDemo.java */
public class AbstractDemo {
    public static void main(String[] args) {
        // ❌ 错误：抽象类不能实例化
        Employee e = new Employee("George W.", "Houston, TX", 43);
        
        e.mailCheck();
    }
}
```

**编译错误：**

```
Employee.java:46: Employee is abstract; cannot be instantiated
      Employee e = new Employee("George W.", "Houston, TX", 43);
                   ^
1 error
```

## 三、继承抽象类

### 3.1 子类实现

```java
/* 文件名: Salary.java */
public class Salary extends Employee {
    private double salary;  // 年薪
    
    // 构造方法
    public Salary(String name, String address, int number, double salary) {
        super(name, address, number);  // 调用父类构造方法
        setSalary(salary);
    }
    
    // 重写父类方法
    @Override
    public void mailCheck() {
        System.out.println("Within mailCheck of Salary class");
        System.out.println("Mailing check to " + getName() + 
                          " with salary " + salary);
    }
    
    // 子类特有方法
    public double getSalary() {
        return salary;
    }
    
    public void setSalary(double newSalary) {
        if (newSalary >= 0.0) {
            salary = newSalary;
        }
    }
    
    @Override
    public double computePay() {
        System.out.println("Computing salary pay for " + getName());
        return salary / 52;
    }
}
```

### 3.2 使用子类对象

```java
/* 文件名: AbstractDemo.java */
public class AbstractDemo {
    public static void main(String[] args) {
        // ✅ 正确：实例化子类对象
        Salary s = new Salary("Mohd Mohtashim", "Ambehta, UP", 3, 3600.00);
        
        // ✅ 正确：父类引用指向子类对象（多态）
        Employee e = new Salary("John Adams", "Boston, MA", 2, 2400.00);
        
        System.out.println("Call mailCheck using Salary reference --");
        s.mailCheck();
        
        System.out.println("\nCall mailCheck using Employee reference--");
        e.mailCheck();
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

## 四、抽象方法

### 4.1 什么是抽象方法？

- **定义**：只有方法声明，没有方法体的方法
- **语法**：使用 `abstract` 关键字，方法名后直接跟分号
- **目的**：由子类提供具体实现

### 4.2 抽象方法语法

```java
public abstract class Employee {
    private String name;
    private String address;
    private int number;
    
    // 抽象方法：没有方法体
    public abstract double computePay();
    
    // 普通方法：有方法体
    public void mailCheck() {
        System.out.println("Mailing check to " + name);
    }
}
```

### 4.3 抽象方法的规则

|规则|说明|
|---|---|
|**类的声明**|包含抽象方法的类必须声明为抽象类|
|**子类实现**|子类必须重写所有抽象方法，或声明为抽象类|
|**方法体**|抽象方法没有方法体（没有 `{}`）|
|**关键字**|使用 `abstract` 关键字|
|**实例化**|必须有子类实现抽象方法才能实例化|

### 4.4 子类实现抽象方法

```java
/* 文件名: Salary.java */
public class Salary extends Employee {
    private double salary;
    
    // ✅ 必须实现父类的抽象方法
    @Override
    public double computePay() {
        System.out.println("Computing salary pay for " + getName());
        return salary / 52;
    }
    
    // 其他代码...
}
```

## 五、抽象类的完整示例

### 5.1 定义抽象类

```java
public abstract class Shape {
    private String color;
    
    // 构造方法
    public Shape(String color) {
        this.color = color;
    }
    
    // 抽象方法：计算面积
    public abstract double getArea();
    
    // 抽象方法：计算周长
    public abstract double getPerimeter();
    
    // 普通方法
    public String getColor() {
        return color;
    }
    
    public void displayInfo() {
        System.out.println("颜色: " + color);
        System.out.println("面积: " + getArea());
        System.out.println("周长: " + getPerimeter());
    }
}
```

### 5.2 实现具体子类

```java
// 圆形类
public class Circle extends Shape {
    private double radius;
    
    public Circle(String color, double radius) {
        super(color);
        this.radius = radius;
    }
    
    @Override
    public double getArea() {
        return Math.PI * radius * radius;
    }
    
    @Override
    public double getPerimeter() {
        return 2 * Math.PI * radius;
    }
}

// 矩形类
public class Rectangle extends Shape {
    private double width;
    private double height;
    
    public Rectangle(String color, double width, double height) {
        super(color);
        this.width = width;
        this.height = height;
    }
    
    @Override
    public double getArea() {
        return width * height;
    }
    
    @Override
    public double getPerimeter() {
        return 2 * (width + height);
    }
}
```

### 5.3 使用示例

```java
public class ShapeDemo {
    public static void main(String[] args) {
        // ❌ 错误：不能实例化抽象类
        // Shape shape = new Shape("Red");
        
        // ✅ 正确：实例化具体子类
        Shape circle = new Circle("红色", 5.0);
        Shape rectangle = new Rectangle("蓝色", 4.0, 6.0);
        
        System.out.println("=== 圆形信息 ===");
        circle.displayInfo();
        
        System.out.println("\n=== 矩形信息 ===");
        rectangle.displayInfo();
    }
}
```

**输出结果：**

```
=== 圆形信息 ===
颜色: 红色
面积: 78.53981633974483
周长: 31.41592653589793

=== 矩形信息 ===
颜色: 蓝色
面积: 24.0
周长: 20.0
```

## 六、抽象类的继承链

### 6.1 多层抽象继承

```java
// 第一层：抽象类
public abstract class Animal {
    public abstract void makeSound();
    
    public void sleep() {
        System.out.println("动物在睡觉");
    }
}

// 第二层：仍然是抽象类（未实现所有抽象方法）
public abstract class Mammal extends Animal {
    public abstract void giveBirth();
    
    // 未实现 makeSound()，所以仍是抽象类
}

// 第三层：具体类（实现所有抽象方法）
public class Dog extends Mammal {
    @Override
    public void makeSound() {
        System.out.println("汪汪汪");
    }
    
    @Override
    public void giveBirth() {
        System.out.println("狗妈妈生小狗");
    }
}
```

### 6.2 使用示例

```java
public class AnimalDemo {
    public static void main(String[] args) {
        // ❌ 错误
        // Animal animal = new Animal();
        // Mammal mammal = new Mammal();
        
        // ✅ 正确
        Dog dog = new Dog();
        dog.makeSound();
        dog.giveBirth();
        dog.sleep();
        
        // ✅ 多态
        Animal animal = new Dog();
        animal.makeSound();
        animal.sleep();
    }
}
```

## 七、抽象类 vs 普通类

### 7.1 对比表

|特性|抽象类|普通类|
|---|---|---|
|**实例化**|❌ 不能|✅ 可以|
|**抽象方法**|✅ 可以有|❌ 不能有|
|**普通方法**|✅ 可以有|✅ 可以有|
|**构造方法**|✅ 可以有|✅ 可以有|
|**成员变量**|✅ 可以有|✅ 可以有|
|**继承**|✅ 可以被继承|✅ 可以被继承|
|**关键字**|`abstract`|无|

### 7.2 代码对比

```java
// 抽象类
public abstract class AbstractClass {
    private int value;
    
    public AbstractClass(int value) {
        this.value = value;
    }
    
    // 抽象方法
    public abstract void abstractMethod();
    
    // 普通方法
    public void normalMethod() {
        System.out.println("普通方法");
    }
}

// 普通类
public class NormalClass {
    private int value;
    
    public NormalClass(int value) {
        this.value = value;
    }
    
    // 只能有普通方法
    public void normalMethod() {
        System.out.println("普通方法");
    }
}
```

## 八、抽象类的使用场景

### 8.1 模板方法模式

```java
public abstract class Game {
    // 模板方法（定义算法骨架）
    public final void play() {
        initialize();
        startPlay();
        endPlay();
    }
    
    // 抽象方法（由子类实现）
    protected abstract void initialize();
    protected abstract void startPlay();
    protected abstract void endPlay();
}

// 具体游戏1
public class Cricket extends Game {
    @Override
    protected void initialize() {
        System.out.println("Cricket Game Initialized");
    }
    
    @Override
    protected void startPlay() {
        System.out.println("Cricket Game Started");
    }
    
    @Override
    protected void endPlay() {
        System.out.println("Cricket Game Finished");
    }
}

// 具体游戏2
public class Football extends Game {
    @Override
    protected void initialize() {
        System.out.println("Football Game Initialized");
    }
    
    @Override
    protected void startPlay() {
        System.out.println("Football Game Started");
    }
    
    @Override
    protected void endPlay() {
        System.out.println("Football Game Finished");
    }
}
```

### 8.2 使用示例

```java
public class GameDemo {
    public static void main(String[] args) {
        Game game1 = new Cricket();
        game1.play();
        
        System.out.println();
        
        Game game2 = new Football();
        game2.play();
    }
}
```

## 九、抽象类的最佳实践

### 9.1 设计原则

```java
// ✅ 好的设计：抽象类定义通用行为
public abstract class Vehicle {
    protected String brand;
    
    public Vehicle(String brand) {
        this.brand = brand;
    }
    
    // 抽象方法：子类必须实现
    public abstract void start();
    public abstract void stop();
    
    // 普通方法：提供默认实现
    public void displayBrand() {
        System.out.println("品牌: " + brand);
    }
}

// ❌ 不好的设计：所有方法都是抽象的（应该用接口）
public abstract class BadDesign {
    public abstract void method1();
    public abstract void method2();
    public abstract void method3();
}
```

### 9.2 命名规范

```java
// ✅ 推荐：使用 Abstract 前缀或描述性名称
public abstract class AbstractShape { }
public abstract class BaseController { }
public abstract class Animal { }

// ❌ 不推荐：名称不清晰
public abstract class A { }
public abstract class MyClass { }
```

## 十、总结

### 10.1 核心要点

1. **抽象类不能实例化**，必须被继承
2. **可以包含抽象方法和普通方法**
3. **子类必须实现所有抽象方法**，或声明为抽象类
4. **可以有构造方法**，供子类调用
5. **体现了模板设计模式**

### 10.2 使用场景

|场景|说明|
|---|---|
|**共同行为**|多个类有共同的方法和属性|
|**部分实现**|有些方法有默认实现，有些需要子类实现|
|**模板模式**|定义算法骨架，细节由子类实现|
|**代码复用**|避免在多个子类中重复代码|

### 10.3 记忆口诀

- **抽象类**：不能实例化，可有抽象方法
- **抽象方法**：只有声明，没有实现
- **子类责任**：必须实现或继续抽象
- **设计目的**：定义规范，提供模板