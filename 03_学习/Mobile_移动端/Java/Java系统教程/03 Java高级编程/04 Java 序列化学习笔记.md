## 一、序列化概述

### 1.1 什么是序列化？
- **定义**：将对象转换为字节序列的过程
- **英文**：Serialization
- **目的**：将对象保存到文件或通过网络传输

### 1.2 序列化包含的内容

| 内容 | 说明 |
|------|------|
| **对象数据** | 对象的属性值 |
| **类型信息** | 对象所属的类 |
| **数据类型** | 对象中数据的类型 |

### 1.3 反序列化
- **定义**：从字节序列恢复对象的过程
- **英文**：Deserialization
- **作用**：从文件或网络读取数据并重建对象

### 1.4 序列化的特点
- **平台独立**：在一个平台序列化的对象可以在另一个平台反序列化
- **JVM 独立**：整个过程由 JVM 处理
- **持久化**：对象可以保存到磁盘

## 二、序列化的实现

### 2.1 核心类

| 类名 | 作用 |
|------|------|
| `ObjectOutputStream` | 序列化对象到输出流 |
| `ObjectInputStream` | 从输入流反序列化对象 |

### 2.2 关键方法

#### ObjectOutputStream
```java
public final void writeObject(Object x) throws IOException
```
- 将对象序列化并发送到输出流

#### ObjectInputStream
```java
public final Object readObject() throws IOException, ClassNotFoundException
```
- 从流中读取并反序列化对象
- 返回值为 Object，需要强制类型转换

## 三、序列化的条件

### 3.1 必须满足的条件

| 条件 | 说明 |
|------|------|
| **实现接口** | 类必须实现 `java.io.Serializable` 接口 |
| **属性可序列化** | 所有属性必须是可序列化的 |
| **transient 关键字** | 不可序列化的属性必须标记为 transient |

### 3.2 Serializable 接口
```java
public interface Serializable {
    // 标记接口，没有任何方法
}
```

### 3.3 Employee 类示例

```java
public class Employee implements java.io.Serializable {
    public String name;
    public String address;
    public transient int SSN;  // transient: 不会被序列化
    public int number;
    
    public void mailCheck() {
        System.out.println("Mailing a check to " + name + " " + address);
    }
}
```

**说明：**
- `name`、`address`、`number` 会被序列化
- `SSN` 标记为 `transient`，不会被序列化

## 四、序列化对象

### 4.1 序列化步骤

1. 创建 `FileOutputStream`
2. 创建 `ObjectOutputStream`
3. 调用 `writeObject()` 方法
4. 关闭流

### 4.2 完整示例

```java
import java.io.*;

public class SerializeDemo {
    public static void main(String[] args) {
        Employee e = new Employee();
        e.name = "Reyan Ali";
        e.address = "Phokka Kuan, Ambehta Peer";
        e.SSN = 11122333;
        e.number = 101;
        
        try {
            // 创建文件输出流
            FileOutputStream fileOut = new FileOutputStream("/tmp/employee.ser");
            
            // 创建对象输出流
            ObjectOutputStream out = new ObjectOutputStream(fileOut);
            
            // 序列化对象
            out.writeObject(e);
            
            // 关闭流
            out.close();
            fileOut.close();
            
            System.out.printf("Serialized data is saved in /tmp/employee.ser");
        } catch (IOException i) {
            i.printStackTrace();
        }
    }
}
```

### 4.3 文件命名约定
- **约定**：序列化文件使用 `.ser` 扩展名
- **示例**：`employee.ser`

## 五、反序列化对象

### 5.1 反序列化步骤

1. 创建 `FileInputStream`
2. 创建 `ObjectInputStream`
3. 调用 `readObject()` 方法
4. 强制类型转换
5. 关闭流

### 5.2 完整示例

```java
import java.io.*;

public class DeserializeDemo {
    public static void main(String[] args) {
        Employee e = null;
        
        try {
            // 创建文件输入流
            FileInputStream fileIn = new FileInputStream("/tmp/employee.ser");
            
            // 创建对象输入流
            ObjectInputStream in = new ObjectInputStream(fileIn);
            
            // 反序列化对象（需要强制类型转换）
            e = (Employee) in.readObject();
            
            // 关闭流
            in.close();
            fileIn.close();
        } catch (IOException i) {
            i.printStackTrace();
            return;
        } catch (ClassNotFoundException c) {
            System.out.println("Employee class not found");
            c.printStackTrace();
            return;
        }
        
        // 输出反序列化后的对象
        System.out.println("Deserialized Employee...");
        System.out.println("Name: " + e.name);
        System.out.println("Address: " + e.address);
        System.out.println("SSN: " + e.SSN);
        System.out.println("Number: " + e.number);
    }
}
```

### 5.3 运行结果

```
Deserialized Employee...
Name: Reyan Ali
Address: Phokka Kuan, Ambehta Peer
SSN: 0
Number: 101
```

**注意**：SSN 的值为 0，因为它被标记为 `transient`，没有被序列化。

## 六、transient 关键字

### 6.1 作用
- **定义**：标记不需要序列化的属性
- **效果**：该属性不会被保存到文件中

### 6.2 使用场景

| 场景 | 说明 |
|------|------|
| **敏感信息** | 密码、身份证号等 |
| **临时数据** | 缓存、计算结果等 |
| **不可序列化** | 无法序列化的对象 |

### 6.3 示例

```java
public class User implements Serializable {
    private String username;
    private transient String password;  // 密码不会被序列化
    private int age;
    private transient Thread thread;    // Thread 不可序列化
    
    // 构造方法、getter、setter...
}
```

### 6.4 反序列化后的值

| 类型 | 默认值 |
|------|--------|
| `int`、`byte`、`short`、`long` | 0 |
| `float`、`double` | 0.0 |
| `boolean` | false |
| `char` | '\u0000' |
| 引用类型 | null |

## 七、异常处理

### 7.1 常见异常

| 异常 | 说明 |
|------|------|
| `IOException` | 输入输出异常 |
| `ClassNotFoundException` | 找不到类定义 |
| `NotSerializableException` | 对象不可序列化 |

### 7.2 ClassNotFoundException

```java
try {
    ObjectInputStream in = new ObjectInputStream(fileIn);
    Employee e = (Employee) in.readObject();
} catch (ClassNotFoundException c) {
    System.out.println("Employee class not found");
    c.printStackTrace();
}
```

**原因：**
- JVM 找不到类的字节码文件
- 类的包名或类名发生变化
- 类文件不在 CLASSPATH 中

## 八、序列化的完整案例

### 8.1 定义可序列化的类

```java
import java.io.Serializable;

public class Student implements Serializable {
    // serialVersionUID 用于版本控制
    private static final long serialVersionUID = 1L;
    
    private String name;
    private int age;
    private transient String password;  // 不序列化
    private String email;
    
    public Student(String name, int age, String password, String email) {
        this.name = name;
        this.age = age;
        this.password = password;
        this.email = email;
    }
    
    // Getter 和 Setter 方法
    public String getName() {
        return name;
    }
    
    public int getAge() {
        return age;
    }
    
    public String getPassword() {
        return password;
    }
    
    public String getEmail() {
        return email;
    }
    
    @Override
    public String toString() {
        return "Student{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", password='" + password + '\'' +
                ", email='" + email + '\'' +
                '}';
    }
}
```

### 8.2 序列化工具类

```java
import java.io.*;

public class SerializationUtil {
    
    // 序列化对象到文件
    public static void serialize(Object obj, String filename) {
        try (FileOutputStream fileOut = new FileOutputStream(filename);
             ObjectOutputStream out = new ObjectOutputStream(fileOut)) {
            
            out.writeObject(obj);
            System.out.println("对象已序列化到: " + filename);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    // 从文件反序列化对象
    public static Object deserialize(String filename) {
        Object obj = null;
        
        try (FileInputStream fileIn = new FileInputStream(filename);
             ObjectInputStream in = new ObjectInputStream(fileIn)) {
            
            obj = in.readObject();
            System.out.println("对象已从文件反序列化: " + filename);
            
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
        
        return obj;
    }
}
```

### 8.3 使用示例

```java
public class SerializationDemo {
    public static void main(String[] args) {
        // 创建学生对象
        Student student = new Student("张三", 20, "secret123", "zhangsan@example.com");
        
        System.out.println("原始对象: " + student);
        
        // 序列化
        String filename = "student.ser";
        SerializationUtil.serialize(student, filename);
        
        // 反序列化
        Student deserializedStudent = (Student) SerializationUtil.deserialize(filename);
        
        System.out.println("反序列化后: " + deserializedStudent);
        System.out.println("密码（transient）: " + deserializedStudent.getPassword());
    }
}
```

**输出结果：**
```
原始对象: Student{name='张三', age=20, password='secret123', email='zhangsan@example.com'}
对象已序列化到: student.ser
对象已从文件反序列化: student.ser
反序列化后: Student{name='张三', age=20, password='null', email='zhangsan@example.com'}
密码（transient）: null
```

## 九、serialVersionUID

### 9.1 什么是 serialVersionUID？
- **定义**：序列化版本号
- **作用**：验证序列化对象的版本一致性

### 9.2 为什么需要？

```java
public class Person implements Serializable {
    // 显式声明 serialVersionUID
    private static final long serialVersionUID = 1L;
    
    private String name;
    private int age;
}
```

### 9.3 版本控制

| 情况 | 说明 |
|------|------|
| **未声明** | JVM 自动生成，类修改后会变化 |
| **显式声明** | 手动控制版本号 |
| **版本不匹配** | 抛出 `InvalidClassException` |

### 9.4 最佳实践

```java
public class Employee implements Serializable {
    // ✅ 推荐：显式声明 serialVersionUID
    private static final long serialVersionUID = 1L;
    
    private String name;
    private int age;
    
    // 添加新字段时，可以保持 serialVersionUID 不变
    // private String email;  // 新增字段
}
```

## 十、序列化的注意事项

### 10.1 继承关系

```java
// 父类
class Animal {
    String species;
}

// 子类实现 Serializable
class Dog extends Animal implements Serializable {
    String name;
}
```

**规则：**
- 子类实现 `Serializable`，父类不实现
- 父类的属性不会被序列化
- 父类必须有无参构造方法

### 10.2 静态变量

```java
public class Test implements Serializable {
    private static int staticVar = 10;
    private int instanceVar = 20;
}
```

**规则：**
- 静态变量不会被序列化
- 静态变量属于类，不属于对象

### 10.3 序列化集合

```java
import java.io.*;
import java.util.ArrayList;
import java.util.List;

public class CollectionSerializationDemo {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("Apple");
        list.add("Banana");
        list.add("Cherry");
        
        // 序列化集合
        try (ObjectOutputStream out = new ObjectOutputStream(
                new FileOutputStream("list.ser"))) {
            out.writeObject(list);
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // 反序列化集合
        try (ObjectInputStream in = new ObjectInputStream(
                new FileInputStream("list.ser"))) {
            List<String> deserializedList = (List<String>) in.readObject();
            System.out.println(deserializedList);
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

## 十一、序列化的应用场景

### 11.1 常见应用

| 场景 | 说明 |
|------|------|
| **持久化** | 将对象保存到文件 |
| **网络传输** | 通过网络发送对象 |
| **缓存** | 将对象缓存到磁盘 |
| **深拷贝** | 通过序列化实现对象深拷贝 |
| **分布式系统** | RMI、RPC 等 |

### 11.2 深拷贝示例

```java
import java.io.*;

public class DeepCopyUtil {
    
    // 通过序列化实现深拷贝
    public static <T> T deepCopy(T obj) {
        try {
            // 序列化到字节数组
            ByteArrayOutputStream bos = new ByteArrayOutputStream();
            ObjectOutputStream out = new ObjectOutputStream(bos);
            out.writeObject(obj);
            
            // 从字节数组反序列化
            ByteArrayInputStream bis = new ByteArrayInputStream(bos.toByteArray());
            ObjectInputStream in = new ObjectInputStream(bis);
            
            return (T) in.readObject();
            
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
            return null;
        }
    }
    
    public static void main(String[] args) {
        Student original = new Student("张三", 20, "pass123", "zhang@example.com");
        Student copy = deepCopy(original);
        
        System.out.println("原始对象: " + original);
        System.out.println("拷贝对象: " + copy);
        System.out.println("是否为同一对象: " + (original == copy));
    }
}
```

## 十二、总结

### 12.1 核心要点

| 要点 | 说明 |
|------|------|
| **实现接口** | 必须实现 `Serializable` 接口 |
| **transient** | 标记不需要序列化的属性 |
| **serialVersionUID** | 显式声明版本号 |
| **异常处理** | 处理 `IOException` 和 `ClassNotFoundException` |
| **平台独立** | 可以跨平台传输对象 |

### 12.2 序列化流程

```
对象 → ObjectOutputStream → 字节流 → 文件/网络
文件/网络 → 字节流 → ObjectInputStream → 对象
```

### 12.3 最佳实践

1. **显式声明 serialVersionUID**
2. **敏感信息使用 transient**
3. **使用 try-with-resources 自动关闭流**
4. **处理所有可能的异常**
5. **考虑版本兼容性**

### 12.4 记忆口诀

- **Serializable 接口**：标记类可序列化
- **ObjectOutputStream**：写对象到流
- **ObjectInputStream**：从流读对象
- **transient 关键字**：属性不序列化
- **serialVersionUID**：版本要控制