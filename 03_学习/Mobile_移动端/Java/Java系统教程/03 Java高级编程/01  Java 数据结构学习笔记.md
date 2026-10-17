## 一、Java 数据结构概述

### 1.1 传统数据结构（遗留类）

Java 工具包提供的传统数据结构类：

|数据结构|类/接口|特点|
|---|---|---|
|枚举|Enumeration|遍历元素的接口|
|位集合|BitSet|位或标志的集合|
|向量|Vector|动态数组|
|栈|Stack|后进先出（LIFO）|
|字典|Dictionary|键值对映射（抽象类）|
|哈希表|Hashtable|键值对存储|
|属性|Properties|持久属性集|

### 1.2 现代集合框架

Java 2 引入了新的集合框架（Collection Framework），提供了更强大和灵活的数据结构。

---

## 二、枚举（Enumeration）

### 2.1 概述

**Enumeration 接口**：定义了从数据结构中取回连续元素的方式。

### 2.2 主要方法

|方法|描述|
|---|---|
|`boolean hasMoreElements()`|测试是否还有更多元素|
|`E nextElement()`|返回下一个元素|

### 2.3 使用示例

```java
import java.util.Enumeration;
import java.util.Vector;

public class EnumerationExample {
    public static void main(String[] args) {
        Vector<String> vector = new Vector<>();
        vector.add("Apple");
        vector.add("Banana");
        vector.add("Cherry");
        
        // 获取枚举
        Enumeration<String> enumeration = vector.elements();
        
        // 遍历元素
        while (enumeration.hasMoreElements()) {
            String element = enumeration.nextElement();
            System.out.println(element);
        }
    }
}
```

**输出：**

```
Apple
Banana
Cherry
```

### 2.4 注意事项

- ⚠️ Enumeration 是遗留接口，现代代码推荐使用 Iterator
- ✅ 只能向前遍历，不能删除元素

---

## 三、位集合（BitSet）

### 3.1 概述

**BitSet 类**：实现一组可以单独设置和清除的位或标志，用于处理布尔值集合。

### 3.2 主要方法

|方法|描述|
|---|---|
|`void set(int index)`|设置指定位为 true|
|`void clear(int index)`|清除指定位为 false|
|`boolean get(int index)`|获取指定位的值|
|`void flip(int index)`|翻转指定位|
|`int size()`|返回位集合的大小|
|`int length()`|返回最高位的索引+1|

### 3.3 使用示例

```java
import java.util.BitSet;

public class BitSetExample {
    public static void main(String[] args) {
        BitSet bits1 = new BitSet(16);
        BitSet bits2 = new BitSet(16);
        
        // 设置位
        for (int i = 0; i < 16; i++) {
            if (i % 2 == 0) bits1.set(i);
            if (i % 5 != 0) bits2.set(i);
        }
        
        System.out.println("bits1: " + bits1);
        System.out.println("bits2: " + bits2);
        
        // 位运算
        bits2.and(bits1);  // 与运算
        System.out.println("bits2 AND bits1: " + bits2);
        
        // 获取位
        System.out.println("bit at index 2: " + bits1.get(2));
        
        // 翻转位
        bits1.flip(2);
        System.out.println("After flip(2): " + bits1);
    }
}
```

### 3.4 应用场景

- ✅ 标志位管理
- ✅ 权限控制
- ✅ 大量布尔值存储（节省内存）
- ✅ 位运算操作

---

## 四、向量（Vector）

### 4.1 概述

**Vector 类**：动态数组，大小可以根据需要自动增长。

### 4.2 主要特点

|特点|说明|
|---|---|
|**动态大小**|自动扩容|
|**线程安全**|方法同步|
|**索引访问**|类似数组|
|**遗留类**|推荐使用 ArrayList|

### 4.3 主要方法

|方法|描述|
|---|---|
|`void add(E element)`|添加元素|
|`void add(int index, E element)`|在指定位置插入元素|
|`E get(int index)`|获取指定位置的元素|
|`E remove(int index)`|删除指定位置的元素|
|`int size()`|返回元素个数|
|`boolean isEmpty()`|判断是否为空|
|`void clear()`|清空所有元素|

### 4.4 使用示例

```java
import java.util.Vector;
import java.util.Enumeration;

public class VectorExample {
    public static void main(String[] args) {
        // 创建 Vector
        Vector<String> vector = new Vector<>(3, 2);
        
        // 添加元素
        vector.add("Apple");
        vector.add("Banana");
        vector.add("Cherry");
        vector.add("Date");
        
        System.out.println("Vector: " + vector);
        System.out.println("Size: " + vector.size());
        System.out.println("Capacity: " + vector.capacity());
        
        // 访问元素
        System.out.println("First element: " + vector.get(0));
        System.out.println("Last element: " + vector.lastElement());
        
        // 删除元素
        vector.remove(1);
        System.out.println("After remove: " + vector);
        
        // 遍历
        for (String fruit : vector) {
            System.out.println(fruit);
        }
    }
}
```

### 4.5 Vector vs ArrayList

|特性|Vector|ArrayList|
|---|---|---|
|线程安全|是|否|
|性能|较慢|较快|
|扩容|翻倍|1.5倍|
|推荐使用|❌|✅|

---

## 五、栈（Stack）

### 5.1 概述

**Stack 类**：实现后进先出（LIFO）的数据结构，继承自 Vector。

### 5.2 主要方法

|方法|描述|
|---|---|
|`E push(E item)`|压栈（添加到栈顶）|
|`E pop()`|出栈（移除栈顶元素）|
|`E peek()`|查看栈顶元素（不移除）|
|`boolean empty()`|判断栈是否为空|
|`int search(Object o)`|搜索元素位置|

### 5.3 使用示例

```java
import java.util.Stack;

public class StackExample {
    public static void main(String[] args) {
        Stack<Integer> stack = new Stack<>();
        
        // 压栈
        stack.push(10);
        stack.push(20);
        stack.push(30);
        stack.push(40);
        
        System.out.println("Stack: " + stack);
        
        // 查看栈顶
        System.out.println("Peek: " + stack.peek());
        
        // 出栈
        System.out.println("Pop: " + stack.pop());
        System.out.println("Pop: " + stack.pop());
        
        System.out.println("Stack after pop: " + stack);
        
        // 搜索元素
        System.out.println("Position of 10: " + stack.search(10));
        
        // 判断是否为空
        System.out.println("Is empty: " + stack.empty());
    }
}
```

**输出：**

```
Stack: [10, 20, 30, 40]
Peek: 40
Pop: 40
Pop: 30
Stack after pop: [10, 20]
Position of 10: 2
Is empty: false
```

### 5.4 应用场景

- ✅ 表达式求值
- ✅ 括号匹配
- ✅ 函数调用栈
- ✅ 撤销操作
- ✅ 深度优先搜索（DFS）

### 5.5 实际应用示例

```java
// 括号匹配检查
public class BracketChecker {
    public static boolean isBalanced(String expression) {
        Stack<Character> stack = new Stack<>();
        
        for (char ch : expression.toCharArray()) {
            if (ch == '(' || ch == '[' || ch == '{') {
                stack.push(ch);
            } else if (ch == ')' || ch == ']' || ch == '}') {
                if (stack.empty()) return false;
                
                char top = stack.pop();
                if ((ch == ')' && top != '(') ||
                    (ch == ']' && top != '[') ||
                    (ch == '}' && top != '{')) {
                    return false;
                }
            }
        }
        
        return stack.empty();
    }
    
    public static void main(String[] args) {
        System.out.println(isBalanced("({[]})"));    // true
        System.out.println(isBalanced("({[}])"));    // false
        System.out.println(isBalanced("((()))"));    // true
        System.out.println(isBalanced("(()"));       // false
    }
}
```

---

## 六、字典（Dictionary）

### 6.1 概述

**Dictionary 类**：抽象类，定义键映射到值的数据结构。

### 6.2 主要方法

|方法|描述|
|---|---|
|`V put(K key, V value)`|添加键值对|
|`V get(Object key)`|获取指定键的值|
|`V remove(Object key)`|删除指定键|
|`boolean isEmpty()`|判断是否为空|
|`int size()`|返回键值对数量|
|`Enumeration<K> keys()`|返回所有键的枚举|
|`Enumeration<V> elements()`|返回所有值的枚举|

### 6.3 注意事项

- ⚠️ Dictionary 是抽象类，不能直接实例化
- ⚠️ 已过时，推荐使用 Map 接口
- ✅ Hashtable 是 Dictionary 的具体实现

---

## 七、哈希表（Hashtable）

### 7.1 概述

**Hashtable 类**：基于哈希表的键值对存储结构，继承自 Dictionary。

### 7.2 主要特点

|特点|说明|
|---|---|
|**键值对存储**|通过键快速访问值|
|**线程安全**|方法同步|
|**不允许 null**|键和值都不能为 null|
|**遗留类**|推荐使用 HashMap|

### 7.3 使用示例

```java
import java.util.Hashtable;
import java.util.Enumeration;

public class HashtableExample {
    public static void main(String[] args) {
        Hashtable<String, Integer> hashtable = new Hashtable<>();
        
        // 添加键值对
        hashtable.put("Apple", 10);
        hashtable.put("Banana", 20);
        hashtable.put("Cherry", 30);
        hashtable.put("Date", 40);
        
        System.out.println("Hashtable: " + hashtable);
        
        // 获取值
        System.out.println("Apple: " + hashtable.get("Apple"));
        
        // 检查键是否存在
        System.out.println("Contains 'Banana': " + hashtable.containsKey("Banana"));
        
        // 检查值是否存在
        System.out.println("Contains value 30: " + hashtable.containsValue(30));
        
        // 删除键值对
        hashtable.remove("Banana");
        System.out.println("After remove: " + hashtable);
        
        // 遍历键
        Enumeration<String> keys = hashtable.keys();
        while (keys.hasMoreElements()) {
            String key = keys.nextElement();
            System.out.println(key + " = " + hashtable.get(key));
        }
    }
}
```

### 7.4 Hashtable vs HashMap

|特性|Hashtable|HashMap|
|---|---|---|
|线程安全|是|否|
|null 键值|不允许|允许|
|性能|较慢|较快|
|继承|Dictionary|AbstractMap|
|推荐使用|❌|✅|

---

## 八、属性（Properties）

### 8.1 概述

**Properties 类**：继承自 Hashtable，表示持久的属性集，键和值都是字符串。

### 8.2 主要方法

|方法|描述|
|---|---|
|`String getProperty(String key)`|获取属性值|
|`String getProperty(String key, String defaultValue)`|获取属性值（带默认值）|
|`Object setProperty(String key, String value)`|设置属性|
|`void load(InputStream in)`|从输入流加载属性|
|`void store(OutputStream out, String comments)`|保存属性到输出流|
|`Enumeration<?> propertyNames()`|返回所有属性名|

### 8.3 使用示例

```java
import java.util.Properties;
import java.io.*;

public class PropertiesExample {
    public static void main(String[] args) {
        Properties properties = new Properties();
        
        // 设置属性
        properties.setProperty("database.url", "jdbc:mysql://localhost:3306/mydb");
        properties.setProperty("database.username", "root");
        properties.setProperty("database.password", "password123");
        properties.setProperty("database.driver", "com.mysql.jdbc.Driver");
        
        // 获取属性
        System.out.println("URL: " + properties.getProperty("database.url"));
        System.out.println("Username: " + properties.getProperty("database.username"));
        
        // 获取属性（带默认值）
        String timeout = properties.getProperty("database.timeout", "30");
        System.out.println("Timeout: " + timeout);
        
        // 列出所有属性
        System.out.println("\nAll properties:");
        properties.list(System.out);
        
        // 保存到文件
        try (FileOutputStream fos = new FileOutputStream("config.properties")) {
            properties.store(fos, "Database Configuration");
            System.out.println("\nProperties saved to file");
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        // 从文件加载
        Properties loadedProps = new Properties();
        try (FileInputStream fis = new FileInputStream("config.properties")) {
            loadedProps.load(fis);
            System.out.println("\nLoaded properties:");
            loadedProps.list(System.out);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 8.4 应用场景

- ✅ 配置文件管理
- ✅ 系统属性读取
- ✅ 国际化资源文件
- ✅ 应用程序设置

### 8.5 系统属性示例

```java
import java.util.Properties;

public class SystemPropertiesExample {
    public static void main(String[] args) {
        // 获取系统属性
        Properties sysProps = System.getProperties();
        
        // 显示常用系统属性
        System.out.println("Java Version: " + sysProps.getProperty("java.version"));
        System.out.println("Java Home: " + sysProps.getProperty("java.home"));
        System.out.println("OS Name: " + sysProps.getProperty("os.name"));
        System.out.println("OS Version: " + sysProps.getProperty("os.version"));
        System.out.println("User Name: " + sysProps.getProperty("user.name"));
        System.out.println("User Home: " + sysProps.getProperty("user.home"));
        System.out.println("User Dir: " + sysProps.getProperty("user.dir"));
    }
}
```

---

## 九、数据结构对比总结

### 9.1 遗留类 vs 现代集合

|遗留类|现代替代|推荐|
|---|---|---|
|Vector|ArrayList|ArrayList|
|Stack|Deque (ArrayDeque)|ArrayDeque|
|Hashtable|HashMap|HashMap|
|Enumeration|Iterator|Iterator|
|Dictionary|Map|Map|

### 9.2 选择指南

```java
// ❌ 不推荐：使用遗留类
Vector<String> vector = new Vector<>();
Hashtable<String, Integer> table = new Hashtable<>();

// ✅ 推荐：使用现代集合
ArrayList<String> list = new ArrayList<>();
HashMap<String, Integer> map = new HashMap<>();

// ✅ 需要线程安全时
List<String> syncList = Collections.synchronizedList(new ArrayList<>());
Map<String, Integer> syncMap = Collections.synchronizedMap(new HashMap<>());

// ✅ 或使用并发集合
ConcurrentHashMap<String, Integer> concurrentMap = new ConcurrentHashMap<>();
```

### 9.3 性能对比

|操作|Vector|ArrayList|LinkedList|
|---|---|---|---|
|随机访问|O(1)|O(1)|O(n)|
|插入/删除（末尾）|O(1)|O(1)|O(1)|
|插入/删除（中间）|O(n)|O(n)|O(1)|
|线程安全|是|否|否|

---

## 十、重点总结

### 10.1 核心要点

1. **遗留类**：Vector、Stack、Hashtable、Dictionary 等是 Java 早期的数据结构
2. **线程安全**：遗留类大多是线程安全的，但性能较差
3. **现代替代**：推荐使用 ArrayList、HashMap、ArrayDeque 等现代集合
4. **特殊用途**：Properties 用于配置管理，BitSet 用于位操作

### 10.2 最佳实践

```java
// ✅ 推荐做法
List<String> list = new ArrayList<>();           // 动态数组
Map<String, Integer> map = new HashMap<>();      // 键值对
Deque<Integer> stack = new ArrayDeque<>();       // 栈
Properties props = new Properties();             // 配置

// ❌ 避免使用
Vector<String> vector = new Vector<>();          // 过时
Hashtable<String, Integer> table = new Hashtable<>();  // 过时
Stack<Integer> stack = new Stack<>();            // 过时
```

### 10.3 记忆口诀

```
遗留数据结构七兄弟
枚举位集向量栈
字典哈希加属性
线程安全性能差
现代集合来替代
ArrayList 和 HashMap
```