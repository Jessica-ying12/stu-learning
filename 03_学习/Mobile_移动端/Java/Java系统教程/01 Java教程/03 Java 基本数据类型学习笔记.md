## 一、变量与内存

### 1.1 变量的本质

- 变量是申请内存来存储值
- 创建变量时，需要在内存中申请空间
- 内存管理系统根据变量类型分配存储空间
- 分配的空间只能存储该类型数据

### 1.2 Java 的两大数据类型

1. **内置数据类型**（基本数据类型）
2. **引用数据类型**

---

## 二、八种基本数据类型

Java 提供了 8 种基本类型：

- 6 种数字类型（4 个整数型 + 2 个浮点型）
- 1 种字符类型
- 1 种布尔型

### 2.1 整数类型

#### 1. byte（字节型）

|属性|值|
|---|---|
|位数|8 位|
|表示方式|有符号，二进制补码|
|最小值|-128（-2⁷）|
|最大值|127（2⁷-1）|
|默认值|0|
|用途|大型数组中节约空间（占用空间是 int 的 1/4）|

**示例**：

```java
byte a = 100;
byte b = -50;
```

#### 2. short（短整型）

|属性|值|
|---|---|
|位数|16 位|
|表示方式|有符号，二进制补码|
|最小值|-32768（-2¹⁵）|
|最大值|32767（2¹⁵-1）|
|默认值|0|
|用途|节省空间（占用空间是 int 的 1/2）|

**示例**：

```java
short s = 1000;
short r = -20000;
```

#### 3. int（整型）

|属性|值|
|---|---|
|位数|32 位|
|表示方式|有符号，二进制补码|
|最小值|-2,147,483,648（-2³¹）|
|最大值|2,147,483,647（2³¹-1）|
|默认值|0|
|说明|整型变量默认为 int 类型|

**示例**：

```java
int a = 100000;
int b = -200000;
```

#### 4. long（长整型）

|属性|值|
|---|---|
|位数|64 位|
|表示方式|有符号，二进制补码|
|最小值|-9,223,372,036,854,775,808（-2⁶³）|
|最大值|9,223,372,036,854,775,807（2⁶³-1）|
|默认值|0L|
|用途|需要比较大整数的系统|

**示例**：

```java
long a = 100000L;
long b = -200000L;
```

**注意**：long 类型数值后面要加 `L` 或 `l`

---

### 2.2 浮点类型

#### 1. float（单精度浮点型）

|属性|值|
|---|---|
|位数|32 位|
|标准|IEEE 754|
|默认值|0.0f|
|用途|存储大型浮点数组时节省内存|
|注意|不能表示精确的值（如货币）|

**示例**：

```java
float f1 = 234.5f;
```

**注意**：float 类型数值后面要加 `f` 或 `F`

#### 2. double（双精度浮点型）

|属性|值|
|---|---|
|位数|64 位|
|标准|IEEE 754|
|默认值|0.0d|
|说明|浮点数的默认类型|
|注意|不能表示精确的值（如货币）|

**示例**：

```java
double d1 = 123.4;
```

---

### 2.3 布尔类型

#### boolean（布尔型）

| 属性  | 值                |
| --- | ---------------- |
| 位数  | 1 位信息            |
| 取值  | true 或 false     |
| 默认值 | false            |
| 用途  | 标志 true/false 情况 |

**示例**：

```java
boolean one = true;
boolean flag = false;
```

---

### 2.4 字符类型

#### char（字符型）

|属性|值|
|---|---|
|位数|16 位|
|字符集|Unicode 字符|
|最小值|'\u0000'（即 0）|
|最大值|'\uffff'（即 65535）|
|用途|存储任何字符|

**示例**：

```java
char letter = 'A';
char unicode = '\u0041';  // 也是 'A'
```

---

## 三、基本类型的包装类

每个基本类型都有对应的包装类，包装类中定义了类型的常量信息。

### 3.1 包装类对照表

|基本类型|包装类|位数|
|---|---|---|
|byte|java.lang.Byte|8|
|short|java.lang.Short|16|
|int|java.lang.Integer|32|
|long|java.lang.Long|64|
|float|java.lang.Float|32|
|double|java.lang.Double|64|
|char|java.lang.Character|16|
|boolean|java.lang.Boolean|-|

### 3.2 查看类型范围示例

```java
public class PrimitiveTypeTest {
    public static void main(String[] args) {
        // byte
        System.out.println("基本类型：byte 二进制位数：" + Byte.SIZE);
        System.out.println("包装类：java.lang.Byte");
        System.out.println("最小值：Byte.MIN_VALUE=" + Byte.MIN_VALUE);
        System.out.println("最大值：Byte.MAX_VALUE=" + Byte.MAX_VALUE);
        System.out.println();
        
        // int
        System.out.println("基本类型：int 二进制位数：" + Integer.SIZE);
        System.out.println("包装类：java.lang.Integer");
        System.out.println("最小值：Integer.MIN_VALUE=" + Integer.MIN_VALUE);
        System.out.println("最大值：Integer.MAX_VALUE=" + Integer.MAX_VALUE);
        System.out.println();
        
        // char
        System.out.println("基本类型：char 二进制位数：" + Character.SIZE);
        System.out.println("包装类：java.lang.Character");
        System.out.println("最小值：Character.MIN_VALUE=" + (int) Character.MIN_VALUE);
        System.out.println("最大值：Character.MAX_VALUE=" + (int) Character.MAX_VALUE);
    }
}
```

### 3.3 输出结果

```
基本类型：byte 二进制位数：8
包装类：java.lang.Byte
最小值：Byte.MIN_VALUE=-128
最大值：Byte.MAX_VALUE=127

基本类型：int 二进制位数：32
包装类：java.lang.Integer
最小值：Integer.MIN_VALUE=-2147483648
最大值：Integer.MAX_VALUE=2147483647

基本类型：char 二进制位数：16
包装类：java.lang.Character
最小值：Character.MIN_VALUE=0
最大值：Character.MAX_VALUE=65535
```

### 3.4 科学记数法说明

Float 和 Double 的值以科学记数法输出：

- `3.14E3` = 3.14 × 10³ = 3140
- `3.14E-3` = 3.14 × 10⁻³ = 0.00314

---

## 四、void 类型

Java 中还存在 `void` 类型：

- 对应包装类：`java.lang.Void`
- 无法直接对其进行操作
- 用于表示方法无返回值

---

## 五、引用数据类型

### 5.1 引用类型的特点

- 由类的构造函数创建
- 用于访问所引用的对象
- 声明时指定特定类型（如 Employee、Puppy 等）
- 类型一旦声明后不能改变
- 默认值都是 `null`

### 5.2 引用类型包括

- 对象
- 数组
- 接口

### 5.3 示例

```java
Animal animal = new Animal("giraffe");
String str = "Hello";
int[] arr = new int[10];
```

---

## 六、Java 常量

### 6.1 常量的定义

- 常量是固定值，不能改变
- 使用 `final` 关键字声明
- 通常使用大写字母表示

### 6.2 常量声明示例

```java
final double PI = 3.1415927;
final int MAX_VALUE = 100;
final String COMPANY_NAME = "ABC Corp";
```

---

## 七、字面量

```
字面量（Literal）是指在代码中直接写出的固定值，它们直接表示数据本身，而不是通过变量或表达式计算得出。
```

### 7.1 整数字面量

```java
byte a = 68;
char a = 'A';

// 十进制
int decimal = 100;

// 八进制（前缀 0）
int octal = 0144;

// 十六进制（前缀 0x）
int hexa = 0x64;
```

### 7.2 字符串字面量

```java
"Hello World"
"two\nlines"
"\"This is in quotes\""
```

### 7.3 Unicode 字符

```java
char a = '\u0001';
String a = "\u0001";
```

---

## 八、转义字符

### 8.1 常用转义字符表

|符号|字符含义|
|---|---|
|`\n`|换行 (0x0a)|
|`\r`|回车 (0x0d)|
|`\f`|换页符 (0x0c)|
|`\b`|退格 (0x08)|
|`\0`|空字符 (0x0)|
|`\s`|空格|
|`\t`|制表符|
|`\"`|双引号|
|`\'`|单引号|
|`\\`|反斜杠|
|`\ddd`|八进制字符|
|`\uxxxx`|16 进制 Unicode 字符|

### 8.2 转义字符示例

```java
System.out.println("Hello\nWorld");     // 换行
System.out.println("Name:\tJohn");      // 制表符
System.out.println("He said \"Hi\"");   // 双引号
System.out.println("Path: C:\\Users");  // 反斜杠
```

---

## 九、重要知识点总结

### 9.1 类型选择建议

1. **整数**：默认使用 `int`，超大数用 `long`
2. **浮点数**：默认使用 `double`，内存敏感用 `float`
3. **字符**：使用 `char`
4. **布尔**：使用 `boolean`
5. **货币计算**：不要用 float/double，使用 `BigDecimal`

### 9.2 类型转换注意事项

- 小类型可以自动转换为大类型
- 大类型转小类型需要强制转换（可能丢失精度）
- long 类型数值加 `L`
- float 类型数值加 `f`

### 9.3 默认值记忆

- 数值类型：`0`
- 布尔类型：`false`
- 字符类型：`'\u0000'`
- 引用类型：`null`

### 9.4 最佳实践

1. 使用包装类的常量获取类型范围
2. 常量使用 `final` 和大写命名
3. 避免使用魔法数字，定义为常量
4. 注意浮点数精度问题
5. 合理选择数据类型以节省内存