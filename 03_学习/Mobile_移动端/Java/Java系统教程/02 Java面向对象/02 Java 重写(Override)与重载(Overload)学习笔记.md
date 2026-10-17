## 一、重写（Override）

### 1.1 概念

- **定义**：子类对父类允许访问的方法进行重新实现
- **特点**：外壳不变，核心重写
- **目的**：子类根据需要定义特定于自己的行为

### 1.2 基础示例

```java
class Animal {
    public void move() {
        System.out.println("动物可以移动");
    }
}

class Dog extends Animal {
    @Override
    public void move() {
        System.out.println("狗可以跑和走");
    }
}

public class TestDog {
    public static void main(String args[]) {
        Animal a = new Animal();  // Animal 对象
        Animal b = new Dog();     // Dog 对象
        
        a.move();  // 执行 Animal 类的方法
        b.move();  // 执行 Dog 类的方法
    }
}
```

**输出结果：**

```
动物可以移动
狗可以跑和走
```

### 1.3 多态性体现

```java
class Animal {
    public void move() {
        System.out.println("动物可以移动");
    }
}

class Dog extends Animal {
    @Override
    public void move() {
        System.out.println("狗可以跑和走");
    }
    
    public void bark() {
        System.out.println("狗可以吠叫");
    }
}

public class TestDog {
    public static void main(String args[]) {
        Animal a = new Animal();
        Animal b = new Dog();
        
        a.move();   // 执行 Animal 类的方法
        b.move();   // 执行 Dog 类的方法
        // b.bark(); // ❌ 编译错误！Animal 类型没有 bark 方法
    }
}
```

**重要说明：**

- 编译阶段：检查引用类型（Animal）
- 运行阶段：JVM 执行实际对象类型（Dog）的方法
- 引用类型决定可以调用哪些方法

## 二、方法重写的规则

### 2.1 核心规则

|规则项|说明|
|---|---|
|**参数列表**|必须与被重写方法完全相同|
|**返回类型**|Java 5-6：必须相同  <br>Java 7+：可以是父类返回值的派生类（协变返回类型）|
|**访问权限**|必须 ≥ 父类方法的访问权限|
|**方法名**|必须相同|

### 2.2 详细规则表

|项目|规则|
|---|---|
|参数列表|✅ 必须完全相同|
|返回类型|✅ 可以是父类返回类型的子类|
|访问修饰符|✅ 可以扩大，❌ 不能缩小|
|异常|✅ 可以减少或删除  <br>❌ 不能抛出新的或更广的强制异常|
|final 方法|❌ 不能被重写|
|static 方法|❌ 不能被重写（但可以再次声明）|
|构造方法|❌ 不能被重写|
|private 方法|❌ 不能被重写（子类看不到）|

### 2.3 访问权限规则

```java
class Parent {
    protected void method1() { }
    public void method2() { }
}

class Child extends Parent {
    // ✅ 正确：扩大访问权限
    @Override
    public void method1() { }
    
    // ✅ 正确：保持相同权限
    @Override
    public void method2() { }
    
    // ❌ 错误：缩小访问权限
    // @Override
    // protected void method2() { }  // 编译错误
}
```

### 2.4 包访问规则

|情况|可重写的方法|
|---|---|
|同一个包|除了 private 和 final 的所有方法|
|不同包|只能重写 public 和 protected 的非 final 方法|

## 三、super 关键字的使用

### 3.1 调用父类方法

```java
class Animal {
    public void move() {
        System.out.println("动物可以移动");
    }
}

class Dog extends Animal {
    @Override
    public void move() {
        super.move();  // 调用父类的 move 方法
        System.out.println("狗可以跑和走");
    }
}

public class TestDog {
    public static void main(String args[]) {
        Animal b = new Dog();
        b.move();
    }
}
```

**输出结果：**

```
动物可以移动
狗可以跑和走
```

### 3.2 super 的使用场景

```java
class Parent {
    protected String name = "Parent";
    
    public void display() {
        System.out.println("Parent display");
    }
}

class Child extends Parent {
    private String name = "Child";
    
    public void showNames() {
        System.out.println("子类 name: " + this.name);
        System.out.println("父类 name: " + super.name);
    }
    
    @Override
    public void display() {
        super.display();  // 调用父类方法
        System.out.println("Child display");
    }
}
```

## 四、重载（Overload）

### 4.1 概念

- **定义**：同一个类中，方法名相同但参数不同
- **特点**：方法名相同，参数列表不同
- **返回类型**：可以相同也可以不同

### 4.2 完整示例

```java
public class Overloading {
    // 无参方法
    public int test() {
        System.out.println("test1");
        return 1;
    }
    
    // 一个 int 参数
    public void test(int a) {
        System.out.println("test2");
    }
    
    // int 和 String 参数（顺序1）
    public String test(int a, String s) {
        System.out.println("test3");
        return "returntest3";
    }
    
    // String 和 int 参数（顺序2）
    public String test(String s, int a) {
        System.out.println("test4");
        return "returntest4";
    }
    
    public static void main(String[] args) {
        Overloading o = new Overloading();
        System.out.println(o.test());
        o.test(1);
        System.out.println(o.test(1, "test3"));
        System.out.println(o.test("test4", 1));
    }
}
```

**输出结果：**

```
test1
1
test2
test3
returntest3
test4
returntest4
```

## 五、重载规则

### 5.1 核心规则

|规则|说明|
|---|---|
|**参数列表**|✅ 必须改变（数量、类型或顺序）|
|**返回类型**|✅ 可以改变|
|**访问修饰符**|✅ 可以改变|
|**异常**|✅ 可以声明新的或更广的异常|
|**作用域**|同一个类或子类中|

### 5.2 参数列表的区分方式

```java
public class OverloadExample {
    // 1. 参数数量不同
    public void method(int a) { }
    public void method(int a, int b) { }
    
    // 2. 参数类型不同
    public void method(int a) { }
    public void method(double a) { }
    
    // 3. 参数顺序不同
    public void method(int a, String b) { }
    public void method(String a, int b) { }
    
    // ❌ 错误：仅返回类型不同不能重载
    // public int method(int a) { return 0; }
    // public void method(int a) { }  // 编译错误
}
```

### 5.3 构造方法重载

```java
public class Person {
    private String name;
    private int age;
    
    // 无参构造
    public Person() {
        this.name = "Unknown";
        this.age = 0;
    }
    
    // 一个参数
    public Person(String name) {
        this.name = name;
        this.age = 0;
    }
    
    // 两个参数
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

## 六、重写 vs 重载对比

### 6.1 核心区别表

|区别点|重载（Overload）|重写（Override）|
|---|---|---|
|**英文**|Overloading|Overriding|
|**发生位置**|同一个类或子类|父类与子类之间|
|**方法名**|必须相同|必须相同|
|**参数列表**|必须不同|必须相同|
|**返回类型**|可以不同|必须相同或协变|
|**访问修饰符**|可以不同|不能更严格|
|**异常**|可以不同|不能抛出新的强制异常|
|**多态性**|编译时多态（静态）|运行时多态（动态）|
|**关键字**|无|@Override（推荐）|

### 6.2 详细对比

```java
// 重载示例
class Calculator {
    // 方法名相同，参数不同
    public int add(int a, int b) {
        return a + b;
    }
    
    public double add(double a, double b) {
        return a + b;
    }
    
    public int add(int a, int b, int c) {
        return a + b + c;
    }
}

// 重写示例
class Animal {
    public void makeSound() {
        System.out.println("动物发出声音");
    }
}

class Dog extends Animal {
    @Override
    public void makeSound() {
        System.out.println("汪汪汪");
    }
}

class Cat extends Animal {
    @Override
    public void makeSound() {
        System.out.println("喵喵喵");
    }
}
```

## 七、多态性表现

### 7.1 重载的多态性（编译时）

```java
public class CompileTimePolymorphism {
    public void display(int a) {
        System.out.println("整数: " + a);
    }
    
    public void display(String a) {
        System.out.println("字符串: " + a);
    }
    
    public static void main(String[] args) {
        CompileTimePolymorphism obj = new CompileTimePolymorphism();
        obj.display(10);      // 编译时确定调用哪个方法
        obj.display("Hello"); // 编译时确定调用哪个方法
    }
}
```

### 7.2 重写的多态性（运行时）

```java
public class RuntimePolymorphism {
    public static void main(String[] args) {
        Animal[] animals = new Animal[3];
        animals[0] = new Animal();
        animals[1] = new Dog();
        animals[2] = new Cat();
        
        for (Animal animal : animals) {
            animal.makeSound();  // 运行时确定调用哪个方法
        }
    }
}
```

## 八、最佳实践

### 8.1 使用 @Override 注解

```java
class Parent {
    public void method() { }
}

class Child extends Parent {
    // ✅ 推荐：使用 @Override 注解
    @Override
    public void method() {
        // 如果父类没有这个方法，编译器会报错
    }
    
    // ❌ 不推荐：不使用注解
    public void method2() {
        // 如果拼写错误，不会被发现
    }
}
```

### 8.2 重载的最佳实践

```java
public class BestPractice {
    // ✅ 好的重载：参数类型明显不同
    public void process(String data) { }
    public void process(int data) { }
    
    // ⚠️ 避免：参数类型容易混淆
    public void calculate(long a, int b) { }
    public void calculate(int a, long b) { }
    
    // ✅ 好的做法：使用不同的方法名
    public void calculateLongInt(long a, int b) { }
    public void calculateIntLong(int a, long b) { }
}
```

## 九、常见错误

### 9.1 重写常见错误

```java
class Parent {
    public void method() throws IOException { }
}

class Child extends Parent {
    // ❌ 错误：抛出了更广的异常
    // @Override
    // public void method() throws Exception { }
    
    // ✅ 正确：抛出更窄的异常或不抛出
    @Override
    public void method() throws IOException { }
    
    // ✅ 正确：不抛出异常
    @Override
    public void method() { }
}
```

### 9.2 重载常见错误

```java
public class OverloadError {
    // ❌ 错误：仅返回类型不同
    // public int method(int a) { return 0; }
    // public void method(int a) { }
    
    // ❌ 错误：仅访问修饰符不同
    // public void method(int a) { }
    // private void method(int a) { }
}
```

## 十、总结

### 核心要点

1. **重写（Override）**
    
    - 父类与子类之间的多态性
    - 方法签名必须相同
    - 体现运行时多态
    - 使用 @Override 注解
2. **重载（Overload）**
    
    - 同一类中的多态性
    - 参数列表必须不同
    - 体现编译时多态
    - 常用于构造方法
3. **记忆口诀**
    
    - 重写：子承父业，改头换面
    - 重载：同名不同参，各司其职