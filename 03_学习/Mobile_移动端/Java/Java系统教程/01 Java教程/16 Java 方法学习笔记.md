## 一、方法概述

### 什么是方法？

- 方法是语句的集合，它们在一起执行一个功能
- 方法是解决一类问题的步骤的有序组合
- 方法包含于类或对象中
- 方法在程序中被创建，在其他地方被引用

### System.out.println() 解析

```java
System.out.println("Hello");
```

- `System`：系统类（Class）
- `out`：标准输出对象（Object）
- `println()`：方法（Method）

## 二、方法的定义

### 基本语法

```java
修饰符 返回值类型 方法名(参数类型 参数名) {
    // 方法体
    return 返回值;
}
```

### 方法的组成部分

|部分|说明|
|---|---|
|**修饰符**|可选，定义访问类型（如 public、static）|
|**返回值类型**|方法返回值的数据类型，无返回值用 void|
|**方法名**|方法的实际名称|
|**参数列表**|参数类型、顺序和个数（可选）|
|**方法体**|具体的语句，定义方法功能|

### 示例

```java
// 单个参数
public static int age(int birthday) {
    // 方法体
}

// 多个参数
static float interest(float principal, int year) {
    // 方法体
}
```

### 术语说明

- **函数**：返回非 void 类型的方法
- **过程**：返回 void 类型的方法

## 三、方法调用

### 两种调用方式

#### 1. 返回值方法调用

```java
int larger = max(30, 40);  // 作为值使用
```

#### 2. void 方法调用

```java
System.out.println("Welcome to Java!");  // 作为语句使用
```

### 完整示例

```java
public class TestMax {
    public static void main(String[] args) {
        int i = 5;
        int j = 2;
        int k = max(i, j);
        System.out.println("The maximum between " + i + 
                          " and " + j + " is " + k);
    }
    
    public static int max(int num1, int num2) {
        int result;
        if (num1 > num2) {
            result = num1;
        } else {
            result = num2;
        }
        return result;
    }
}
// 输出: The maximum between 5 and 2 is 5
```

## 四、void 关键字

### void 方法示例

```java
public class TestVoidMethod {
    public static void main(String[] args) {
        printGrade(78.5);
    }
    
    public static void printGrade(double score) {
        if (score >= 90.0) {
            System.out.println('A');
        } else if (score >= 80.0) {
            System.out.println('B');
        } else if (score >= 70.0) {
            System.out.println('C');
        } else if (score >= 60.0) {
            System.out.println('D');
        } else {
            System.out.println('F');
        }
    }
}
// 输出: C
```

### void 方法特点

- 不返回值
- 调用时必须作为语句（以分号结束）
- 不能作为表达式的一部分

## 五、参数传递

### 按值传递

Java 中所有参数都是按值传递的。

```java
public class TestPassByValue {
    public static void main(String[] args) {
        int num1 = 1;
        int num2 = 2;
        
        System.out.println("Before swap: num1=" + num1 + ", num2=" + num2);
        swap(num1, num2);
        System.out.println("After swap: num1=" + num1 + ", num2=" + num2);
    }
    
    public static void swap(int n1, int n2) {
        System.out.println("\tInside swap method");
        System.out.println("\t\tBefore: n1=" + n1 + ", n2=" + n2);
        
        int temp = n1;
        n1 = n2;
        n2 = temp;
        
        System.out.println("\t\tAfter: n1=" + n1 + ", n2=" + n2);
    }
}
```

**输出结果：**

```
Before swap: num1=1, num2=2
    Inside swap method
        Before: n1=1, n2=2
        After: n1=2, n2=1
After swap: num1=1, num2=2
```

**重要结论**：方法内部对参数的修改不会影响原始变量的值。

## 六、方法重载（Overloading）

### 概念

- 同一个类中，多个方法拥有相同的名字但不同的参数列表
- 编译器根据方法签名判断调用哪个方法

### 示例

```java
// int 类型的 max 方法
public static int max(int num1, int num2) {
    if (num1 > num2) {
        return num1;
    } else {
        return num2;
    }
}

// double 类型的 max 方法
public static double max(double num1, double num2) {
    if (num1 > num2) {
        return num1;
    } else {
        return num2;
    }
}
```

### 重载规则

- ✅ 必须有不同的参数列表
- ✅ 可以有不同的返回类型
- ✅ 可以有不同的访问修饰符
- ❌ 不能仅通过返回类型不同来重载
- ❌ 不能仅通过修饰符不同来重载

## 七、变量作用域

### 局部变量

- 在方法内定义的变量
- 作用范围：从声明开始到包含它的块结束
- 必须先声明才能使用

### 方法参数

- 参数实际上是局部变量
- 作用范围：整个方法

### for 循环变量

```java
for (int i = 0; i < 10; i++) {
    // i 的作用域在整个循环内
    int temp = i * 2;  // temp 的作用域从声明到循环体结束
}
```

### 作用域规则

- ✅ 可以在不同的非嵌套块中声明同名变量
- ❌ 不能在嵌套块内重复声明同名变量

## 八、命令行参数

### 使用方式

```java
public class CommandLine {
    public static void main(String args[]) {
        for (int i = 0; i < args.length; i++) {
            System.out.println("args[" + i + "]: " + args[i]);
        }
    }
}
```

### 运行示例

```bash
java CommandLine this is a command line 200 -100
```

**输出：**

```
args[0]: this
args[1]: is
args[2]: a
args[3]: command
args[4]: line
args[5]: 200
args[6]: -100
```

## 九、构造方法

### 特点

- 方法名与类名相同
- 没有返回值类型（连 void 都不写）
- 用于初始化对象
- 如果不定义，Java 提供默认构造方法

### 无参构造方法

```java
class MyClass {
    int x;
    
    // 构造方法
    MyClass() {
        x = 10;
    }
}

// 使用
MyClass t1 = new MyClass();
MyClass t2 = new MyClass();
System.out.println(t1.x + " " + t2.x);  // 输出: 10 10
```

### 有参构造方法

```java
class MyClass {
    int x;
    
    // 带参数的构造方法
    MyClass(int i) {
        x = i;
    }
}

// 使用
MyClass t1 = new MyClass(10);
MyClass t2 = new MyClass(20);
System.out.println(t1.x + " " + t2.x);  // 输出: 10 20
```

## 十、可变参数（JDK 1.5+）

### 语法

```java
typeName... parameterName
```

### 规则

- 在参数类型后加省略号（...）
- 一个方法只能有一个可变参数
- 必须是方法的最后一个参数
- 普通参数必须在可变参数之前

### 示例

```java
public class VarargsDemo {
    public static void main(String args[]) {
        printMax(34, 3, 3, 2, 56.5);
        printMax(new double[]{1, 2, 3});
    }
    
    public static void printMax(double... numbers) {
        if (numbers.length == 0) {
            System.out.println("No argument passed");
            return;
        }
        
        double result = numbers[0];
        for (int i = 1; i < numbers.length; i++) {
            if (numbers[i] > result) {
                result = numbers[i];
            }
        }
        System.out.println("The max value is " + result);
    }
}
```

**输出：**

```
The max value is 56.5
The max value is 3.0
```

## 十一、finalize() 方法

### 概念

- 在对象被垃圾收集器回收前调用
- 用于清理资源（如关闭文件）
- 由 JVM 自动调用

### 语法

```java
protected void finalize() throws java.lang.Throwable {
    // 清理代码
}
```

### 示例

```java
public class FinalizationDemo {
    public static void main(String[] args) {
        Cake c1 = new Cake(1);
        Cake c2 = new Cake(2);
        Cake c3 = new Cake(3);
        
        c2 = c3 = null;
        System.gc();  // 调用垃圾收集器
    }
}

class Cake extends Object {
    private int id;
    
    public Cake(int id) {
        this.id = id;
        System.out.println("Cake Object " + id + " is created");
    }
    
    protected void finalize() throws java.lang.Throwable {
        super.finalize();
        System.out.println("Cake Object " + id + " is disposed");
    }
}
```

**输出：**

```
Cake Object 1 is created
Cake Object 2 is created
Cake Object 3 is created
Cake Object 3 is disposed
Cake Object 2 is disposed
```

### 注意事项

- `protected` 修饰符确保方法不被外部调用
- Java 的内存回收通常由 JVM 自动完成
- 不建议过度依赖 finalize() 方法

## 十二、方法最佳实践

1. **命名规范**：使用动词或动词短语，采用驼峰命名法
2. **单一职责**：一个方法只做一件事
3. **参数数量**：尽量控制在 3 个以内
4. **返回值**：明确返回值的含义
5. **异常处理**：合理处理可能的异常情况
6. **注释文档**：为公共方法添加 JavaDoc 注释