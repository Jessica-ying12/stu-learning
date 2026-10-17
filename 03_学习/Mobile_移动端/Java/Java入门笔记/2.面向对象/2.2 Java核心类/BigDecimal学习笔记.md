# BigDecimal 学习笔记

## 1. 什么是 BigDecimal

表示**任意大小且精度完全准确**的浮点数，常用于财务计算。

```java
import java.math.BigDecimal;

BigDecimal bd = new BigDecimal("123.4567");
System.out.println(bd.multiply(bd)); // 15241.55677489（精确）
```

> 内部由 BigInteger + scale（小数位数）组成。

---

## 2. scale（小数位数）

```java
new BigDecimal("123.45").scale()    // 2
new BigDecimal("123.4500").scale()  // 4
new BigDecimal("1234500").scale()   // 0
```

### 去除末尾 0

```java
BigDecimal d = new BigDecimal("123.4500");
d.stripTrailingZeros().scale()  // 2

BigDecimal d2 = new BigDecimal("1234500");
d2.stripTrailingZeros().scale() // -2（整数，末尾有2个0）
```

### 设置精度

```java
BigDecimal d = new BigDecimal("123.456789");
d.setScale(4, RoundingMode.HALF_UP)  // 123.4568（四舍五入）
d.setScale(4, RoundingMode.DOWN)     // 123.4567（直接截断）
```

---

## 3. 运算

```java
BigDecimal a = new BigDecimal("123.456");
BigDecimal b = new BigDecimal("23.456789");

a.add(b)        // 加
a.subtract(b)   // 减
a.multiply(b)   // 乘（精度不丢失）
```

### 除法（必须指定精度）

```java
a.divide(b, 10, RoundingMode.HALF_UP)  // ✅ 保留10位小数，四舍五入
a.divide(b)                             // ❌ ArithmeticException（除不尽时报错）
```

### 同时求商和余数

```java
BigDecimal n = new BigDecimal("12.345");
BigDecimal m = new BigDecimal("0.12");
BigDecimal[] dr = n.divideAndRemainder(m);
// dr[0] = 102（商，整数）
// dr[1] = 0.105（余数）

// 判断是否整除
if (dr[1].signum() == 0) {
    // n 是 m 的整数倍
}
```

---

## 4. 比较（重点）

```java
BigDecimal d1 = new BigDecimal("123.456");
BigDecimal d2 = new BigDecimal("123.45600");

d1.equals(d2)      // false！scale 不同（3 vs 5）
d1.compareTo(d2)   // 0（值相等）
```

| 方法 | 比较规则 | 推荐 |
|------|----------|------|
| `equals()` | 值相等**且** scale 相等 | ❌ 不推荐 |
| `compareTo()` | 只比较数值大小 | ✅ 始终使用 |

> compareTo 返回值：负数 = 小于，0 = 等于，正数 = 大于

---

## 5. 关键总结

| 要点 | 说明 |
|------|------|
| 用途 | 精确小数运算，常用于财务 |
| 创建 | `new BigDecimal("字符串")`（不要用 double 构造） |
| 除法 | 必须指定精度和舍入模式 |
| 比较 | 用 `compareTo()`，不用 `equals()` |
| 不可变 | 运算返回新对象 |
| 继承 Number | 可转为基本类型 |
