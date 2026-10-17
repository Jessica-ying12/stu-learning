## 一、异常概述

### 1.1 什么是异常？

**异常(Exception)**：程序运行时发生的不被期望的事件，它阻止程序按预期正常执行。

### 1.2 异常产生的原因

|原因类型|示例|
|---|---|
|用户输入错误|输入非法数据|
|程序错误|数组越界、空指针|
|物理错误|文件不存在、网络中断|
|系统错误|JVM 内存溢出|

### 1.3 错误 vs 异常

```java
// 错误示例（编译错误）
System.out.println("Hello")  // 缺少分号 -> java.lang.Error

// 异常示例（运行时异常）
System.out.println(11/0);    // 除以零 -> java.lang.ArithmeticException
```

---

## 二、异常的分类

### 2.1 三种异常类型

|类型|说明|特点|
|---|---|---|
|**检查性异常**|用户错误或外部问题引起|编译时必须处理|
|**运行时异常**|程序逻辑错误|编译时可以忽略|
|**错误(Error)**|系统级严重问题|程序无法处理|

### 2.2 异常类层次结构

```
java.lang.Object
    └── java.lang.Throwable
            ├── java.lang.Error (错误)
            │       └── OutOfMemoryError
            │       └── StackOverflowError
            └── java.lang.Exception (异常)
                    ├── IOException (检查性异常)
                    │       └── FileNotFoundException
                    └── RuntimeException (运行时异常)
                            ├── NullPointerException
                            ├── ArrayIndexOutOfBoundsException
                            └── ArithmeticException
```

---

## 三、常见异常类型

### 3.1 非检查性异常（运行时异常）

|异常类|描述|
|---|---|
|`ArithmeticException`|算术异常（如除以零）|
|`NullPointerException`|空指针异常|
|`ArrayIndexOutOfBoundsException`|数组索引越界|
|`ClassCastException`|类型转换异常|
|`IllegalArgumentException`|非法参数异常|
|`NumberFormatException`|数字格式异常|
|`IndexOutOfBoundsException`|索引越界异常|
|`NegativeArraySizeException`|负数组大小异常|
|`StringIndexOutOfBoundsException`|字符串索引越界|

### 3.2 检查性异常

|异常类|描述|
|---|---|
|`IOException`|输入输出异常|
|`FileNotFoundException`|文件未找到异常|
|`ClassNotFoundException`|类未找到异常|
|`CloneNotSupportedException`|克隆不支持异常|
|`IllegalAccessException`|非法访问异常|
|`InstantiationException`|实例化异常|
|`InterruptedException`|线程中断异常|
|`NoSuchMethodException`|方法不存在异常|
|`NoSuchFieldException`|字段不存在异常|

---

## 四、Throwable 类的主要方法

|方法|描述|
|---|---|
|`String getMessage()`|返回异常的详细信息|
|`Throwable getCause()`|返回异常的原因|
|`String toString()`|返回类名和消息|
|`void printStackTrace()`|打印异常堆栈信息|
|`StackTraceElement[] getStackTrace()`|返回堆栈跟踪数组|
|`Throwable fillInStackTrace()`|填充堆栈跟踪信息|

---

## 五、异常处理机制

### 5.1 try-catch 基本语法

```java
try {
    // 可能抛出异常的代码
} catch (ExceptionType e) {
    // 异常处理代码
}
```

### 5.2 单个异常捕获示例

```java
import java.io.*;

public class ExcepTest {
    public static void main(String args[]) {
        try {
            int a[] = new int[2];
            System.out.println("Access element three: " + a[3]);
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Exception thrown: " + e);
        }
        System.out.println("Out of the block");
    }
}
```

**输出：**

```
Exception thrown: java.lang.ArrayIndexOutOfBoundsException: 3
Out of the block
```

### 5.3 多重捕获块

```java
try {
    // 程序代码
} catch (异常类型1 e1) {
    // 处理异常类型1
} catch (异常类型2 e2) {
    // 处理异常类型2
} catch (异常类型3 e3) {
    // 处理异常类型3
}
```

**示例：**

```java
try {
    file = new FileInputStream(fileName);
    x = (byte) file.read();
} catch (FileNotFoundException f) {
    f.printStackTrace();
    return -1;
} catch (IOException i) {
    i.printStackTrace();
    return -1;
}
```

**注意**：子类异常必须在父类异常之前捕获！

---

## 六、throws 和 throw 关键字

### 6.1 throws 声明异常

用于方法签名，声明方法可能抛出的异常。

```java
import java.io.*;

public class ClassName {
    // 声明抛出单个异常
    public void deposit(double amount) throws RemoteException {
        // 方法实现
        throw new RemoteException();
    }
    
    // 声明抛出多个异常
    public void withdraw(double amount) 
        throws RemoteException, InsufficientFundsException {
        // 方法实现
    }
}
```

### 6.2 throw 抛出异常

用于方法内部，主动抛出异常对象。

```java
public void checkAge(int age) {
    if (age < 18) {
        throw new IllegalArgumentException("年龄必须大于18岁");
    }
}
```

### 6.3 throws vs throw

|关键字|位置|作用|数量|
|---|---|---|---|
|`throws`|方法声明|声明可能抛出的异常|可以多个|
|`throw`|方法体内|主动抛出异常对象|只能一个|

---

## 七、finally 块

### 7.1 基本语法

```java
try {
    // 可能抛出异常的代码
} catch (ExceptionType e) {
    // 异常处理
} finally {
    // 无论是否发生异常都会执行
}
```

### 7.2 完整示例

```java
public class ExcepTest {
    public static void main(String args[]) {
        int a[] = new int[2];
        try {
            System.out.println("Access element three: " + a[3]);
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("Exception thrown: " + e);
        } finally {
            a[0] = 6;
            System.out.println("First element value: " + a[0]);
            System.out.println("The finally statement is executed");
        }
    }
}
```

**输出：**

```
Exception thrown: java.lang.ArrayIndexOutOfBoundsException: 3
First element value: 6
The finally statement is executed
```

### 7.3 finally 使用规则

- ✅ `finally` 块总是会执行（除非 JVM 退出）
- ✅ 常用于资源清理（关闭文件、数据库连接等）
- ❌ `catch` 不能独立于 `try` 存在
- ❌ `try` 后必须有 `catch` 或 `finally`（至少一个）
- ❌ `try`、`catch`、`finally` 之间不能添加其他代码

---

## 八、自定义异常

### 8.1 创建自定义异常类

```java
// 检查性异常：继承 Exception
class MyException extends Exception {
    public MyException(String message) {
        super(message);
    }
}

// 运行时异常：继承 RuntimeException
class MyRuntimeException extends RuntimeException {
    public MyRuntimeException(String message) {
        super(message);
    }
}
```

### 8.2 完整示例

#### 1. 定义自定义异常

```java
// 文件名：InsufficientFundsException.java
import java.io.*;

public class InsufficientFundsException extends Exception {
    private double amount;
    
    public InsufficientFundsException(double amount) {
        this.amount = amount;
    }
    
    public double getAmount() {
        return amount;
    }
}
```

#### 2. 使用自定义异常

```java
// 文件名：CheckingAccount.java
import java.io.*;

public class CheckingAccount {
    private double balance;
    private int number;
    
    public CheckingAccount(int number) {
        this.number = number;
    }
    
    public void deposit(double amount) {
        balance += amount;
    }
    
    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount <= balance) {
            balance -= amount;
        } else {
            double needs = amount - balance;
            throw new InsufficientFundsException(needs);
        }
    }
    
    public double getBalance() {
        return balance;
    }
    
    public int getNumber() {
        return number;
    }
}
```

#### 3. 测试自定义异常

```java
// 文件名：BankDemo.java
public class BankDemo {
    public static void main(String[] args) {
        CheckingAccount c = new CheckingAccount(101);
        System.out.println("Depositing $500...");
        c.deposit(500.00);
        
        try {
            System.out.println("\nWithdrawing $100...");
            c.withdraw(100.00);
            System.out.println("\nWithdrawing $600...");
            c.withdraw(600.00);
        } catch (InsufficientFundsException e) {
            System.out.println("Sorry, but you are short $" + e.getAmount());
            e.printStackTrace();
        }
    }
}
```

**输出：**

```
Depositing $500...

Withdrawing $100...

Withdrawing $600...
Sorry, but you are short $200.0
InsufficientFundsException
    at CheckingAccount.withdraw(CheckingAccount.java:25)
    at BankDemo.main(BankDemo.java:13)
```

---

## 九、异常处理最佳实践

### 9.1 异常处理原则

|原则|说明|
|---|---|
|**具体捕获**|优先捕获具体异常，而非通用异常|
|**及时处理**|在合适的层级处理异常|
|**不要吞异常**|不要捕获后什么都不做|
|**记录日志**|记录异常信息便于调试|
|**资源清理**|使用 finally 或 try-with-resources|

### 9.2 推荐写法

```java
// ✅ 推荐：try-with-resources（Java 7+）
try (FileInputStream fis = new FileInputStream("file.txt")) {
    // 使用资源
} catch (IOException e) {
    e.printStackTrace();
}
// 自动关闭资源

// ✅ 推荐：具体异常在前
try {
    // 代码
} catch (FileNotFoundException e) {
    // 处理文件未找到
} catch (IOException e) {
    // 处理其他 IO 异常
}

// ✅ 推荐：记录异常信息
catch (Exception e) {
    logger.error("操作失败", e);
    throw e;
}
```

### 9.3 不推荐写法

```java
// ❌ 不推荐：吞掉异常
try {
    // 代码
} catch (Exception e) {
    // 什么都不做
}

// ❌ 不推荐：捕获过于宽泛
try {
    // 代码
} catch (Exception e) {
    // 捕获所有异常
}

// ❌ 不推荐：父类异常在前
try {
    // 代码
} catch (IOException e) {
    // 永远不会执行
} catch (FileNotFoundException e) {
    // 编译错误！
}
```

---

## 十、异常处理流程图

```
程序执行
    ↓
进入 try 块
    ↓
是否发生异常？
    ├─ 否 → 跳过 catch → 执行 finally → 继续执行
    └─ 是 → 匹配 catch 块
            ├─ 匹配成功 → 执行 catch → 执行 finally → 继续执行
            └─ 匹配失败 → 向上抛出异常
```

---

## 十一、总结对比

### 11.1 异常类型对比

|特性|检查性异常|运行时异常|错误|
|---|---|---|---|
|继承自|Exception|RuntimeException|Error|
|编译检查|必须处理|可以不处理|不需要处理|
|常见场景|IO操作、网络|逻辑错误|系统崩溃|
|示例|IOException|NullPointerException|OutOfMemoryError|

### 11.2 关键字对比

|关键字|用途|位置|
|---|---|---|
|`try`|包裹可能异常的代码|代码块|
|`catch`|捕获并处理异常|try 后面|
|`finally`|无论如何都执行|catch 后面|
|`throw`|抛出异常对象|方法内部|
|`throws`|声明可能抛出的异常|方法签名|

### 11.3 常用模板

```java
// 模板1：基本异常处理
try {
    // 业务代码
} catch (SpecificException e) {
    // 处理特定异常
    e.printStackTrace();
} finally {
    // 清理资源
}

// 模板2：多重异常处理
try {
    // 业务代码
} catch (FileNotFoundException e) {
    // 处理文件未找到
} catch (IOException e) {
    // 处理IO异常
} catch (Exception e) {
    // 处理其他异常
} finally {
    // 清理资源
}

// 模板3：自定义异常
public void method() throws CustomException {
    if (condition) {
        throw new CustomException("错误信息");
    }
}
```

---

## 十二、重点记忆

1. **异常层次**：Throwable → Exception/Error
2. **必须处理**：检查性异常必须 try-catch 或 throws
3. **finally 执行**：无论是否异常都会执行
4. **异常顺序**：子类异常在前，父类异常在后
5. **资源管理**：使用 try-with-resources 自动关闭资源