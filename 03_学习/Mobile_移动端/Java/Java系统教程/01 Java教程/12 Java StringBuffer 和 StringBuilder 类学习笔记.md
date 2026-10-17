## 一、StringBuffer 和 StringBuilder 概述

### 1.1 为什么需要 StringBuffer 和 StringBuilder

- **String 的不可变性问题**：对字符串进行修改时，String 会产生新对象
- **StringBuffer 和 StringBuilder**：对象能够被多次修改，不产生新的未使用对象
- **性能优势**：避免频繁创建对象，提高性能

### 1.2 String vs StringBuffer vs StringBuilder

|特性|String|StringBuffer|StringBuilder|
|---|---|---|---|
|可变性|不可变|可变|可变|
|线程安全|安全|安全（同步）|不安全|
|性能|低（频繁修改时）|中等|高|
|使用场景|少量字符串操作|多线程环境|单线程环境|
|引入版本|JDK 1.0|JDK 1.0|JDK 1.5|

### 1.3 线程安全说明

**线程安全**：

- 采用加锁机制
- 一个线程访问数据时，其他线程等待
- 不会出现数据不一致或数据污染
- StringBuffer 是线程安全的

**线程不安全**：

- 不提供数据访问保护
- 可能出现多个线程先后更改数据
- 可能造成脏数据
- StringBuilder 是线程不安全的

---

## 二、StringBuilder 基本使用

### 2.1 创建 StringBuilder

```java
// 创建空的 StringBuilder
StringBuilder sb1 = new StringBuilder();

// 创建指定容量的 StringBuilder
StringBuilder sb2 = new StringBuilder(50);

// 使用字符串初始化
StringBuilder sb3 = new StringBuilder("Hello");
```

### 2.2 基本示例

```java
public class Test {
    public static void main(String args[]) {
        StringBuilder sb = new StringBuilder("test");
        sb.append(" String Builder");
        System.out.println(sb);
    }
}
```

**输出**：

```
test String Builder
```

---

## 三、StringBuffer 基本使用

### 3.1 创建 StringBuffer

```java
// 创建空的 StringBuffer
StringBuffer sbf1 = new StringBuffer();

// 创建指定容量的 StringBuffer
StringBuffer sbf2 = new StringBuffer(50);

// 使用字符串初始化
StringBuffer sbf3 = new StringBuffer("Hello");
```

### 3.2 基本示例

```java
public class Test {
    public static void main(String args[]) {
        StringBuffer sBuffer = new StringBuffer(" test");
        sBuffer.append(" String Buffer");
        System.out.println(sBuffer);
    }
}
```

**输出**：

```
test String Buffer
```

---

## 四、常用方法详解

### 4.1 追加方法

#### append() - 追加内容

```java
StringBuilder sb = new StringBuilder("Hello");

// 追加字符串
sb.append(" World");
System.out.println(sb);  // Hello World

// 追加数字
sb.append(123);
System.out.println(sb);  // Hello World123

// 追加字符
sb.append('!');
System.out.println(sb);  // Hello World123!

// 追加布尔值
sb.append(true);
System.out.println(sb);  // Hello World123!true

// 链式调用
StringBuilder sb2 = new StringBuilder();
sb2.append("Java").append(" ").append("Programming");
System.out.println(sb2);  // Java Programming
```

### 4.2 插入方法

#### insert() - 在指定位置插入

```java
StringBuilder sb = new StringBuilder("Hello World");

// 在索引 5 处插入字符串
sb.insert(5, " Java");
System.out.println(sb);  // Hello Java World

// 插入数字
sb.insert(0, 123);
System.out.println(sb);  // 123Hello Java World

// 插入字符
sb.insert(3, '!');
System.out.println(sb);  // 123!Hello Java World
```

### 4.3 删除方法

#### delete() - 删除指定范围的字符

```java
StringBuilder sb = new StringBuilder("Hello World");

// 删除索引 5 到 11 的字符（不包括 11）
sb.delete(5, 11);
System.out.println(sb);  // Hello

// 删除所有内容
sb.delete(0, sb.length());
System.out.println(sb);  // （空字符串）
```

#### deleteCharAt() - 删除指定位置的字符

```java
StringBuilder sb = new StringBuilder("Hello");
sb.deleteCharAt(1);
System.out.println(sb);  // Hllo
```

### 4.4 替换方法

#### replace() - 替换指定范围的字符

```java
StringBuilder sb = new StringBuilder("Hello World");

// 替换索引 6 到 11 的字符
sb.replace(6, 11, "Java");
System.out.println(sb);  // Hello Java
```

### 4.5 反转方法

#### reverse() - 反转字符序列

```java
StringBuilder sb = new StringBuilder("Hello");
sb.reverse();
System.out.println(sb);  // olleH
```

### 4.6 查找方法

#### indexOf() - 查找子串第一次出现的位置

```java
StringBuilder sb = new StringBuilder("Hello World");

System.out.println(sb.indexOf("World"));     // 6
System.out.println(sb.indexOf("Java"));      // -1（未找到）
System.out.println(sb.indexOf("o"));         // 4
System.out.println(sb.indexOf("o", 5));      // 7（从索引5开始查找）
```

#### lastIndexOf() - 查找子串最后一次出现的位置

```java
StringBuilder sb = new StringBuilder("Hello World");
System.out.println(sb.lastIndexOf("o"));     // 7
System.out.println(sb.lastIndexOf("l"));     // 9
```

### 4.7 截取方法

#### substring() - 截取子字符串

```java
StringBuilder sb = new StringBuilder("Hello World");

// 从索引 6 到末尾
System.out.println(sb.substring(6));         // World

// 从索引 0 到 5（不包括 5）
System.out.println(sb.substring(0, 5));      // Hello
```

#### subSequence() - 返回字符序列

```java
StringBuilder sb = new StringBuilder("Hello World");
CharSequence seq = sb.subSequence(0, 5);
System.out.println(seq);  // Hello
```

### 4.8 字符访问和修改

#### charAt() - 获取指定位置的字符

```java
StringBuilder sb = new StringBuilder("Hello");
char ch = sb.charAt(0);
System.out.println(ch);  // H
```

#### setCharAt() - 设置指定位置的字符

```java
StringBuilder sb = new StringBuilder("Hello");
sb.setCharAt(0, 'h');
System.out.println(sb);  // hello
```

#### getChars() - 复制字符到数组

```java
StringBuilder sb = new StringBuilder("Hello World");
char[] chars = new char[5];
sb.getChars(0, 5, chars, 0);
System.out.println(new String(chars));  // Hello
```

### 4.9 长度和容量

#### length() - 获取长度

```java
StringBuilder sb = new StringBuilder("Hello");
System.out.println(sb.length());  // 5
```

#### capacity() - 获取容量

```java
StringBuilder sb = new StringBuilder();
System.out.println(sb.capacity());  // 16（默认容量）

StringBuilder sb2 = new StringBuilder("Hello");
System.out.println(sb2.capacity());  // 21（16 + 5）
```

#### setLength() - 设置长度

```java
StringBuilder sb = new StringBuilder("Hello World");
sb.setLength(5);
System.out.println(sb);  // Hello

sb.setLength(10);
System.out.println(sb.length());  // 10（用空字符填充）
```

#### ensureCapacity() - 确保最小容量

```java
StringBuilder sb = new StringBuilder();
System.out.println(sb.capacity());  // 16

sb.ensureCapacity(50);
System.out.println(sb.capacity());  // 50 或更大
```

### 4.10 转换方法

#### toString() - 转换为 String

```java
StringBuilder sb = new StringBuilder("Hello");
String str = sb.toString();
System.out.println(str);  // Hello
```

---

## 五、方法完整列表

### 5.1 StringBuffer/StringBuilder 特有方法

|序号|方法|描述|
|---|---|---|
|1|`append(String s)`|追加字符串|
|2|`reverse()`|反转字符序列|
|3|`delete(int start, int end)`|删除子字符串|
|4|`deleteCharAt(int index)`|删除指定位置的字符|
|5|`insert(int offset, String str)`|插入字符串|
|6|`replace(int start, int end, String str)`|替换子字符串|

### 5.2 与 String 类似的方法

|序号|方法|描述|
|---|---|---|
|1|`capacity()`|返回当前容量|
|2|`charAt(int index)`|返回指定索引处的字符|
|3|`ensureCapacity(int min)`|确保容量至少等于指定值|
|4|`getChars(...)`|复制字符到目标数组|
|5|`indexOf(String str)`|返回子串第一次出现的索引|
|6|`indexOf(String str, int from)`|从指定索引开始查找|
|7|`lastIndexOf(String str)`|返回子串最后一次出现的索引|
|8|`lastIndexOf(String str, int from)`|从指定索引反向查找|
|9|`length()`|返回长度|
|10|`setCharAt(int index, char ch)`|设置指定位置的字符|
|11|`setLength(int newLength)`|设置长度|
|12|`subSequence(int start, int end)`|返回字符序列|
|13|`substring(int start)`|返回子字符串|
|14|`substring(int start, int end)`|返回指定范围的子字符串|
|15|`toString()`|转换为字符串|

---

## 六、实用示例

### 6.1 字符串拼接性能对比

```java
public class PerformanceTest {
    public static void main(String[] args) {
        int count = 10000;
        
        // String 拼接
        long start1 = System.currentTimeMillis();
        String str = "";
        for (int i = 0; i < count; i++) {
            str += i;
        }
        long end1 = System.currentTimeMillis();
        System.out.println("String: " + (end1 - start1) + "ms");
        
        // StringBuilder 拼接
        long start2 = System.currentTimeMillis();
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < count; i++) {
            sb.append(i);
        }
        String result = sb.toString();
        long end2 = System.currentTimeMillis();
        System.out.println("StringBuilder: " + (end2 - start2) + "ms");
    }
}
```

**输出示例**：

```
String: 245ms
StringBuilder: 2ms
```

### 6.2 构建 SQL 语句

```java
public class SQLBuilder {
    public static String buildInsertSQL(String table, String[] columns, Object[] values) {
        StringBuilder sql = new StringBuilder();
        sql.append("INSERT INTO ").append(table).append(" (");
        
        for (int i = 0; i < columns.length; i++) {
            sql.append(columns[i]);
            if (i < columns.length - 1) {
                sql.append(", ");
            }
        }
        
        sql.append(") VALUES (");
        
        for (int i = 0; i < values.length; i++) {
            sql.append("'").append(values[i]).append("'");
            if (i < values.length - 1) {
                sql.append(", ");
            }
        }
        
        sql.append(")");
        return sql.toString();
    }
    
    public static void main(String[] args) {
        String[] columns = {"name", "age", "email"};
        Object[] values = {"张三", 25, "zhangsan@example.com"};
        String sql = buildInsertSQL("users", columns, values);
        System.out.println(sql);
    }
}
```

**输出**：

```
INSERT INTO users (name, age, email) VALUES ('张三', '25', 'zhangsan@example.com')
```

### 6.3 字符串反转

```java
public class StringReverser {
    public static String reverse(String str) {
        return new StringBuilder(str).reverse().toString();
    }
    
    public static void main(String[] args) {
        System.out.println(reverse("Hello World"));  // dlroW olleH
        System.out.println(reverse("12345"));        // 54321
    }
}
```

### 6.4 删除字符串中的空格

```java
public class SpaceRemover {
    public static String removeSpaces(String str) {
        StringBuilder sb = new StringBuilder();
        for (char ch : str.toCharArray()) {
            if (ch != ' ') {
                sb.append(ch);
            }
        }
        return sb.toString();
    }
    
    public static void main(String[] args) {
        String str = "Hello World Java";
        System.out.println(removeSpaces(str));  // HelloWorldJava
    }
}
```

### 6.5 构建 HTML 内容

```java
public class HTMLBuilder {
    public static String buildTable(String[][] data) {
        StringBuilder html = new StringBuilder();
        html.append("<table>\n");
        
        for (String[] row : data) {
            html.append("  <tr>\n");
            for (String cell : row) {
                html.append("    <td>").append(cell).append("</td>\n");
            }
            html.append("  </tr>\n");
        }
        
        html.append("</table>");
        return html.toString();
    }
    
    public static void main(String[] args) {
        String[][] data = {
            {"姓名", "年龄", "城市"},
            {"张三", "25", "北京"},
            {"李四", "30", "上海"}
        };
        System.out.println(buildTable(data));
    }
}
```

---

## 七、StringBuilder vs StringBuffer 选择

### 7.1 使用 StringBuilder 的场景

```java
// 单线程环境（推荐）
public String concatenate(String[] words) {
    StringBuilder sb = new StringBuilder();
    for (String word : words) {
        sb.append(word).append(" ");
    }
    return sb.toString().trim();
}
```

### 7.2 使用 StringBuffer 的场景

```java
// 多线程环境
public class ThreadSafeCounter {
    private StringBuffer log = new StringBuffer();
    
    public synchronized void addLog(String message) {
        log.append(message).append("\n");
    }
    
    public String getLog() {
        return log.toString();
    }
}
```

### 7.3 性能对比

```java
public class ComparisonTest {
    public static void main(String[] args) {
        int count = 100000;
        
        // StringBuilder（单线程）
        long start1 = System.currentTimeMillis();
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < count; i++) {
            sb.append(i);
        }
        long end1 = System.currentTimeMillis();
        System.out.println("StringBuilder: " + (end1 - start1) + "ms");
        
        // StringBuffer（线程安全）
        long start2 = System.currentTimeMillis();
        StringBuffer sbf = new StringBuffer();
        for (int i = 0; i < count; i++) {
            sbf.append(i);
        }
        long end2 = System.currentTimeMillis();
        System.out.println("StringBuffer: " + (end2 - start2) + "ms");
    }
}
```

---

## 八、重要知识点总结

### 8.1 容量管理

```java
StringBuilder sb = new StringBuilder();
System.out.println("初始容量: " + sb.capacity());  // 16

sb.append("Hello");
System.out.println("添加后容量: " + sb.capacity());  // 16

// 容量不足时自动扩容（通常是原容量的2倍+2）
for (int i = 0; i < 20; i++) {
    sb.append("a");
}
System.out.println("扩容后容量: " + sb.capacity());  // 34
```

### 8.2 链式调用

```java
StringBuilder sb = new StringBuilder();
String result = sb.append("Hello")
                  .append(" ")
                  .append("World")
                  .append("!")
                  .reverse()
                  .toString();
System.out.println(result);  // !dlroW olleH
```

### 8.3 常见错误

```java
// ❌ 错误：索引越界
StringBuilder sb = new StringBuilder("Hello");
// sb.charAt(10);  // StringIndexOutOfBoundsException

// ❌ 错误：在多线程中使用 StringBuilder
// 可能导致数据不一致

// ✅ 正确：多线程使用 StringBuffer
StringBuffer sbf = new StringBuffer();
```

### 8.4 最佳实践

```java
// ✅ 推荐：预估容量
StringBuilder sb = new StringBuilder(100);  // 避免频繁扩容

// ✅ 推荐：单线程使用 StringBuilder
StringBuilder sb1 = new StringBuilder();

// ✅ 推荐：多线程使用 StringBuffer
StringBuffer sbf = new StringBuffer();

// ✅ 推荐：使用链式调用
String result = new StringBuilder()
    .append("Hello")
    .append(" ")
    .append("World")
    .toString();
```

### 8.5 性能优化建议

1. **预估容量**：避免频繁扩容
2. **单线程优先 StringBuilder**：性能更好
3. **避免频繁 toString()**：只在最后转换一次
4. **循环中使用 StringBuilder**：避免使用 String 拼接

---

## 九、总结对比表

### 9.1 三者对比

|特性|String|StringBuffer|StringBuilder|
|---|---|---|---|
|可变性|不可变|可变|可变|
|线程安全|是|是|否|
|性能|低|中|高|
|内存|频繁创建对象|修改原对象|修改原对象|
|使用场景|字符串不变|多线程修改|单线程修改|
|引入版本|JDK 1.0|JDK 1.0|JDK 1.5|

### 9.2 选择建议

```java
// 场景1：字符串不需要修改
String str = "Hello World";

// 场景2：单线程环境，需要频繁修改
StringBuilder sb = new StringBuilder();

// 场景3：多线程环境，需要频繁修改
StringBuffer sbf = new StringBuffer();

// 场景4：少量拼接（3个以内）
String result = "Hello" + " " + "World";  // 可以接受

// 场景5：大量拼接
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);
}
```

---

## 十、实战技巧

### 10.1 高效字符串拼接

```java
// ❌ 低效
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i + ",";
}

// ✅ 高效
StringBuilder sb = new StringBuilder(5000);  // 预估容量
for (int i = 0; i < 1000; i++) {
    sb.append(i).append(",");
}
String result = sb.toString();
```

### 10.2 条件拼接

```java
public String buildQuery(String name, Integer age, String city) {
    StringBuilder sql = new StringBuilder("SELECT * FROM users WHERE 1=1");
    
    if (name != null) {
        sql.append(" AND name='").append(name).append("'");
    }
    if (age != null) {
        sql.append(" AND age=").append(age);
    }
    if (city != null) {
        sql.append(" AND city='").append(city).append("'");
    }
    
    return sql.toString();
}
```

### 10.3 格式化输出

```java
public String formatList(List<String> items) {
    if (items == null || items.isEmpty()) {
        return "[]";
    }
    
    StringBuilder sb = new StringBuilder("[");
    for (int i = 0; i < items.size(); i++) {
        sb.append(items.get(i));
        if (i < items.size() - 1) {
            sb.append(", ");
        }
    }
    sb.append("]");
    return sb.toString();
}
```

恭喜你完成了 Java StringBuffer 和 StringBuilder 的学习！掌握这两个类对于高效处理字符串操作至关重要。