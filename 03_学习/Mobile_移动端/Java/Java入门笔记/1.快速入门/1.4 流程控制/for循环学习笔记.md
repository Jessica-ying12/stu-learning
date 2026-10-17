# for 循环学习笔记

## 1. 基本语法

```java
for (初始条件; 循环检测条件; 循环后更新计数器) {
    // 执行语句
}
```

**执行流程：**

```
初始化(一次) → 判断条件 → true → 执行循环体 → 更新计数器 → 回到判断条件
                  │
                  └→ false → 跳出循环
```

---

## 2. 典型示例

### 累加 1 到 100

```java
int sum = 0;
for (int i = 1; i <= 100; i++) {
    sum = sum + i;
}
System.out.println(sum);  // 5050
```

### 遍历数组

```java
int[] ns = { 1, 4, 9, 16, 25 };
int sum = 0;
for (int i = 0; i < ns.length; i++) {
    sum = sum + ns[i];
}
```

> 循环条件用 `i < ns.length`（不是 `<=`），因为索引范围是 0 到 length-1。

---

## 3. for vs while vs do while

| 对比 | for | while | do while |
|------|-----|-------|----------|
| 适用场景 | 已知循环次数 | 未知次数，先判断 | 至少执行一次 |
| 计数器 | 集中管理在 `for()` 中 | 散落在循环外和循环体内 | 同 while |
| 可读性 | 初始化、条件、更新一目了然 | 需要自己找 | 需要自己找 |

---

## 4. 注意事项

### 不要在循环体内修改计数器

```java
// ❌ 错误：循环体内修改 i，实际每次加了 2
for (int i = 0; i < ns.length; i++) {
    System.out.println(ns[i]);
    i = i + 1;  // 不要这样做！
}

// ✅ 正确：通过更新语句控制步长
for (int i = 0; i < ns.length; i = i + 2) {
    System.out.println(ns[i]);  // 只访问偶数索引
}
```

### 计数器变量定义在 for 循环内

```java
// ✅ 推荐：i 的作用域仅限 for 循环
for (int i = 0; i < ns.length; i++) { ... }

// ❌ 不推荐：i 在循环外仍可访问，违反作用域最小化原则
int i;
for (i = 0; i < ns.length; i++) { ... }
```

---

## 5. for each 循环

直接遍历数组每个元素的值，无需索引：

```java
int[] ns = { 1, 4, 9, 16, 25 };
for (int n : ns) {
    System.out.println(n);
}
```

### for vs for each

| 对比 | for | for each |
|------|-----|----------|
| 写法 | `for (int i=0; i<ns.length; i++)` | `for (int n : ns)` |
| 访问元素 | 通过索引 `ns[i]` | 直接得到值 `n` |
| 能否获取索引 | ✅ 能 | ❌ 不能 |
| 能否控制遍历顺序 | ✅ 能（正序、倒序、跳步） | ❌ 不能 |
| 适用范围 | 数组 | 数组、List、Map 等可迭代对象 |

---

## 6. 灵活写法（了解）

```java
for (int i = 0; ; i++) { ... }   // 省略结束条件（需内部 break）
for (;;) { ... }                  // 等同于 while(true)
```

> 不推荐省略，除非特殊场景。

---

## 7. 练习

### 练习1：倒序输出数组

```java
int[] ns = { 1, 4, 9, 16, 25 };
for (int i = ns.length - 1; i >= 0; i--) {
    System.out.println(ns[i]);
}
// 输出：25, 16, 9, 4, 1
```

### 练习2：for each 求和

```java
int[] ns = { 1, 4, 9, 16, 25 };
int sum = 0;
for (int n : ns) {
    sum = sum + n;
}
System.out.println(sum);  // 55
```

### 练习3：计算圆周率

公式：`π/4 = 1 - 1/3 + 1/5 - 1/7 + 1/9 - ...`

```java
double pi = 0;
for (int i = 0; i < 1000000; i++) {
    if (i % 2 == 0) {
        pi += 1.0 / (2 * i + 1);
    } else {
        pi -= 1.0 / (2 * i + 1);
    }
}
pi = pi * 4;
System.out.println(pi);  // 3.1415916...
```

---

## 8. 关键总结

| 要点 | 说明 |
|------|------|
| 语法 | `for (初始化; 条件; 更新) { 循环体 }` |
| 最佳实践 | 计数器定义在 for 内，循环体内不修改计数器 |
| for each | `for (类型 变量 : 数组)` 简洁遍历，但无索引 |
| 数组遍历条件 | 用 `i < length`，不是 `i <= length` |
| 步长控制 | 在更新语句中设置，如 `i += 2` |
