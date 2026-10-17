## 一、变量声明基础

### 1.1 变量声明规则

在 Java 中，所有变量在使用前必须声明。

### 1.2 声明格式

```java
type identifier [ = value][, identifier [= value] ...] ;
```

**格式说明**：

- `type`：Java 数据类型
- `identifier`：变量名
- 可以使用逗号隔开声明多个同类型变量

### 1.3 声明示例

```java
int a, b, c;                    // 声明三个 int 型整数
int d = 3, e, f = 5;            // 声明三个整数并赋初值
byte z = 22;                    // 声明并初始化 z
double pi = 3.14159;            // 声明 pi
char x = 'x';                   // 变量 x 的值是字符 'x'
```

---

## 二、Java 变量的三种类型

### 2.1 变量类型概览

| 变量类型      | 位置     | 修饰符      | 说明     |
| --------- | ------ | -------- | ------ |
| 局部变量      | 方法内部   | 无        | 方法中的变量 |
| 实例变量      | 类中，方法外 | 无 static | 对象的属性  |
| 类变量（静态变量） | 类中，方法外 | static   | 类的属性   |

### 2.2 示例代码

```java
public class Variable {
    static int allClicks = 0;      // 类变量
    String str = "hello world";    // 实例变量
    
    public void method() {
        int i = 0;                 // 局部变量
    }
}
```

---

## 三、局部变量（Local Variables）

### 3.1 局部变量的特点

1. **声明位置**：在方法、构造方法或语句块中
2. **生命周期**：
    - 创建：方法、构造方法或语句块执行时
    - 销毁：执行完成后
3. **访问修饰符**：不能使用访问修饰符
4. **作用域**：只在声明它的方法、构造方法或语句块中可见
5. **存储位置**：栈（Stack）上分配
6. **默认值**：没有默认值，必须初始化后才能使用

### 3.2 实例 1：正确使用局部变量

```java
public class Test {
    public void pupAge() {
        int age = 0;              // 声明并初始化
        age = age + 7;
        System.out.println("Puppy age is : " + age);
    }
    
    public static void main(String[] args) {
        Test test = new Test();
        test.pupAge();
    }
}
```

**输出**：

```
Puppy age is: 7
```

### 3.3 实例 2：未初始化的局部变量（错误示例）

```java
public class Test {
    public void pupAge() {
        int age;                  // 只声明，未初始化
        age = age + 7;            // 编译错误！
        System.out.println("Puppy age is : " + age);
    }
    
    public static void main(String[] args) {
        Test test = new Test();
        test.pupAge();
    }
}
```

**编译错误**：

```
Test.java:4: variable number might not have been initialized
age = age + 7;
^
1 error
```

---

## 四、实例变量（Instance Variables）

### 4.1 实例变量的特点

1. **声明位置**：类中，但在方法、构造方法和语句块之外
2. **生命周期**：
    - 创建：对象创建时
    - 销毁：对象销毁时
3. **访问修饰符**：可以使用访问修饰符（public、private、protected）
4. **作用域**：对类中的方法、构造方法或语句块可见
5. **可见性**：一般设为私有（private），通过方法访问
6. **默认值**：有默认值
    - 数值型：`0`
    - 布尔型：`false`
    - 引用类型：`null`
7. **初始化**：可以在声明时指定，也可以在构造方法中指定
8. **访问方式**：
    - 类内部：直接使用变量名
    - 静态方法或其他类：`ObjectReference.VariableName`

### 4.2 实例变量示例

```java
import java.io.*;

public class Employee {
    // 实例变量：对子类可见
    public String name;
    
    // 实例变量：私有，仅在该类可见
    private double salary;
    
    // 构造器中对 name 赋值
    public Employee(String empName) {
        name = empName;
    }
    
    // 设定 salary 的值
    public void setSalary(double empSal) {
        salary = empSal;
    }
    
    // 打印信息
    public void printEmp() {
        System.out.println("name  : " + name);
        System.out.println("salary :" + salary);
    }
    
    public static void main(String[] args) {
        Employee empOne = new Employee("Ransika");
        empOne.setSalary(1000);
        empOne.printEmp();
    }
}
```

**输出**：

```
name  : Ransika
salary :1000.0
```

---

## 五、类变量/静态变量（Class Variables / Static Variables）

### 5.1 类变量的特点

1. **声明位置**：类中，用 `static` 关键字声明，但在方法、构造方法和语句块之外
2. **共享性**：无论创建多少个对象，类只拥有类变量的一份拷贝
3. **存储位置**：静态存储区
4. **生命周期**：
    - 创建：程序开始时
    - 销毁：程序结束时
5. **默认值**：与实例变量相似
    - 数值型：`0`
    - 布尔型：`false`
    - 引用类型：`null`
6. **初始化**：
    - 声明时指定
    - 构造方法中指定
    - 静态语句块中初始化
7. **访问方式**：`ClassName.VariableName`
8. **常量声明**：`public static final` 类型时，变量名必须大写

### 5.2 类变量使用场景

- 常量定义（`public static final`）
- 共享数据（所有对象共享）
- 计数器（如统计对象创建次数）

### 5.3 类变量示例

```java
import java.io.*;

public class Employee {
    // 静态私有变量
    private static double salary;
    
    // 常量（public static final）
    public static final String DEPARTMENT = "Development ";
    
    public static void main(String[] args) {
        salary = 1000;
        System.out.println(DEPARTMENT + "average salary:" + salary);
    }
}
```

**输出**：

```
Development average salary:1000
```

### 5.4 访问类变量

其他类访问类变量：

```java
// 访问常量
String dept = Employee.DEPARTMENT;

// 如果是 public static 变量
// int count = Employee.employeeCount;
```

---

## 六、三种变量类型对比

### 6.1 详细对比表

|特性|局部变量|实例变量|类变量|
|---|---|---|---|
|**声明位置**|方法/构造方法/语句块内|类中，方法外|类中，方法外|
|**修饰符**|无|无 static|static|
|**访问修饰符**|不能使用|可以使用|可以使用|
|**默认值**|无（必须初始化）|有默认值|有默认值|
|**生命周期**|方法执行期间|对象存在期间|程序运行期间|
|**存储位置**|栈（Stack）|堆（Heap）|静态存储区|
|**作用域**|声明的代码块内|整个类内|整个类内|
|**访问方式**|直接访问|对象.变量名|类名.变量名|
|**共享性**|不共享|每个对象独立|所有对象共享|

### 6.2 默认值对比

|数据类型|默认值|
|---|---|
|byte|0|
|short|0|
|int|0|
|long|0L|
|float|0.0f|
|double|0.0d|
|char|'\u0000'|
|boolean|false|
|引用类型|null|

**注意**：局部变量没有默认值！

---

## 七、变量命名规范

### 7.1 命名规则

1. **局部变量和实例变量**：
    
    - 小驼峰命名法（lowerCamelCase）
    - 示例：`userName`、`totalCount`、`isActive`
2. **类变量（非常量）**：
    
    - 小驼峰命名法
    - 示例：`employeeCount`、`maxSize`
3. **常量（public static final）**：
    
    - 全大写，下划线分隔
    - 示例：`MAX_VALUE`、`DEFAULT_SIZE`、`PI`

### 7.2 命名示例

```java
public class Example {
    // 常量：全大写
    public static final int MAX_SIZE = 100;
    public static final String DEFAULT_NAME = "Unknown";
    
    // 类变量：小驼峰
    private static int instanceCount = 0;
    
    // 实例变量：小驼峰
    private String userName;
    private int age;
    
    public void method() {
        // 局部变量：小驼峰
        int localVar = 10;
        String tempName = "temp";
    }
}
```

---

## 八、变量使用最佳实践

### 8.1 局部变量

```java
public void calculate() {
    // ✅ 声明时立即初始化
    int sum = 0;
    
    // ❌ 避免未初始化就使用
    // int count;
    // count = count + 1;  // 编译错误
}
```

### 8.2 实例变量

```java
public class User {
    // ✅ 私有化实例变量
    private String name;
    private int age;
    
    // ✅ 提供 getter/setter 方法
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
}
```

### 8.3 类变量

```java
public class Counter {
    // ✅ 常量使用 public static final
    public static final int MAX_COUNT = 100;
    
    // ✅ 共享数据使用 private static
    private static int count = 0;
    
    // ✅ 提供静态方法访问
    public static int getCount() {
        return count;
    }
    
    public static void increment() {
        count++;
    }
}
```

---

## 九、重要知识点总结

### 9.1 关键区别

1. **局部变量**：
    
    - 必须初始化
    - 作用域最小
    - 生命周期最短
2. **实例变量**：
    
    - 有默认值
    - 属于对象
    - 每个对象独立
3. **类变量**：
    
    - 有默认值
    - 属于类
    - 所有对象共享

### 9.2 使用建议

1. 优先使用局部变量（作用域最小化原则）
2. 实例变量设为 private，提供访问方法
3. 类变量用于常量和共享数据
4. 常量使用 `public static final` 并全大写命名
5. 局部变量声明时立即初始化

### 9.3 常见错误

```java
// ❌ 局部变量未初始化
public void method() {
    int x;
    System.out.println(x);  // 编译错误
}

// ❌ 在静态方法中直接访问实例变量
public class Test {
    int instanceVar = 10;
    
    public static void main(String[] args) {
        System.out.println(instanceVar);  // 编译错误
    }
}

// ✅ 正确方式
public static void main(String[] args) {
    Test obj = new Test();
    System.out.println(obj.instanceVar);  // 正确
}
```