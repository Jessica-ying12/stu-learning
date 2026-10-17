## 一、Scanner 类概述

### 基本信息

- **包路径**：`java.util.Scanner`
- **引入版本**：Java 5
- **主要功能**：获取用户输入

### 创建 Scanner 对象

```java
Scanner s = new Scanner(System.in);
```

## 二、字符串输入方法

### 1. next() 方法

#### 示例代码

```java
import java.util.Scanner;

public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        
        System.out.println("next方式接收：");
        if (scan.hasNext()) {
            String str1 = scan.next();
            System.out.println("输入的数据为：" + str1);
        }
    }
}
```

#### 运行结果

```
next方式接收：
youj com
输入的数据为：youj
```

#### next() 方法特点

1. 必须读取到有效字符后才能结束输入
2. 自动去掉输入前的空白字符
3. 遇到空白字符（空格、Tab、Enter）作为结束符
4. **不能获取带空格的字符串**

### 2. nextLine() 方法

#### 示例代码

```java
import java.util.Scanner;

public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        
        System.out.println("nextLine方式接收：");
        if (scan.hasNextLine()) {
            String str2 = scan.nextLine();
            System.out.println("输入的数据为：" + str2);
        }
    }
}
```

#### 运行结果

```
nextLine方式接收：
youj com
输入的数据为：youj com
```

#### nextLine() 方法特点

1. 以 Enter（回车）为结束符
2. 返回回车之前的所有字符
3. **可以获取带空格的字符串**

### 3. next() vs nextLine() 对比

|特性|next()|nextLine()|
|---|---|---|
|结束符|空白字符（空格/Tab/Enter）|Enter（回车）|
|空格处理|作为分隔符，不能获取|可以获取|
|前导空白|自动去除|保留|
|使用场景|单个单词输入|整行文本输入|

## 三、数值类型输入

### 基本数值输入示例

```java
import java.util.Scanner;

public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        int i = 0;
        float f = 0.0f;
        
        // 输入整数
        System.out.print("输入整数：");
        if (scan.hasNextInt()) {
            i = scan.nextInt();
            System.out.println("整数数据：" + i);
        } else {
            System.out.println("输入的不是整数！");
        }
        
        // 输入小数
        System.out.print("输入小数：");
        if (scan.hasNextFloat()) {
            f = scan.nextFloat();
            System.out.println("小数数据：" + f);
        } else {
            System.out.println("输入的不是小数！");
        }
    }
}
```

#### 运行结果

```
输入整数：12
整数数据：12
输入小数：1.2
小数数据：1.2
```

### Scanner 常用方法总览

|方法|说明|
|---|---|
|`hasNext()`|判断是否还有输入|
|`next()`|读取字符串（遇空白结束）|
|`hasNextLine()`|判断是否还有一行输入|
|`nextLine()`|读取一行字符串|
|`hasNextInt()`|判断是否为整数|
|`nextInt()`|读取整数|
|`hasNextFloat()`|判断是否为浮点数|
|`nextFloat()`|读取浮点数|
|`hasNextDouble()`|判断是否为双精度浮点数|
|`nextDouble()`|读取双精度浮点数|
|`hasNextBoolean()`|判断是否为布尔值|
|`nextBoolean()`|读取布尔值|
|`hasNextByte()`|判断是否为字节|
|`nextByte()`|读取字节|
|`hasNextShort()`|判断是否为短整型|
|`nextShort()`|读取短整型|
|`hasNextLong()`|判断是否为长整型|
|`nextLong()`|读取长整型|

## 四、实战案例：计算总和与平均值

### 示例代码

```java
import java.util.Scanner;

class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        
        double sum = 0;
        int m = 0;
        
        System.out.println("请输入数字（输入非数字结束）：");
        
        while (scan.hasNextDouble()) {
            double x = scan.nextDouble();
            m = m + 1;
            sum = sum + x;
        }
        
        System.out.println(m + "个数的和为" + sum);
        System.out.println(m + "个数的平均值是" + (sum / m));
    }
}
```

### 运行结果

```
请输入数字（输入非数字结束）：
12
23
15
21.4
end
4个数的和为71.4
4个数的平均值是17.85
```

### 代码解析

1. 使用 `while(scan.hasNextDouble())` 循环读取数字
2. 每输入一个数字用回车确认
3. 输入非数字（如 "end"）时循环结束
4. 计算并输出总和与平均值

## 五、使用注意事项

### 1. 输入验证

```java
// 推荐做法：先验证再读取
if (scan.hasNextInt()) {
    int num = scan.nextInt();
} else {
    System.out.println("输入格式错误！");
}
```

### 2. 混合使用 next() 和 nextLine() 的问题

```java
Scanner scan = new Scanner(System.in);

System.out.print("输入数字：");
int num = scan.nextInt();  // 读取数字，但不消耗换行符

System.out.print("输入字符串：");
String str = scan.nextLine();  // 会读取到上一行的换行符，导致跳过

// 解决方案：在 nextInt() 后添加 nextLine() 消耗换行符
scan.nextLine();  // 消耗换行符
String str = scan.nextLine();  // 正常读取
```

### 3. 资源关闭

```java
Scanner scan = new Scanner(System.in);
try {
    // 使用 Scanner
} finally {
    scan.close();  // 关闭资源
}

// 或使用 try-with-resources（推荐）
try (Scanner scan = new Scanner(System.in)) {
    // 使用 Scanner
}  // 自动关闭
```

## 六、常见应用场景

### 1. 菜单选择

```java
Scanner scan = new Scanner(System.in);
System.out.println("请选择操作：");
System.out.println("1. 添加");
System.out.println("2. 删除");
System.out.println("3. 退出");

if (scan.hasNextInt()) {
    int choice = scan.nextInt();
    switch (choice) {
        case 1:
            System.out.println("执行添加操作");
            break;
        case 2:
            System.out.println("执行删除操作");
            break;
        case 3:
            System.out.println("退出程序");
            break;
        default:
            System.out.println("无效选择");
    }
}
```

### 2. 用户信息录入

```java
Scanner scan = new Scanner(System.in);

System.out.print("请输入姓名：");
String name = scan.nextLine();

System.out.print("请输入年龄：");
int age = scan.nextInt();

System.out.print("请输入身高（米）：");
double height = scan.nextDouble();

System.out.println("姓名：" + name);
System.out.println("年龄：" + age);
System.out.println("身高：" + height + "米");
```

### 3. 循环输入直到满足条件

```java
Scanner scan = new Scanner(System.in);
int password;

do {
    System.out.print("请输入密码（1234）：");
    password = scan.nextInt();
    
    if (password != 1234) {
        System.out.println("密码错误，请重试！");
    }
} while (password != 1234);

System.out.println("密码正确，登录成功！");
```

## 七、最佳实践

### 1. 输入验证模板

```java
Scanner scan = new Scanner(System.in);

System.out.print("请输入整数：");
while (!scan.hasNextInt()) {
    System.out.println("输入无效，请输入整数！");
    scan.next();  // 清除无效输入
    System.out.print("请输入整数：");
}
int num = scan.nextInt();
```

### 2. 异常处理

```java
Scanner scan = new Scanner(System.in);

try {
    System.out.print("请输入数字：");
    int num = scan.nextInt();
    System.out.println("您输入的数字是：" + num);
} catch (InputMismatchException e) {
    System.out.println("输入格式错误！");
}
```

### 3. 完整的输入处理流程

```java
import java.util.Scanner;
import java.util.InputMismatchException;

public class SafeInput {
    public static void main(String[] args) {
        try (Scanner scan = new Scanner(System.in)) {
            System.out.print("请输入整数：");
            
            if (scan.hasNextInt()) {
                int num = scan.nextInt();
                System.out.println("您输入的整数是：" + num);
            } else {
                System.out.println("输入格式错误！");
                scan.next();  // 清除错误输入
            }
        } catch (Exception e) {
            System.out.println("发生错误：" + e.getMessage());
        }
    }
}
```

## 八、总结

### Scanner 类优点

- 简单易用，适合初学者
- 支持多种数据类型输入
- 提供输入验证方法

### Scanner 类缺点

- 性能相对较低
- 不适合大量数据处理
- 线程不安全

### 使用建议

1. 小型程序和学习阶段使用 Scanner
2. 生产环境考虑使用 BufferedReader
3. 始终进行输入验证
4. 记得关闭资源
5. 注意 next() 和 nextLine() 的区别