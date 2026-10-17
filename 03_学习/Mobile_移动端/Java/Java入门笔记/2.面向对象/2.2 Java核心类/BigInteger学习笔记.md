# BigInteger 学习笔记

## 1. 为什么需要 BigInteger

`long` 是 CPU 原生最大整型（64 位），超出范围就需要 `java.math.BigInteger` 来表示任意大小的整数。

```java
import java.math.BigInteger;

BigInteger bi = new BigInteger("1234567890");
System.out.println(bi.pow(5)); // 2867971860299718107233761438093672048294900000
```

---

## 2. 运算方法

BigInteger 不能用 `+`、`-`、`*`、`/`，必须用实例方法：

```java
BigInteger i1 = new BigInteger("1234567890");
BigInteger i2 = new BigInteger("12345678901234567890");

BigInteger sum = i1.add(i2);       // 加
BigInteger diff = i1.subtract(i2); // 减
BigInteger prod = i1.multiply(i2); // 乘
BigInteger quot = i2.divide(i1);   // 除
BigInteger mod = i2.mod(i1);       // 取余
BigInteger power = i1.pow(5);      // 幂
```

---

## 3. 转换为基本类型

继承自 Number，提供转换方法：

| 方法 | 说明 |
|------|------|
| `intValue()` | 转 int，超出范围会截断 |
| `longValue()` | 转 long，超出范围会截断 |
| `intValueExact()` | 转 int，超出抛 ArithmeticException |
| `longValueExact()` | 转 long，超出抛 ArithmeticException |
| `floatValue()` | 转 float，超出返回 Infinity |
| `doubleValue()` | 转 double，超出返回 Infinity |

```java
BigInteger i = new BigInteger("123456789000");
long l = i.longValue();          // 123456789000（正常）
long l2 = i.multiply(i).longValueExact(); // ❌ ArithmeticException
```

> 需要精确转换时用 `xxxValueExact()`，超出范围直接报错而非静默截断。

---

## 4. 特点

| 特点 | 说明 |
|------|------|
| 无范围限制 | 可以表示任意大的整数 |
| 不可变类 | 运算返回新对象，原对象不变 |
| 继承 Number | 可转为各种基本类型 |
| 速度较慢 | 软件模拟，比 CPU 原生 long 运算慢 |

---

## 5. 关键总结

| 要点 | 说明 |
|------|------|
| 创建 | `new BigInteger("数字字符串")` |
| 运算 | 实例方法：add / subtract / multiply / divide / pow |
| 精确转换 | 用 `longValueExact()` 等方法，超出抛异常 |
| 适用场景 | 超出 long 范围的大整数运算（如密码学、科学计算） |
