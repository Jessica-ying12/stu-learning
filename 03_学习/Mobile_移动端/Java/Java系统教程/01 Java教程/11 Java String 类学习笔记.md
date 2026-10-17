## 一、String 类概述

### 1.1 什么是 String

- 字符串在 Java 编程中广泛应用
- 在 Java 中字符串属于对象
- Java 提供了 `String` 类来创建和操作字符串

### 1.2 String 的特点

- **不可变性**：一旦创建，String 对象的值就无法改变
- **引用类型**：String 是对象，不是基本数据类型
- **常量池**：字符串字面量存储在字符串常量池中

---

## 二、创建字符串

### 2.1 字面量方式（推荐）

```java
String greeting = "Hello world!";
```

当代码中遇到字符串常量时，编译器会使用该值创建一个 String 对象。

### 2.2 使用构造方法

```java
// 使用字符数组创建
char[] helloArray = {'h', 'e', 'l', 'l', 'o', '.'};
String helloString = new String(helloArray);
System.out.println(helloString);  // hello.

// 其他构造方法
String str1 = new String("Hello");
String str2 = new String(bytes);  // 从字节数组
String str3 = new String(bytes, charset);  // 指定字符集
```

### 2.3 创建方式对比

```java
// 方式1：字面量（推荐）
String s1 = "Hello";

// 方式2：构造方法
String s2 = new String("Hello");

// 区别
String a = "Hello";
String b = "Hello";
System.out.println(a == b);  // true（指向同一个常量池对象）

String c = new String("Hello");
String d = new String("Hello");
System.out.println(c == d);  // false（不同的对象）
System.out.println(c.equals(d));  // true（内容相同）
```

---

## 三、字符串长度

### 3.1 length() 方法

```java
public class StringDemo {
    public static void main(String args[]) {
        String palindrome = "Dot saw I was Tod";
        int len = palindrome.length();
        System.out.println("String Length is : " + len);
    }
}
```

**输出**：

```
String Length is : 17
```

### 3.2 示例

```java
String str = "Hello";
System.out.println(str.length());  // 5

String empty = "";
System.out.println(empty.length());  // 0

String chinese = "你好";
System.out.println(chinese.length());  // 2
```

---

## 四、连接字符串

### 4.1 使用 concat() 方法

```java
String string1 = "saw I was ";
String result = "Dot ".concat(string1).concat("Tod");
System.out.println(result);  // Dot saw I was Tod
```

### 4.2 使用 + 操作符（推荐）

```java
String result = "Hello," + " world" + "!";
System.out.println(result);  // Hello, world!

// 示例
public class StringDemo {
    public static void main(String args[]) {
        String string1 = "saw I was ";
        System.out.println("Dot " + string1 + "Tod");
    }
}
```

**输出**：

```
Dot saw I was Tod
```

### 4.3 连接方式对比

```java
// 方式1：+ 操作符（最常用）
String s1 = "Hello" + " " + "World";

// 方式2：concat() 方法
String s2 = "Hello".concat(" ").concat("World");

// 方式3：StringBuilder（大量拼接时推荐）
StringBuilder sb = new StringBuilder();
sb.append("Hello").append(" ").append("World");
String s3 = sb.toString();
```

---

## 五、格式化字符串

### 5.1 使用 format() 方法

```java
String fs;
fs = String.format("The value of the float variable is " +
                   "%f, while the value of the integer " +
                   "variable is %d, and the string " +
                   "is %s", floatVar, intVar, stringVar);
System.out.println(fs);
```

### 5.2 格式化示例

```java
// 格式化数字
String s1 = String.format("整数: %d", 100);
String s2 = String.format("浮点数: %.2f", 3.14159);
String s3 = String.format("字符串: %s", "Hello");

System.out.println(s1);  // 整数: 100
System.out.println(s2);  // 浮点数: 3.14
System.out.println(s3);  // 字符串: Hello

// 多个参数
String s4 = String.format("姓名: %s, 年龄: %d, 分数: %.1f", 
                          "张三", 20, 95.5);
System.out.println(s4);  // 姓名: 张三, 年龄: 20, 分数: 95.5
```

### 5.3 常用格式说明符

|格式符|说明|示例|
|---|---|---|
|`%s`|字符串|`String.format("%s", "Hello")`|
|`%d`|十进制整数|`String.format("%d", 100)`|
|`%f`|浮点数|`String.format("%.2f", 3.14)`|
|`%c`|字符|`String.format("%c", 'A')`|
|`%b`|布尔值|`String.format("%b", true)`|
|`%x`|十六进制整数|`String.format("%x", 255)`|

---

## 六、String 类常用方法

### 6.1 字符访问

#### charAt(int index) - 获取指定位置的字符

```java
String str = "Hello";
char ch = str.charAt(0);
System.out.println(ch);  // H
System.out.println(str.charAt(4));  // o
```

#### toCharArray() - 转换为字符数组

```java
String str = "Hello";
char[] chars = str.toCharArray();
for (char ch : chars) {
    System.out.print(ch + " ");  // H e l l o
}
```

### 6.2 字符串比较

#### equals() - 比较内容是否相等

```java
String s1 = "Hello";
String s2 = "Hello";
String s3 = "hello";

System.out.println(s1.equals(s2));  // true
System.out.println(s1.equals(s3));  // false
```

#### equalsIgnoreCase() - 忽略大小写比较

```java
String s1 = "Hello";
String s2 = "hello";
System.out.println(s1.equalsIgnoreCase(s2));  // true
```

#### compareTo() - 按字典顺序比较

```java
String s1 = "abc";
String s2 = "abd";
String s3 = "abc";

System.out.println(s1.compareTo(s2));  // 负数（s1 < s2）
System.out.println(s1.compareTo(s3));  // 0（相等）
System.out.println(s2.compareTo(s1));  // 正数（s2 > s1）
```

#### compareToIgnoreCase() - 忽略大小写比较

```java
String s1 = "ABC";
String s2 = "abc";
System.out.println(s1.compareToIgnoreCase(s2));  // 0
```

### 6.3 查找方法

#### indexOf() - 查找字符或子串的位置

```java
String str = "Hello World";

// 查找字符
System.out.println(str.indexOf('o'));     // 4（第一次出现）
System.out.println(str.indexOf('o', 5));  // 7（从索引5开始查找）

// 查找子串
System.out.println(str.indexOf("World")); // 6
System.out.println(str.indexOf("Java"));  // -1（未找到）
```

#### lastIndexOf() - 从后向前查找

```java
String str = "Hello World";
System.out.println(str.lastIndexOf('o'));  // 7（最后一次出现）
System.out.println(str.lastIndexOf("o"));  // 7
```

### 6.4 判断方法

#### startsWith() - 判断是否以指定前缀开始

```java
String str = "Hello World";
System.out.println(str.startsWith("Hello"));  // true
System.out.println(str.startsWith("World"));  // false
System.out.println(str.startsWith("World", 6));  // true（从索引6开始）
```

#### endsWith() - 判断是否以指定后缀结束

```java
String str = "Hello World";
System.out.println(str.endsWith("World"));  // true
System.out.println(str.endsWith("Hello"));  // false
```

#### contains() - 判断是否包含子串

```java
String str = "Hello World";
System.out.println(str.contains("World"));  // true
System.out.println(str.contains("Java"));   // false
```

#### isEmpty() - 判断是否为空字符串

```java
String s1 = "";
String s2 = "Hello";
System.out.println(s1.isEmpty());  // true
System.out.println(s2.isEmpty());  // false
```

### 6.5 截取方法

#### substring() - 截取子字符串

```java
String str = "Hello World";

// 从指定位置到末尾
System.out.println(str.substring(6));     // World

// 指定开始和结束位置（不包括结束位置）
System.out.println(str.substring(0, 5));  // Hello
System.out.println(str.substring(6, 11)); // World
```

### 6.6 替换方法

#### replace() - 替换字符或子串

```java
String str = "Hello World";

// 替换字符
System.out.println(str.replace('o', 'a'));  // Hella Warld

// 替换子串
System.out.println(str.replace("World", "Java"));  // Hello Java
```

#### replaceAll() - 使用正则表达式替换

```java
String str = "Hello123World456";
System.out.println(str.replaceAll("\\d+", ""));  // HelloWorld
```

#### replaceFirst() - 替换第一个匹配项

```java
String str = "Hello World World";
System.out.println(str.replaceFirst("World", "Java"));  // Hello Java World
```

### 6.7 分割方法

#### split() - 分割字符串

```java
String str = "apple,banana,orange";
String[] fruits = str.split(",");
for (String fruit : fruits) {
    System.out.println(fruit);
}
// 输出：
// apple
// banana
// orange

// 限制分割次数
String str2 = "a-b-c-d";
String[] parts = str2.split("-", 2);
System.out.println(parts[0]);  // a
System.out.println(parts[1]);  // b-c-d
```

### 6.8 大小写转换

#### toUpperCase() - 转换为大写

```java
String str = "Hello World";
System.out.println(str.toUpperCase());  // HELLO WORLD
```

#### toLowerCase() - 转换为小写

```java
String str = "Hello World";
System.out.println(str.toLowerCase());  // hello world
```

### 6.9 去除空白

#### trim() - 去除首尾空白

```java
String str = "  Hello World  ";
System.out.println(str.trim());  // "Hello World"
System.out.println(str.trim().length());  // 11
```

### 6.10 类型转换

#### valueOf() - 将其他类型转换为字符串

```java
String s1 = String.valueOf(100);        // "100"
String s2 = String.valueOf(3.14);       // "3.14"
String s3 = String.valueOf(true);       // "true"
String s4 = String.valueOf('A');        // "A"

int[] arr = {1, 2, 3};
String s5 = String.valueOf(arr);        // 数组的字符串表示
```

---

## 七、String 方法完整列表

### 7.1 字符访问和转换

|方法|描述|
|---|---|
|`charAt(int index)`|返回指定索引处的字符|
|`toCharArray()`|转换为字符数组|
|`getBytes()`|转换为字节数组|
|`getChars(...)`|将字符复制到目标数组|

### 7.2 比较方法

|方法|描述|
|---|---|
|`equals(Object obj)`|比较内容是否相等|
|`equalsIgnoreCase(String str)`|忽略大小写比较|
|`compareTo(String str)`|按字典顺序比较|
|`compareToIgnoreCase(String str)`|忽略大小写按字典顺序比较|
|`contentEquals(StringBuffer sb)`|与 StringBuffer 比较|

### 7.3 查找方法

|方法|描述|
|---|---|
|`indexOf(int ch)`|查找字符第一次出现的位置|
|`indexOf(String str)`|查找子串第一次出现的位置|
|`lastIndexOf(int ch)`|查找字符最后一次出现的位置|
|`lastIndexOf(String str)`|查找子串最后一次出现的位置|

### 7.4 判断方法

|方法|描述|
|---|---|
|`startsWith(String prefix)`|判断是否以指定前缀开始|
|`endsWith(String suffix)`|判断是否以指定后缀结束|
|`contains(CharSequence s)`|判断是否包含指定序列|
|`isEmpty()`|判断是否为空字符串|
|`matches(String regex)`|判断是否匹配正则表达式|

### 7.5 截取和拼接

|方法|描述|
|---|---|
|`substring(int beginIndex)`|从指定位置截取到末尾|
|`substring(int begin, int end)`|截取指定范围|
|`concat(String str)`|连接字符串|
|`subSequence(int begin, int end)`|返回字符序列|

### 7.6 替换和分割

|方法|描述|
|---|---|
|`replace(char old, char new)`|替换字符|
|`replace(CharSequence old, CharSequence new)`|替换子串|
|`replaceAll(String regex, String replacement)`|正则替换所有|
|`replaceFirst(String regex, String replacement)`|正则替换第一个|
|`split(String regex)`|分割字符串|
|`split(String regex, int limit)`|限制分割次数|

### 7.7 大小写和空白

|方法|描述|
|---|---|
|`toUpperCase()`|转换为大写|
|`toLowerCase()`|转换为小写|
|`trim()`|去除首尾空白|

### 7.8 其他方法

|方法|描述|
|---|---|
|`length()`|返回字符串长度|
|`toString()`|返回字符串本身|
|`hashCode()`|返回哈希码|
|`intern()`|返回规范化表示形式|
|`valueOf(...)`|将其他类型转换为字符串|

---

## 八、实用示例

### 8.1 字符串反转

```java
public class StringReverse {
    public static String reverse(String str) {
        char[] chars = str.toCharArray();
        int left = 0, right = chars.length - 1;
        while (left < right) {
            char temp = chars[left];
            chars[left] = chars[right];
            chars[right] = temp;
            left++;
            right--;
        }
        return new String(chars);
    }
    
    public static void main(String[] args) {
        System.out.println(reverse("Hello"));  // olleH
    }
}
```

### 8.2 统计字符出现次数

```java
public class CharCount {
    public static int countChar(String str, char ch) {
        int count = 0;
        for (int i = 0; i < str.length(); i++) {
            if (str.charAt(i) == ch) {
                count++;
            }
        }
        return count;
    }
    
    public static void main(String[] args) {
        String str = "Hello World";
        System.out.println(countChar(str, 'o'));  // 2
    }
}
```

### 8.3 判断回文字符串

```java
public class Palindrome {
    public static boolean isPalindrome(String str) {
        str = str.toLowerCase().replaceAll("[^a-z0-9]", "");
        int left = 0, right = str.length() - 1;
        while (left < right) {
            if (str.charAt(left) != str.charAt(right)) {
                return false;
            }
            left++;
            right--;
        }
        return true;
    }
    
    public static void main(String[] args) {
        System.out.println(isPalindrome("A man, a plan, a canal: Panama"));  // true
    }
}
```

---

## 九、重要知识点总结

### 9.1 String 的不可变性

```java
String str = "Hello";
str = str + " World";  // 创建了新对象，原对象不变

// 原理
String s1 = "Hello";
String s2 = s1;
s1 = s1 + " World";
System.out.println(s1);  // Hello World
System.out.println(s2);  // Hello（s2 仍指向原对象）
```

### 9.2 字符串比较

```java
// ❌ 错误：使用 == 比较内容
String s1 = new String("Hello");
String s2 = new String("Hello");
if (s1 == s2) { }  // false（比较引用）

// ✅ 正确：使用 equals() 比较内容
if (s1.equals(s2)) { }  // true（比较内容）
```

### 9.3 字符串拼接性能

```java
// ❌ 不推荐：循环中使用 + 拼接
String result = "";
for (int i = 0; i < 1000; i++) {
    result += i;  // 每次都创建新对象
}

// ✅ 推荐：使用 StringBuilder
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);
}
String result = sb.toString();
```

### 9.4 常见错误

```java
// ❌ 错误：空指针
String str = null;
// int len = str.length();  // NullPointerException

// ✅ 正确：检查 null
if (str != null) {
    int len = str.length();
}

// ❌ 错误：索引越界
String str = "Hello";
// char ch = str.charAt(10);  // StringIndexOutOfBoundsException

// ✅ 正确：检查索引
if (index >= 0 && index < str.length()) {
    char ch = str.charAt(index);
}
```

### 9.5 最佳实践

1. **优先使用字面量创建字符串**
2. **使用 equals() 比较字符串内容**
3. **大量拼接使用 StringBuilder**
4. **注意 null 检查**
5. **使用 String 的不可变性保证线程安全**

---

## 十、String、StringBuilder 和 StringBuffer

|特性|String|StringBuilder|StringBuffer|
|---|---|---|---|
|可变性|不可变|可变|可变|
|线程安全|安全|不安全|安全|
|性能|低（频繁修改时）|高|中|
|使用场景|少量操作|单线程大量操作|多线程大量操作|

```java
// String - 不可变
String str = "Hello";
str = str + " World";  // 创建新对象

// StringBuilder - 可变，线程不安全，性能高
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");  // 修改原对象

// StringBuffer - 可变，线程安全，性能较低
StringBuffer sbf = new StringBuffer("Hello");
sbf.append(" World");  // 修改原对象
```

恭喜你完成了 Java String 类的学习！String 是 Java 中最常用的类之一，掌握它的使用对 Java 编程至关重要。