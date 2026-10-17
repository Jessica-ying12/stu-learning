## 一、基本数据类型与包装类

### 1.1 八种基本数据类型

|基本数据类型|包装类|说明|
|---|---|---|
|`boolean`|`Boolean`|布尔型|
|`byte`|`Byte`|字节型|
|`short`|`Short`|短整型|
|`int`|`Integer`|整型|
|`long`|`Long`|长整型|
|`char`|`Character`|字符型|
|`float`|`Float`|单精度浮点型|
|`double`|`Double`|双精度浮点型|

### 1.2 包装类的特点

- 所有包装类都用 `final` 声明，不可被继承重写
- 都继承自 `Number` 抽象类（除了 `Boolean` 和 `Character`）
- 位于 `java.lang` 包下

---

## 二、自动装箱与拆箱

### 2.1 概念

- **装箱（Boxing）**：将基本数据类型转换为包装类对象
- **拆箱（Unboxing）**：将包装类对象转换为基本数据类型

### 2.2 自动装箱拆箱示例

```java
public static void main(String[] args) {
    int num1 = 1;
    
    // 自动装箱：基本类型 → 包装类
    Integer num2 = num1;
    
    Integer num3 = 3;
    
    // 自动拆箱：包装类 → 基本类型
    int num4 = num3;
}
```

### 2.3 装箱拆箱的完整示例

```java
public class Test {
    public static void main(String args[]) {
        Integer x = 5;      // 装箱：int → Integer
        x = x + 10;         // 拆箱 + 装箱：Integer → int → Integer
        System.out.println(x);
    }
}
```

**输出**：

```
15
```

**说明**：

1. `x` 被赋值为整型值 5 时，编译器自动装箱
2. 执行 `x + 10` 时，先拆箱为 int 进行运算
3. 运算结果再装箱为 Integer 赋值给 x

---

## 三、Number 抽象类

### 3.1 Number 类的定义

```java
package java.lang;

public abstract class Number implements java.io.Serializable {
    
    public abstract int intValue();
    
    public abstract long longValue();
    
    public abstract float floatValue();
    
    public abstract double doubleValue();
    
    public byte byteValue() {
        return (byte)intValue();
    }
    
    public short shortValue() {
        return (short)intValue();
    }
    
    private static final long serialVersionUID = -8742448824652078965L;
}
```

### 3.2 Number 类的作用

- 提供将包装类型拆箱成基本类型的方法
- 所有数值类型的包装类都继承自 Number
- 是一个抽象类，不能直接实例化

### 3.3 Number 类的继承关系

```
Number (抽象类)
├── Byte
├── Short
├── Integer
├── Long
├── Float
└── Double
```

---

## 四、Math 类

### 4.1 Math 类概述

- 位于 `java.lang` 包下
- 包含执行基本数学运算的属性和方法
- 所有方法都是 `static` 形式
- 可以直接通过类名调用

### 4.2 Math 类常用常量

```java
Math.PI     // 圆周率 π ≈ 3.141592653589793
Math.E      // 自然对数的底数 e ≈ 2.718281828459045
```

### 4.3 三角函数示例

```java
public class Test {
    public static void main(String[] args) {
        System.out.println("90 度的正弦值：" + Math.sin(Math.PI/2));
        System.out.println("0度的余弦值：" + Math.cos(0));
        System.out.println("60度的正切值：" + Math.tan(Math.PI/3));
        System.out.println("1的反正切值： " + Math.atan(1));
        System.out.println("π/2的角度值：" + Math.toDegrees(Math.PI/2));
        System.out.println(Math.PI);
    }
}
```

**输出**：

```
90 度的正弦值：1.0
0度的余弦值：1.0
60度的正切值：1.7320508075688767
1的反正切值： 0.7853981633974483
π/2的角度值：90.0
3.141592653589793
```

---

## 五、Number 类常用方法

### 5.1 类型转换方法

|方法|描述|示例|
|---|---|---|
|`xxxValue()`|将 Number 对象转换为 xxx 数据类型|`intValue()`, `doubleValue()`|

```java
Integer num = 100;
int i = num.intValue();           // 转换为 int
double d = num.doubleValue();     // 转换为 double
long l = num.longValue();         // 转换为 long
```

### 5.2 比较方法

|方法|描述|返回值|
|---|---|---|
|`compareTo()`|将 Number 对象与参数比较|相等返回0，小于返回负数，大于返回正数|
|`equals()`|判断 Number 对象是否与参数相等|boolean|

```java
Integer x = 5;
System.out.println(x.compareTo(3));   // 1（大于）
System.out.println(x.compareTo(5));   // 0（相等）
System.out.println(x.compareTo(8));   // -1（小于）
System.out.println(x.equals(5));      // true
```

### 5.3 转换方法

|方法|描述|示例|
|---|---|---|
|`valueOf()`|返回指定类型的包装类对象|`Integer.valueOf(10)`|
|`toString()`|以字符串形式返回值|`num.toString()`|
|`parseInt()`|将字符串解析为 int 类型|`Integer.parseInt("123")`|

```java
// valueOf() - 字符串转包装类
Integer num1 = Integer.valueOf("100");
Double num2 = Double.valueOf("3.14");

// toString() - 包装类转字符串
String str1 = num1.toString();

// parseInt() - 字符串转基本类型
int i = Integer.parseInt("123");
```

---

## 六、Math 类常用方法

### 6.1 基本数学运算

|方法|描述|示例|
|---|---|---|
|`abs()`|返回参数的绝对值|`Math.abs(-5)` → 5|
|`max()`|返回两个参数中的最大值|`Math.max(10, 20)` → 20|
|`min()`|返回两个参数中的最小值|`Math.min(10, 20)` → 10|
|`pow()`|返回第一个参数的第二个参数次方|`Math.pow(2, 3)` → 8.0|
|`sqrt()`|求参数的算术平方根|`Math.sqrt(16)` → 4.0|

```java
System.out.println(Math.abs(-10));        // 10
System.out.println(Math.max(5, 10));      // 10
System.out.println(Math.min(5, 10));      // 5
System.out.println(Math.pow(2, 3));       // 8.0
System.out.println(Math.sqrt(16));        // 4.0
```

### 6.2 取整方法

|方法|描述|示例|
|---|---|---|
|`ceil()`|向上取整（返回大于等于参数的最小整数）|`Math.ceil(3.2)` → 4.0|
|`floor()`|向下取整（返回小于等于参数的最大整数）|`Math.floor(3.8)` → 3.0|
|`round()`|四舍五入|`Math.round(3.5)` → 4|
|`rint()`|返回最接近的整数（double类型）|`Math.rint(3.5)` → 4.0|

```java
System.out.println(Math.ceil(3.2));       // 4.0
System.out.println(Math.floor(3.8));      // 3.0
System.out.println(Math.round(3.5));      // 4
System.out.println(Math.round(3.4));      // 3
System.out.println(Math.rint(3.5));       // 4.0
```

### 6.3 指数和对数

|方法|描述|示例|
|---|---|---|
|`exp()`|返回 e 的参数次方|`Math.exp(1)` → e|
|`log()`|返回参数的自然对数|`Math.log(Math.E)` → 1.0|
|`log10()`|返回参数的以10为底的对数|`Math.log10(100)` → 2.0|

```java
System.out.println(Math.exp(1));          // 2.718281828459045
System.out.println(Math.log(Math.E));     // 1.0
System.out.println(Math.log10(100));      // 2.0
```

### 6.4 三角函数

|方法|描述|参数单位|
|---|---|---|
|`sin()`|正弦值|弧度|
|`cos()`|余弦值|弧度|
|`tan()`|正切值|弧度|
|`asin()`|反正弦值|弧度|
|`acos()`|反余弦值|弧度|
|`atan()`|反正切值|弧度|
|`atan2()`|笛卡尔坐标转极坐标角度|弧度|

```java
// 三角函数（参数为弧度）
System.out.println(Math.sin(Math.PI/2));  // 1.0（90度的正弦）
System.out.println(Math.cos(0));          // 1.0（0度的余弦）
System.out.println(Math.tan(Math.PI/4));  // 1.0（45度的正切）

// 反三角函数
System.out.println(Math.asin(1));         // π/2
System.out.println(Math.acos(1));         // 0.0
System.out.println(Math.atan(1));         // π/4
```

### 6.5 角度与弧度转换

|方法|描述|示例|
|---|---|---|
|`toDegrees()`|弧度转角度|`Math.toDegrees(Math.PI)` → 180.0|
|`toRadians()`|角度转弧度|`Math.toRadians(180)` → π|

```java
System.out.println(Math.toDegrees(Math.PI));    // 180.0
System.out.println(Math.toRadians(180));        // 3.141592653589793
```

### 6.6 随机数

|方法|描述|返回值|
|---|---|---|
|`random()`|返回随机数|[0.0, 1.0) 之间的 double 值|

```java
// 生成 [0.0, 1.0) 之间的随机数
double rand = Math.random();

// 生成 [0, 100) 之间的随机整数
int randomInt = (int)(Math.random() * 100);

// 生成 [min, max] 之间的随机整数
int min = 10;
int max = 50;
int randomRange = (int)(Math.random() * (max - min + 1)) + min;
```

---

## 七、Number & Math 方法完整列表

### 7.1 Number 类方法

|序号|方法|描述|
|---|---|---|
|1|`xxxValue()`|将 Number 对象转换为 xxx 数据类型|
|2|`compareTo()`|将 Number 对象与参数比较|
|3|`equals()`|判断 Number 对象是否与参数相等|
|4|`valueOf()`|返回指定类型的包装类对象|
|5|`toString()`|以字符串形式返回值|
|6|`parseInt()`|将字符串解析为 int 类型|

### 7.2 Math 类方法

|序号|方法|描述|
|---|---|---|
|7|`abs()`|返回参数的绝对值|
|8|`ceil()`|向上取整|
|9|`floor()`|向下取整|
|10|`rint()`|返回最接近的整数（double）|
|11|`round()`|四舍五入|
|12|`min()`|返回两个参数中的最小值|
|13|`max()`|返回两个参数中的最大值|
|14|`exp()`|返回 e 的参数次方|
|15|`log()`|返回自然对数|
|16|`pow()`|返回第一个参数的第二个参数次方|
|17|`sqrt()`|求算术平方根|
|18|`sin()`|求正弦值|
|19|`cos()`|求余弦值|
|20|`tan()`|求正切值|
|21|`asin()`|求反正弦值|
|22|`acos()`|求反余弦值|
|23|`atan()`|求反正切值|
|24|`atan2()`|笛卡尔坐标转极坐标角度|
|25|`toDegrees()`|弧度转角度|
|26|`toRadians()`|角度转弧度|
|27|`random()`|返回随机数|

---

## 八、实用示例

### 8.1 数值转换

```java
// 字符串转数值
String str = "123";
int i = Integer.parseInt(str);
double d = Double.parseDouble("3.14");

// 数值转字符串
String s1 = String.valueOf(123);
String s2 = Integer.toString(456);

// 不同数值类型转换
Integer num = 100;
double d2 = num.doubleValue();
long l = num.longValue();
```

### 8.2 数学计算

```java
// 计算圆的面积
double radius = 5.0;
double area = Math.PI * Math.pow(radius, 2);

// 计算两点之间的距离
double x1 = 0, y1 = 0;
double x2 = 3, y2 = 4;
double distance = Math.sqrt(Math.pow(x2-x1, 2) + Math.pow(y2-y1, 2));

// 生成指定范围的随机数
int min = 1, max = 100;
int random = (int)(Math.random() * (max - min + 1)) + min;
```

### 8.3 取整操作

```java
double value = 3.7;

System.out.println(Math.ceil(value));    // 4.0（向上取整）
System.out.println(Math.floor(value));   // 3.0（向下取整）
System.out.println(Math.round(value));   // 4（四舍五入）

// 保留两位小数
double num = 3.14159;
double rounded = Math.round(num * 100.0) / 100.0;  // 3.14
```

---

## 九、重要知识点总结

### 9.1 装箱拆箱注意事项

```java
// ✅ 自动装箱拆箱
Integer a = 10;
int b = a;

// ⚠️ 注意 null 值
Integer c = null;
// int d = c;  // 运行时异常：NullPointerException

// ✅ 正确处理
if (c != null) {
    int d = c;
}
```

### 9.2 包装类缓存

```java
// Integer 缓存 -128 到 127
Integer a = 100;
Integer b = 100;
System.out.println(a == b);  // true（使用缓存）

Integer c = 200;
Integer d = 200;
System.out.println(c == d);  // false（超出缓存范围）

// ✅ 正确比较
System.out.println(c.equals(d));  // true
```

### 9.3 Math 方法使用技巧

```java
// 取绝对值
int abs = Math.abs(-10);

// 找最大最小值
int max = Math.max(10, 20);
int min = Math.min(10, 20);

// 幂运算
double power = Math.pow(2, 10);  // 1024.0

// 平方根
double sqrt = Math.sqrt(16);     // 4.0

// 随机数
double random = Math.random();   // [0.0, 1.0)
```

### 9.4 常见错误

```java
// ❌ 错误：除以零
// int result = 10 / 0;  // 运行时异常

// ❌ 错误：字符串转换
// int num = Integer.parseInt("abc");  // NumberFormatException

// ✅ 正确：异常处理
try {
    int num = Integer.parseInt("abc");
} catch (NumberFormatException e) {
    System.out.println("无效的数字格式");
}

// ❌ 错误：浮点数比较
double a = 0.1 + 0.2;
// if (a == 0.3) { }  // 可能为 false

// ✅ 正确：使用误差范围
if (Math.abs(a - 0.3) < 0.0001) {
    // 认为相等
}
```

### 9.5 最佳实践

1. **使用包装类时注意 null 检查**
2. **比较包装类对象使用 equals() 而不是 ==**
3. **字符串转数值时进行异常处理**
4. **浮点数比较使用误差范围**
5. **优先使用 Math 类的静态方法进行数学运算**
6. **三角函数参数使用弧度而非角度**