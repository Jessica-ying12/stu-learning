# JavaBean 学习笔记

## 1. 什么是 JavaBean

符合特定命名规范的 class：private 字段 + public getter/setter 方法。

```java
public class Person {
    private String name;
    private int age;

    public String getName() { return this.name; }
    public void setName(String name) { this.name = name; }

    public int getAge() { return this.age; }
    public void setAge(int age) { this.age = age; }
}
```

---

## 2. 命名规范

| 字段类型 | getter 方法 | setter 方法 |
|----------|------------|------------|
| 普通类型 | `getXyz()` | `setXyz(Type value)` |
| boolean | `isXyz()` | `setXyz(boolean value)` |

- 方法名以 get/set/is 开头，后接**首字母大写**的属性名
- 如字段 `name` → `getName()` / `setName()`

---

## 3. 属性（property）

一组对应的 getter 和 setter 称为一个**属性**：

| 属性类型 | 说明 |
|----------|------|
| 读写属性 | 有 getter + setter |
| 只读属性 | 只有 getter，无 setter |
| 只写属性 | 只有 setter，无 getter（少见） |

### 属性不一定对应字段

```java
public boolean isChild() {
    return age <= 6;  // 没有 child 字段，但有 child 只读属性
}
```

---

## 4. JavaBean 的作用

| 用途 | 说明 |
|------|------|
| 数据传递 | 把一组数据组合成对象便于传输 |
| IDE 支持 | 自动生成 getter/setter，可视化设计 |
| 框架集成 | Spring、Hibernate 等框架依赖 JavaBean 规范 |

### IDE 快速生成

Eclipse：右键 → Source → Generate Getters and Setters

---

## 5. 枚举 JavaBean 属性（Introspector）

```java
import java.beans.*;

BeanInfo info = Introspector.getBeanInfo(Person.class);
for (PropertyDescriptor pd : info.getPropertyDescriptors()) {
    System.out.println(pd.getName());
    System.out.println("  读: " + pd.getReadMethod());
    System.out.println("  写: " + pd.getWriteMethod());
}
```

> 注意：会输出一个 `class` 属性，来自 Object 的 `getClass()` 方法。

---

## 6. 关键总结

| 要点 | 说明 |
|------|------|
| 定义 | private 字段 + public getter/setter |
| 命名 | getXyz / setXyz / isXyz（boolean） |
| 属性 | 由方法定义，不一定对应字段 |
| 本质 | 一种数据封装的规范，不是语法强制 |
| 工具支持 | IDE 生成、Introspector 枚举、框架集成 |
