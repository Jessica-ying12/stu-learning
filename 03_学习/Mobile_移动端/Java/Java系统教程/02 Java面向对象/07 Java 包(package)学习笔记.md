## 一、包的概述

### 1.1 什么是包？

- **定义**：用于组织类的命名空间机制
- **英文**：Package
- **作用**：类似文件夹，采用树形目录结构

### 1.2 包的作用

|作用|说明|
|---|---|
|**组织类**|把功能相似或相关的类组织在一起|
|**避免冲突**|不同包中可以有同名的类|
|**访问控制**|限定访问权限|
|**便于查找**|方便类的查找和使用|
|**命名空间**|提供独立的命名空间|

### 1.3 包的意义

- 防止命名冲突
- 访问控制
- 提供搜索和定位类、接口、枚举和注释等

## 二、包的语法

### 2.1 包声明语法

```java
package pkg1[.pkg2[.pkg3…]];
```

### 2.2 包声明示例

```java
// 文件名: Something.java
package net.java.util;

public class Something {
    // 类实现
}
```

**对应的文件路径：**

```
net/java/util/Something.java
```

### 2.3 包声明规则

|规则|说明|
|---|---|
|**位置**|必须在源文件的第一行（注释除外）|
|**数量**|每个源文件只能有一个包声明|
|**作用域**|文件中的所有类型都属于该包|
|**无声明**|没有包声明的类属于无名包（unnamed package）|

## 三、Java 常用包

### 3.1 JDK 内置包

|包名|说明|
|---|---|
|`java.lang`|基础类（自动导入，无需 import）|
|`java.io`|输入输出功能|
|`java.util`|工具类（集合、日期等）|
|`java.net`|网络编程|
|`java.awt`|图形界面|
|`java.sql`|数据库操作|

### 3.2 包的命名规范

```java
// ✅ 推荐：使用小写字母
package com.company.project;

// ❌ 不推荐：使用大写字母
package Com.Company.Project;

// ✅ 推荐：使用公司域名倒序
package com.apple.computers;

// ✅ 推荐：多层级结构
package com.company.project.module.submodule;
```

## 四、创建包

### 4.1 创建包的步骤

1. 在源文件开头声明包名
2. 将源文件放在对应的目录结构中
3. 编译时使用 `-d` 选项

### 4.2 完整示例

#### 创建接口

```java
/* 文件名: Animal.java */
package animals;

interface Animal {
    public void eat();
    public void travel();
}
```

#### 实现接口

```java
/* 文件名: MammalInt.java */
package animals;

public class MammalInt implements Animal {
    
    public void eat() {
        System.out.println("Mammal eats");
    }
    
    public void travel() {
        System.out.println("Mammal travels");
    }
    
    public int noOfLegs() {
        return 0;
    }
    
    public static void main(String args[]) {
        MammalInt m = new MammalInt();
        m.eat();
        m.travel();
    }
}
```

#### 编译和运行

```bash
# 创建目录
$ mkdir animals

# 编译文件
$ javac -d . Animal.java
$ javac -d . MammalInt.java

# 运行程序
$ java animals.MammalInt
```

**输出结果：**

```
Mammal eats
Mammal travels
```

## 五、import 关键字

### 5.1 import 语法

```java
import package1[.package2…].(classname|*);
```

### 5.2 import 规则

|规则|说明|
|---|---|
|**位置**|在 package 语句之后，类定义之前|
|**数量**|可以有多条 import 语句|
|**可选**|可以没有 import 语句|

### 5.3 import 的三种方式

```java
// 方式1：使用类的全名（不需要 import）
payroll.Employee emp = new payroll.Employee();

// 方式2：导入特定的类
import payroll.Employee;
Employee emp = new Employee();

// 方式3：使用通配符导入整个包
import payroll.*;
Employee emp = new Employee();
Boss boss = new Boss();
```

### 5.4 同包类的使用

```java
package payroll;

public class Boss {
    // 同一个包中的类可以直接使用，不需要 import
    public void payEmployee(Employee e) {
        e.mailCheck();
    }
}
```

### 5.5 import 示例

```java
// 文件开头的标准结构
package com.company.project;  // 包声明

import java.util.ArrayList;   // 导入特定类
import java.util.List;
import java.io.*;              // 导入整个包

public class MyClass {
    // 类实现
}
```

## 六、包的目录结构

### 6.1 目录结构规则

**包名必须与目录结构对应：**

- 包名成为类名的一部分
- 包名必须与字节码所在的目录结构相吻合

### 6.2 目录结构示例

```java
// 文件名: Car.java
package vehicle;

public class Car {
    // 类实现
}
```

**对应的目录结构：**

```
项目根目录/
└── vehicle/
    └── Car.java
```

**完整的类名和路径：**

- 类名：`vehicle.Car`
- 路径：`vehicle\Car.java` (Windows)
- 路径：`vehicle/Car.java` (Unix/Linux)

### 6.3 多层级包结构

```java
// 文件名: Dell.java
package com.apple.computers;

public class Dell {
    // 类实现
}

class Ups {
    // 类实现
}
```

**对应的目录结构：**

```
项目根目录/
└── com/
    └── apple/
        └── computers/
            └── Dell.java
```

### 6.4 使用 -d 选项编译

```bash
# 编译时自动创建目录结构
$ javac -d . Dell.java

# 生成的目录结构：
# .\com\apple\computers\Dell.class
# .\com\apple\computers\Ups.class
```

### 6.5 导入包中的类

```java
// 导入 com.apple.computers 包中的所有类
import com.apple.computers.*;

// 或导入特定类
import com.apple.computers.Dell;
```

## 七、源码和类文件的分离

### 7.1 分离的目录结构

```
项目根目录/
├── src/                          # 源码目录
│   └── com/
│       └── apple/
│           └── computers/
│               └── Dell.java
│
└── bin/                          # 编译后的类文件目录
    └── com/
        └── apple/
            └── computers/
                └── Dell.class
```

### 7.2 编译到指定目录

```bash
# 将编译后的类文件输出到 bin 目录
$ javac -d bin src/com/apple/computers/Dell.java

# 运行时指定 classpath
$ java -cp bin com.apple.computers.Dell
```

### 7.3 分离的好处

|好处|说明|
|---|---|
|**保护源码**|可以只分享 .class 文件|
|**组织清晰**|源码和编译文件分开管理|
|**便于部署**|只需要部署 .class 文件|

## 八、CLASSPATH 设置

### 8.1 什么是 CLASSPATH？

- **定义**：类路径，JVM 查找类文件的路径
- **作用**：告诉编译器和 JVM 在哪里查找 .class 文件

### 8.2 查看 CLASSPATH

```bash
# Windows
C:\> set CLASSPATH

# Unix/Linux
$ echo $CLASSPATH
```

### 8.3 设置 CLASSPATH

```bash
# Windows
C:\> set CLASSPATH=C:\users\jack\java\classes

# Unix/Linux (Bourne shell)
$ CLASSPATH=/home/jack/java/classes
$ export CLASSPATH
```

### 8.4 删除 CLASSPATH

```bash
# Windows
C:\> set CLASSPATH=

# Unix/Linux
$ unset CLASSPATH
$ export CLASSPATH
```

### 8.5 CLASSPATH 的多个路径

```bash
# Windows（使用分号分隔）
C:\> set CLASSPATH=C:\java\classes;D:\project\lib

# Unix/Linux（使用冒号分隔）
$ CLASSPATH=/home/java/classes:/home/project/lib
$ export CLASSPATH
```

### 8.6 临时设置 CLASSPATH

```bash
# 编译时指定
$ javac -cp /path/to/classes MyClass.java

# 运行时指定
$ java -cp /path/to/classes MyClass
```

## 九、JAR 文件

### 9.1 什么是 JAR？

- **定义**：Java Archive，Java 归档文件
- **作用**：将多个类文件打包成一个文件
- **格式**：ZIP 压缩格式

### 9.2 创建 JAR 文件

```bash
# 创建 JAR 文件
$ jar cvf myapp.jar com/company/project/*.class

# 查看 JAR 文件内容
$ jar tvf myapp.jar

# 解压 JAR 文件
$ jar xvf myapp.jar
```

### 9.3 使用 JAR 文件

```bash
# 将 JAR 文件添加到 CLASSPATH
$ java -cp myapp.jar com.company.project.MainClass

# 运行可执行 JAR
$ java -jar myapp.jar
```

## 十、包的实战案例

### 10.1 创建项目包结构

```
myproject/
├── src/
│   └── com/
│       └── mycompany/
│           └── myapp/
│               ├── model/
│               │   ├── User.java
│               │   └── Product.java
│               ├── service/
│               │   ├── UserService.java
│               │   └── ProductService.java
│               ├── util/
│               │   └── StringUtil.java
│               └── Main.java
└── bin/
```

### 10.2 模型类

```java
// 文件名: User.java
package com.mycompany.myapp.model;

public class User {
    private String username;
    private String email;
    
    public User(String username, String email) {
        this.username = username;
        this.email = email;
    }
    
    // Getter 和 Setter 方法
    public String getUsername() {
        return username;
    }
    
    public String getEmail() {
        return email;
    }
}
```

### 10.3 服务类

```java
// 文件名: UserService.java
package com.mycompany.myapp.service;

import com.mycompany.myapp.model.User;

public class UserService {
    public void createUser(String username, String email) {
        User user = new User(username, email);
        System.out.println("创建用户: " + user.getUsername());
    }
}
```

### 10.4 工具类

```java
// 文件名: StringUtil.java
package com.mycompany.myapp.util;

public class StringUtil {
    public static boolean isEmpty(String str) {
        return str == null || str.trim().isEmpty();
    }
}
```

### 10.5 主类

```java
// 文件名: Main.java
package com.mycompany.myapp;

import com.mycompany.myapp.service.UserService;
import com.mycompany.myapp.util.StringUtil;

public class Main {
    public static void main(String[] args) {
        UserService userService = new UserService();
        
        String username = "张三";
        if (!StringUtil.isEmpty(username)) {
            userService.createUser(username, "zhangsan@example.com");
        }
    }
}
```

## 十一、包的最佳实践

### 11.1 命名规范

```java
// ✅ 推荐：使用公司域名倒序
package com.company.project.module;

// ✅ 推荐：全部小写
package com.example.utils;

// ❌ 避免：使用 Java 关键字
// package com.example.class;  // 错误

// ❌ 避免：使用大写字母
// package Com.Example.Utils;  // 不推荐
```

### 11.2 包的组织原则

|原则|说明|
|---|---|
|**按功能分包**|model, service, dao, util 等|
|**按模块分包**|user, product, order 等|
|**层次清晰**|不要嵌套太深|
|**职责单一**|每个包有明确的职责|

### 11.3 import 的最佳实践

```java
// ✅ 推荐：导入具体的类
import java.util.ArrayList;
import java.util.HashMap;

// ⚠️ 谨慎使用：通配符导入
import java.util.*;  // 可能导致命名冲突

// ✅ 推荐：按字母顺序排列
import java.io.File;
import java.util.List;
import javax.swing.JFrame;

// ❌ 避免：导入未使用的类
import java.util.Date;  // 如果没有使用，应该删除
```

## 十二、总结

### 12.1 核心要点

|要点|说明|
|---|---|
|**包声明**|必须在文件第一行|
|**目录对应**|包名必须与目录结构一致|
|**import 导入**|使用其他包的类需要导入|
|**CLASSPATH**|设置类文件的查找路径|
|**命名规范**|使用小写字母，域名倒序|

### 12.2 包的作用总结

1. **组织代码**：将相关的类组织在一起
2. **避免冲突**：提供独立的命名空间
3. **访问控制**：限定类的访问权限
4. **便于管理**：清晰的目录结构

### 12.3 记忆口诀

- **包声明第一行**：package 语句在最前
- **目录结构对应**：包名就是文件夹
- **import 来导入**：使用别的包要导入
- **小写加点号**：命名规范要记牢
- **CLASSPATH 路径**：告诉 JVM 哪里找