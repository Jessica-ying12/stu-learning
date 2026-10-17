## 一、封装概述

### 1.1 什么是封装？

- **定义**：将抽象性函数接口的实现细节包装、隐藏起来的方法
- **英文**：Encapsulation
- **本质**：隐藏对象的属性和实现细节，仅对外公开接口

### 1.2 封装的作用

|作用|说明|
|---|---|
|**保护数据**|防止外部代码随机访问内部数据|
|**隐藏细节**|隐藏实现细节，只暴露必要接口|
|**提高维护性**|修改内部实现不影响外部调用|
|**增强安全性**|通过访问控制保护数据安全|
|**降低耦合**|减少类之间的依赖关系|

### 1.3 封装的特点

- 作为保护屏障，防止数据被随意访问
- 通过严格的接口控制访问
- 可以修改内部实现而不影响外部代码
- 让代码更容易理解和维护

## 二、封装的实现

### 2.1 基本实现步骤

1. **修改属性的可见性**：设置为 `private`
2. **创建 getter 方法**：用于获取属性值
3. **创建 setter 方法**：用于设置属性值
4. **在方法中添加控制逻辑**：验证和处理数据

### 2.2 完整示例

```java
/* 文件名: EncapTest.java */
public class EncapTest {
    // 私有属性（外部不能直接访问）
    private String name;
    private String idNum;
    private int age;
    
    // Getter 方法：获取 age
    public int getAge() {
        return age;
    }
    
    // Getter 方法：获取 name
    public String getName() {
        return name;
    }
    
    // Getter 方法：获取 idNum
    public String getIdNum() {
        return idNum;
    }
    
    // Setter 方法：设置 age
    public void setAge(int newAge) {
        age = newAge;
    }
    
    // Setter 方法：设置 name
    public void setName(String newName) {
        name = newName;
    }
    
    // Setter 方法：设置 idNum
    public void setIdNum(String newId) {
        idNum = newId;
    }
}
```

### 2.3 使用封装类

```java
/* 文件名: RunEncap.java */
public class RunEncap {
    public static void main(String args[]) {
        EncapTest encap = new EncapTest();
        
        // 通过 setter 方法设置值
        encap.setName("James");
        encap.setAge(20);
        encap.setIdNum("12343ms");
        
        // 通过 getter 方法获取值
        System.out.print("Name : " + encap.getName() + 
                        " Age : " + encap.getAge());
    }
}
```

**输出结果：**

```
Name : James Age : 20
```

## 三、访问修饰符

### 3.1 Java 访问修饰符

|修饰符|类内部|同包|子类|其他包|
|---|---|---|---|---|
|`private`|✅|❌|❌|❌|
|`default`|✅|✅|❌|❌|
|`protected`|✅|✅|✅|❌|
|`public`|✅|✅|✅|✅|

### 3.2 访问修饰符示例

```java
public class AccessModifierDemo {
    // private: 只能在类内部访问
    private String privateField = "私有字段";
    
    // default: 同包可访问
    String defaultField = "默认字段";
    
    // protected: 同包和子类可访问
    protected String protectedField = "受保护字段";
    
    // public: 所有地方都可访问
    public String publicField = "公共字段";
    
    // 私有方法
    private void privateMethod() {
        System.out.println("私有方法");
    }
    
    // 公共方法
    public void publicMethod() {
        System.out.println("公共方法");
        // 类内部可以访问私有成员
        privateMethod();
        System.out.println(privateField);
    }
}
```

## 四、Getter 和 Setter 方法

### 4.1 命名规范

```java
public class Person {
    private String name;
    private int age;
    private boolean married;
    
    // Getter 方法命名：get + 属性名（首字母大写）
    public String getName() {
        return name;
    }
    
    public int getAge() {
        return age;
    }
    
    // boolean 类型的 Getter 可以用 is 开头
    public boolean isMarried() {
        return married;
    }
    
    // Setter 方法命名：set + 属性名（首字母大写）
    public void setName(String name) {
        this.name = name;
    }
    
    public void setAge(int age) {
        this.age = age;
    }
    
    public void setMarried(boolean married) {
        this.married = married;
    }
}
```

### 4.2 带验证的 Setter 方法

```java
public class Student {
    private String name;
    private int age;
    private double score;
    
    public void setName(String name) {
        // 验证：名字不能为空
        if (name == null || name.trim().isEmpty()) {
            System.out.println("名字不能为空！");
            return;
        }
        this.name = name;
    }
    
    public void setAge(int age) {
        // 验证：年龄必须在合理范围内
        if (age < 0 || age > 150) {
            System.out.println("年龄必须在 0-150 之间！");
            return;
        }
        this.age = age;
    }
    
    public void setScore(double score) {
        // 验证：分数必须在 0-100 之间
        if (score < 0 || score > 100) {
            System.out.println("分数必须在 0-100 之间！");
            return;
        }
        this.score = score;
    }
    
    // Getter 方法
    public String getName() {
        return name;
    }
    
    public int getAge() {
        return age;
    }
    
    public double getScore() {
        return score;
    }
}
```

### 4.3 使用示例

```java
public class StudentDemo {
    public static void main(String[] args) {
        Student student = new Student();
        
        // 正常设置
        student.setName("张三");
        student.setAge(20);
        student.setScore(85.5);
        
        System.out.println("姓名: " + student.getName());
        System.out.println("年龄: " + student.getAge());
        System.out.println("分数: " + student.getScore());
        
        // 非法设置（会被验证拦截）
        student.setAge(-5);      // 年龄必须在 0-150 之间！
        student.setScore(150);   // 分数必须在 0-100 之间！
    }
}
```

## 五、封装的优点

### 5.1 数据隐藏

```java
// ❌ 不好的设计：直接暴露字段
public class BadDesign {
    public String username;
    public String password;
}

// 使用时可以随意修改
BadDesign user = new BadDesign();
user.password = "";  // 可以设置空密码，不安全！

// ✅ 好的设计：封装字段
public class GoodDesign {
    private String username;
    private String password;
    
    public void setPassword(String password) {
        // 添加验证逻辑
        if (password == null || password.length() < 6) {
            throw new IllegalArgumentException("密码长度至少6位！");
        }
        this.password = password;
    }
    
    public String getUsername() {
        return username;
    }
}
```

### 5.2 灵活性和可维护性

```java
public class BankAccount {
    private double balance;
    
    // 版本 1：简单实现
    public double getBalance() {
        return balance;
    }
    
    // 版本 2：添加日志记录（不影响外部调用）
    public double getBalance() {
        System.out.println("查询余额: " + balance);
        return balance;
    }
    
    // 版本 3：添加权限检查（不影响外部调用）
    public double getBalance() {
        if (!hasPermission()) {
            throw new SecurityException("无权限查询余额");
        }
        return balance;
    }
    
    private boolean hasPermission() {
        // 权限检查逻辑
        return true;
    }
}
```

### 5.3 只读或只写属性

```java
public class ReadOnlyExample {
    private final String id;
    private String name;
    private int readCount;
    
    public ReadOnlyExample(String id) {
        this.id = id;
    }
    
    // 只读属性：只有 getter，没有 setter
    public String getId() {
        return id;
    }
    
    // 读写属性：有 getter 和 setter
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    // 只写属性：只有 setter，没有 getter（少见）
    public void incrementReadCount() {
        readCount++;
    }
}
```

## 六、封装的实战案例

### 6.1 银行账户类

```java
public class BankAccount {
    private String accountNumber;
    private String accountHolder;
    private double balance;
    private String password;
    
    // 构造方法
    public BankAccount(String accountNumber, String accountHolder, String password) {
        this.accountNumber = accountNumber;
        this.accountHolder = accountHolder;
        this.password = password;
        this.balance = 0.0;
    }
    
    // 存款
    public boolean deposit(double amount) {
        if (amount <= 0) {
            System.out.println("存款金额必须大于0！");
            return false;
        }
        balance += amount;
        System.out.println("存款成功！当前余额: " + balance);
        return true;
    }
    
    // 取款（需要密码验证）
    public boolean withdraw(double amount, String password) {
        if (!this.password.equals(password)) {
            System.out.println("密码错误！");
            return false;
        }
        
        if (amount <= 0) {
            System.out.println("取款金额必须大于0！");
            return false;
        }
        
        if (amount > balance) {
            System.out.println("余额不足！");
            return false;
        }
        
        balance -= amount;
        System.out.println("取款成功！当前余额: " + balance);
        return true;
    }
    
    // 查询余额（需要密码验证）
    public double getBalance(String password) {
        if (!this.password.equals(password)) {
            System.out.println("密码错误！");
            return -1;
        }
        return balance;
    }
    
    // 修改密码
    public boolean changePassword(String oldPassword, String newPassword) {
        if (!this.password.equals(oldPassword)) {
            System.out.println("原密码错误！");
            return false;
        }
        
        if (newPassword == null || newPassword.length() < 6) {
            System.out.println("新密码长度至少6位！");
            return false;
        }
        
        this.password = newPassword;
        System.out.println("密码修改成功！");
        return true;
    }
    
    // 只读属性
    public String getAccountNumber() {
        return accountNumber;
    }
    
    public String getAccountHolder() {
        return accountHolder;
    }
}
```

### 6.2 使用银行账户类

```java
public class BankDemo {
    public static void main(String[] args) {
        // 创建账户
        BankAccount account = new BankAccount("6222021234567890", "张三", "123456");
        
        // 存款
        account.deposit(1000);
        
        // 查询余额
        double balance = account.getBalance("123456");
        System.out.println("当前余额: " + balance);
        
        // 取款
        account.withdraw(500, "123456");
        
        // 密码错误的取款
        account.withdraw(200, "wrong");
        
        // 修改密码
        account.changePassword("123456", "654321");
        
        // 使用新密码查询
        balance = account.getBalance("654321");
        System.out.println("当前余额: " + balance);
    }
}
```

### 6.3 员工类

```java
public class Employee {
    private String id;
    private String name;
    private int age;
    private String department;
    private double salary;
    
    // 构造方法
    public Employee(String id, String name, int age, String department, double salary) {
        this.id = id;
        setName(name);
        setAge(age);
        setDepartment(department);
        setSalary(salary);
    }
    
    // Getter 和 Setter 方法
    public String getId() {
        return id;
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        if (name == null || name.trim().isEmpty()) {
            throw new IllegalArgumentException("姓名不能为空！");
        }
        this.name = name;
    }
    
    public int getAge() {
        return age;
    }
    
    public void setAge(int age) {
        if (age < 18 || age > 65) {
            throw new IllegalArgumentException("年龄必须在18-65之间！");
        }
        this.age = age;
    }
    
    public String getDepartment() {
        return department;
    }
    
    public void setDepartment(String department) {
        if (department == null || department.trim().isEmpty()) {
            throw new IllegalArgumentException("部门不能为空！");
        }
        this.department = department;
    }
    
    public double getSalary() {
        return salary;
    }
    
    public void setSalary(double salary) {
        if (salary < 0) {
            throw new IllegalArgumentException("薪资不能为负数！");
        }
        this.salary = salary;
    }
    
    // 业务方法
    public void raiseSalary(double percentage) {
        if (percentage < 0 || percentage > 100) {
            throw new IllegalArgumentException("涨薪比例必须在0-100之间！");
        }
        salary = salary * (1 + percentage / 100);
    }
    
    public void displayInfo() {
        System.out.println("员工信息：");
        System.out.println("ID: " + id);
        System.out.println("姓名: " + name);
        System.out.println("年龄: " + age);
        System.out.println("部门: " + department);
        System.out.println("薪资: " + salary);
    }
}
```

## 七、封装的最佳实践

### 7.1 设计原则

```java
public class BestPractice {
    // ✅ 1. 属性私有化
    private String name;
    private int age;
    
    // ✅ 2. 提供公共的 getter 和 setter
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    // ✅ 3. 在 setter 中添加验证逻辑
    public void setAge(int age) {
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException("年龄不合法！");
        }
        this.age = age;
    }
    
    // ✅ 4. 不要为所有属性都提供 setter（只读属性）
    private final String id = generateId();
    
    public String getId() {
        return id;
    }
    
    private String generateId() {
        return "ID_" + System.currentTimeMillis();
    }
    
    // ✅ 5. 返回可变对象的副本，而不是原对象
    private List<String> items = new ArrayList<>();
    
    public List<String> getItems() {
        return new ArrayList<>(items);  // 返回副本
    }
}
```

### 7.2 常见错误

```java
public class CommonMistakes {
    private String[] data;
    
    // ❌ 错误：直接返回可变对象的引用
    public String[] getData() {
        return data;  // 外部可以修改数组内容
    }
    
    // ✅ 正确：返回副本
    public String[] getData() {
        return data.clone();
    }
    
    // ❌ 错误：setter 没有验证
    private int age;
    
    public void setAge(int age) {
        this.age = age;  // 可能设置负数
    }
    
    // ✅ 正确：添加验证
    public void setAge(int age) {
        if (age < 0) {
            throw new IllegalArgumentException("年龄不能为负数");
        }
        this.age = age;
    }
}
```

## 八、总结

### 8.1 封装的核心要点

|要点|说明|
|---|---|
|**私有化属性**|使用 `private` 修饰成员变量|
|**公共接口**|提供 `public` 的 getter/setter 方法|
|**数据验证**|在 setter 中添加验证逻辑|
|**隐藏实现**|只暴露必要的接口|
|**提高安全性**|通过访问控制保护数据|

### 8.2 封装的好处

1. **数据安全**：防止非法数据
2. **灵活性**：可以修改内部实现
3. **可维护性**：代码更易理解和维护
4. **降低耦合**：减少类之间的依赖
5. **代码复用**：提高代码的可重用性

### 8.3 记忆口诀

- **属性私有**：private 修饰成员变量
- **方法公开**：public 提供访问接口
- **验证数据**：setter 中加入检查
- **隐藏细节**：只暴露必要功能
- **提高安全**：保护数据不被破坏

### 8.4 封装与面向对象

封装是面向对象编程的三大特性之一：

1. **封装**（Encapsulation）：隐藏实现细节
2. **继承**（Inheritance）：代码复用
3. **多态**（Polymorphism）：灵活性和扩展性