# StringBuilder 学习笔记

## 1. 为什么需要 StringBuilder

循环中用 `+` 拼接字符串，每次都创建新对象，浪费内存且影响 GC：

```java
// ❌ 低效：每次循环创建临时字符串对象
String s = "";
for (int i = 0; i < 1000; i++) {
    s = s + "," + i;
}

// ✅ 高效：StringBuilder 预分配缓冲区，不创建临时对象
StringBuilder sb = new StringBuilder(1024);
for (int i = 0; i < 1000; i++) {
    sb.append(',');
    sb.append(i);
}
String s = sb.toString();
```

---

## 2. 基本用法

```java
StringBuilder sb = new StringBuilder(1024);  // 预分配容量
sb.append("Hello");
sb.append(" ");
sb.append("World");
String result = sb.toString();  // "Hello World"
```

### 常用方法

| 方法 | 作用 |
|------|------|
| `append(xxx)` | 追加内容（支持各种类型） |
| `insert(offset, xxx)` | 在指定位置插入 |
| `delete(start, end)` | 删除指定范围 |
| `reverse()` | 反转 |
| `toString()` | 转为 String |

---

## 3. 链式操作

`append()` 返回 `this`，支持链式调用：

```java
var sb = new StringBuilder(1024);
sb.append("Mr ")
  .append("Bob")
  .append("!")
  .insert(0, "Hello, ");
System.out.println(sb.toString());  // "Hello, Mr Bob!"
```

### 链式操作的实现原理

```java
public StringBuilder append(String s) {
    // ... 追加逻辑
    return this;  // 返回自身，使得可以继续调用
}
```

---

## 4. StringBuilder vs StringBuffer vs +

| 对比 | StringBuilder | StringBuffer | + 拼接 |
|------|--------------|-------------|--------|
| 可变性 | 可变 | 可变 | 不可变（每次创建新对象） |
| 线程安全 | ❌ | ✅（同步） | - |
| 性能 | 最快 | 较慢（同步开销） | 编译器自动优化 |
| 使用场景 | 循环拼接 | 几乎不用了 | 简单拼接 |

> **普通的 `+` 拼接**不需要手动改为 StringBuilder，编译器会自动优化。只有循环中大量拼接时才需要手动使用 StringBuilder。

---

## 5. 练习：构造 INSERT 语句

```java
static String buildInsertSql(String table, String[] fields) {
    var sb = new StringBuilder();
    sb.append("INSERT INTO ")
      .append(table)
      .append(" (");
    for (int i = 0; i < fields.length; i++) {
        if (i > 0) sb.append(", ");
        sb.append(fields[i]);
    }
    sb.append(") VALUES (");
    for (int i = 0; i < fields.length; i++) {
        if (i > 0) sb.append(", ");
        sb.append("?");
    }
    sb.append(")");
    return sb.toString();
}
// 输出: INSERT INTO employee (name, position, salary) VALUES (?, ?, ?)
```

---

## 6. 关键总结

| 要点 | 说明 |
|------|------|
| 用途 | 循环中高效拼接字符串 |
| 原理 | 可变对象，预分配缓冲区，避免创建临时对象 |
| 链式操作 | append() 返回 this |
| StringBuffer | 线程安全版，现在基本不用 |
| 普通 + 拼接 | 编译器自动优化，无需手动替换 |
