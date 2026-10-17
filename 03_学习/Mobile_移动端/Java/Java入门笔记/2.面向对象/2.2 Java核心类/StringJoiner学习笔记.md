# StringJoiner 学习笔记

## 1. 问题背景

用 StringBuilder 拼接带分隔符的字符串时，需要手动处理末尾多余的分隔符：

```java
var sb = new StringBuilder();
sb.append("Hello ");
for (String name : names) {
    sb.append(name).append(", ");
}
sb.delete(sb.length() - 2, sb.length());  // 手动去掉最后的 ", "
sb.append("!");
```

> 这种"用分隔符拼接数组"的需求很常见，Java 提供了 StringJoiner 来简化。

---

## 2. StringJoiner 基本用法

```java
import java.util.StringJoiner;

String[] names = {"Bob", "Alice", "Grace"};
var sj = new StringJoiner(", ");  // 指定分隔符
for (String name : names) {
    sj.add(name);
}
System.out.println(sj.toString());  // Bob, Alice, Grace
```

### 指定开头和结尾

```java
var sj = new StringJoiner(", ", "Hello ", "!");
for (String name : names) {
    sj.add(name);
}
System.out.println(sj.toString());  // Hello Bob, Alice, Grace!
```

### 构造方法

| 构造方法 | 说明 |
|----------|------|
| `new StringJoiner(分隔符)` | 只指定分隔符 |
| `new StringJoiner(分隔符, 前缀, 后缀)` | 分隔符 + 开头 + 结尾 |

---

## 3. String.join()

不需要前缀后缀时，用 `String.join()` 更简洁（内部使用 StringJoiner）：

```java
String[] names = {"Bob", "Alice", "Grace"};
String s = String.join(", ", names);  // "Bob, Alice, Grace"
```

---

## 4. StringJoiner vs StringBuilder vs String.join()

| 方式 | 适用场景 |
|------|----------|
| `String.join()` | 简单分隔符拼接，无前后缀 |
| `StringJoiner` | 需要前缀/后缀，或逐步 add |
| `StringBuilder` | 复杂拼接逻辑，非单一分隔符 |

---

## 5. 练习：构造 SELECT 语句

```java
static String buildSelectSql(String table, String[] fields) {
    var sj = new StringJoiner(", ", "SELECT ", " FROM " + table);
    for (String field : fields) {
        sj.add(field);
    }
    return sj.toString();
}
// 输出: SELECT name, position, salary FROM employee
```

---

## 6. 关键总结

| 要点 | 说明 |
|------|------|
| StringJoiner | 用分隔符拼接，自动处理末尾，可加前后缀 |
| String.join() | 简洁写法，无前后缀时首选 |
| 分隔符处理 | 自动在元素之间插入，首尾不多余 |
