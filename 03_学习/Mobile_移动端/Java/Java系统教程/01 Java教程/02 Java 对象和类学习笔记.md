## 一、面向对象程序设计基础

### 1.1 面向对象的核心概念

Java 作为面向对象语言，支持以下基本概念：

- **多态（Polymorphism）**
- **继承（Inheritance）**
- **封装（Encapsulation）**
- **抽象（Abstraction）**
- **类（Class）**
- **对象（Object）**
- **实例（Instance）**
- **方法（Method）**
- **消息解析（Message Parsing）**

### 1.2 对象与类的定义

- **对象**：类的实例，具有状态和行为
    - 示例：一条狗
    - 状态：颜色、名字、品种
    - 行为：摇尾巴、叫、吃
- **类**：创建对象的模板，描述一类对象的行为和状态

---

## 二、Java 中的对象

### 2.1 对象的特征

现实世界中的对象（车、狗、人等）都有：
- **状态**：对象的属性
- **行为**：对象的方法

软件对象与现实对象相似：
- **状态** → 属性（成员变量）
- **行为** → 方法

### 2.2 对象间的交互

- 方法操作对象内部状态的改变
- 对象间通过方法调用进行通信

---

## 三、Java 中的类

### 3.1 类的定义示例

```java
public class Dog {
    String breed;  // 品种
    int age;       // 年龄
    String color;  // 颜色
    
    void barking() {
        // 叫
    }
    
    void hungry() {
        // 饥饿
    }
    
    void sleeping() {
        // 睡觉
    }
}
```

### 3.2 类中的变量类型

#### 1. 局部变量

- 定义在方法、构造方法或语句块中
- 方法结束后自动销毁
- 作用域仅限于声明的代码块内

#### 2. 成员变量（实例变量）

- 定义在类中，方法体之外
- 创建对象时实例化
- 可被类中的方法、构造方法访问

#### 3. 类变量（静态变量）

- 声明在类中，方法体之外
- 必须声明为 `static` 类型
- 所有实例共享同一个类变量

---

## 四、构造方法

### 4.1 构造方法的特点

- 名称必须与类名相同
- 没有返回值类型
- 创建对象时至少调用一个构造方法
- 如果没有显式定义，编译器提供默认构造方法
- 一个类可以有多个构造方法（重载）

### 4.2 构造方法示例

```java
public class Puppy {
    // 无参构造方法
    public Puppy() {
    }
    
    // 带参构造方法
    public Puppy(String name) {
        // 这个构造器仅有一个参数：name
    }
}
```

---

## 五、创建对象

### 5.1 创建对象的三个步骤

1. **声明**：声明对象名称和类型
2. **实例化**：使用 `new` 关键字创建对象
3. **初始化**：调用构造方法初始化对象

### 5.2 创建对象示例

```java
public class Puppy {
    public Puppy(String name) {
        System.out.println("Puppy Name is :" + name);
    }
    
    public static void main(String[] args) {
        // 创建 Puppy 对象
        Puppy myPuppy = new Puppy("tommy");
    }
}
```

**输出**：

```
Puppy Name is :tommy
```

---

## 六、访问实例变量和方法

通过已创建的对象来访问成员变量和成员方法

### 6.1 访问语法

```java
/* 实例化对象 */
ObjectReference = new Constructor();

/* 访问变量 */
ObjectReference.variableName;

/* 访问方法 */
ObjectReference.MethodName();
```

### 6.2 完整示例

```java
public class Puppy {
    int puppyAge;
    
    public Puppy(String name) {
        System.out.println("Passed Name is :" + name);
    }
    
    public void setAge(int age) {
        puppyAge = age;
    }
    
    public int getAge() {
        System.out.println("Puppy's age is :" + puppyAge);
        return puppyAge;
    }
    
    public static void main(String[] args) {
        /* 创建对象 */
        Puppy myPuppy = new Puppy("tommy");
        
        /* 通过方法设置 age */
        myPuppy.setAge(2);
        
        /* 调用方法获取 age */
        myPuppy.getAge();
        
        /* 直接访问成员变量 */
        System.out.println("Variable Value :" + myPuppy.puppyAge);
    }
}
```

**输出**：

```
Passed Name is :tommy
Puppy's age is :2
Variable Value :2
```

---

## 七、源文件声明规则

### 7.1 重要规则

1. 一个源文件中只能有**一个 public 类**
2. 一个源文件可以有**多个非 public 类**
3. 源文件名称应与 **public 类名保持一致**
    - 例如：public 类名为 `Employee`，文件名应为 `Employee.java`
4. `package` 语句应在源文件**首行**
5. `import` 语句应在 `package` 和类定义**之间**
6. 如果没有 `package` 语句，`import` 应在**最前面**
7. `import` 和 `package` 对源文件中**所有类**都有效
8. 同一源文件中，不能给不同类不同的包声明

### 7.2 文件结构顺序

```java
package 包名;           // 第一行（可选）

import 导入语句;        // 第二部分（可选）

public class 类名 {    // 第三部分
    // 类定义
}
```

---

## 八、Java 包（Package）

### 8.1 包的作用

- 对类和接口进行分类
- 避免命名冲突
- 便于管理大量的类

### 8.2 Import 语句

提供路径，使编译器能找到类：

```java
import java.io.*;  // 导入 java.io 包下的所有类
```

---

## 九、完整实例：Employee 管理系统

### 9.1 Employee 类

```java
import java.io.*;

public class Employee {
    String name;
    int age;
    String designation;
    double salary;
    
    // 构造器
    public Employee(String name) {
        this.name = name;
    }
    
    // 设置 age
    public void empAge(int empAge) {
        age = empAge;
    }
    
    // 设置 designation
    public void empDesignation(String empDesig) {
        designation = empDesig;
    }
    
    // 设置 salary
    public void empSalary(double empSalary) {
        salary = empSalary;
    }
    
    // 打印信息
    public void printEmployee() {
        System.out.println("Name:" + name);
        System.out.println("Age:" + age);
        System.out.println("Designation:" + designation);
        System.out.println("Salary:" + salary);
    }
}
```

### 9.2 EmployeeTest 测试类

```java
import java.io.*;

public class EmployeeTest {
    public static void main(String[] args) {
        /* 使用构造器创建两个对象 */
        Employee empOne = new Employee("James Smith");
        Employee empTwo = new Employee("Mary Anne");
        
        // 调用对象的成员方法
        empOne.empAge(26);
        empOne.empDesignation("Senior Software Engineer");
        empOne.empSalary(1000);
        empOne.printEmployee();
        
        empTwo.empAge(21);
        empTwo.empDesignation("Software Engineer");
        empTwo.empSalary(500);
        empTwo.printEmployee();
    }
}
```

### 9.3 编译和运行

```bash
javac Employee.java
javac EmployeeTest.java
java EmployeeTest
```

**输出**：

```
Name:James Smith
Age:26
Designation:Senior Software Engineer
Salary:1000.0
Name:Mary Anne
Age:21
Designation:Software Engineer
Salary:500.0
```

---

## 十、关键知识点总结

### 10.1 类与对象的关系

- 类是对象的**模板/蓝图**
- 对象是类的**实例**
- 一个类可以创建**多个对象**

### 10.2 访问控制

- 使用 `public`、`private`、`protected` 控制访问权限
- 成员变量通常设为 `private`
- 提供 `public` 方法访问私有变量（封装）

### 10.3 this 关键字

- 引用当前对象
- 区分成员变量和局部变量
- 示例：`this.name = name;`

### 10.4 最佳实践

1. 类名使用大驼峰命名法
2. 方法名和变量名使用小驼峰命名法
3. 成员变量私有化，提供 getter/setter 方法
4. 构造方法用于初始化对象状态
5. 一个源文件一个 public 类