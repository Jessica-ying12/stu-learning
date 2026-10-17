## 1. 什么是 IDE

**IDE（Integrated Development Environment）** 是集成开发环境。

### IDE 的作用
IDE 把以下开发过程集中到一个界面中：
- 编写代码
- 管理项目
- 编译程序
- 运行程序
- 调试程序

### IDE 的优势
- **自动提示代码**：输入更快，减少拼写错误
- **自动编译与运行**：修改后可快速验证结果
- **断点调试方便**：更容易定位问题

---

## 2. 常见 Java IDE

Java 常用的 IDE 主要有：

### Eclipse
- 由 IBM 开发并捐赠给开源社区
- 免费、功能完整
- 基于插件结构
- 适合 Java、C/C++、PHP、Rust 等开发

### IntelliJ IDEA
- 由 JetBrains 开发
- 功能强大
- 分为免费版和付费版
- 是目前非常流行的 Java 开发工具

### NetBeans
- 早期由 SUN 开发
- 现在使用人数较少，已不算主流

---

## 3. 为什么建议用 IDE 学习 Java

使用 IDE 学习 Java 有以下好处：
- 不需要手动记太多命令
- 更容易创建项目结构
- 更容易发现语法错误
- 更适合后续学习框架和工程开发

> 你已经下载了 **IntelliJ IDEA**，完全可以直接用它来学习 Java，不必强制使用 Eclipse。

---

## 4. IntelliJ IDEA 中创建第一个 Java 程序

下面以 IntelliJ IDEA 为例，演示如何创建并运行第一个 Java 程序。

### 4.1 新建项目
打开 IntelliJ IDEA 后：

1. 点击 **New Project**
2. 选择 **Java**
3. 选择已安装的 **JDK**
4. 设置项目名称，例如：`HelloWorld`
5. 点击 **Create**

---

### 4.2 新建类
在项目的 `src` 目录下：

1. 右键 `src`
2. 选择 **New -> Java Class**
3. 输入类名：`Hello`

IDE 会自动创建 `Hello.java` 文件。

---

### 4.3 编写代码
在 `Hello.java` 中输入：

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, world!");
    }
}
```

---

### 4.4 运行程序
运行方式通常有两种：

#### 方法一：点击运行按钮
- 在 `main` 方法左侧点击绿色运行箭头
- 或点击右上角的 **Run**

#### 方法二：右键运行
- 右键 `Hello.java`
- 选择 **Run 'Hello.main()'**

运行后，在控制台输出：

```text
Hello, world!
```

---

## 5. 代码结构回顾

```java
public class Hello {
```
- 定义一个类
- `public` 表示公开
- `class` 是关键字
- `Hello` 是类名

```java
public static void main(String[] args) {
```
- Java 程序入口方法
- 程序从这里开始执行
- `String[] args` 是命令行参数

```java
System.out.println("Hello, world!");
```
- 向控制台输出一行文本

```java
}
```
- 花括号表示代码块的开始和结束

---

## 6. 使用 IDE 时要注意什么

### 6.1 项目结构要清楚
常见结构：
- `src`：源码目录
- `out` 或 `target`：编译输出目录

### 6.2 类名与文件名一致
例如：
- 类名：`Hello`
- 文件名：`Hello.java`

### 6.3 一个初学者只需关注 main 方法
先理解：
- 类是什么
- 方法是什么
- `main` 为什么是入口

---

## 7. 与命令行方式的区别

### 命令行方式
需要手动执行：
```bash
javac Hello.java
java Hello
```

### IDE 方式
IDE 帮你完成：
- 编译
- 运行
- 错误提示
- 自动补全

> 对初学者来说，IDE 更适合入门；命令行更适合理解底层原理。

---

## 8. 学习建议

### 初学阶段建议
1. 先学会在 IDEA 中创建项目
2. 学会创建 Java 类
3. 学会写 `main` 方法
4. 学会运行程序
5. 再逐步学习变量、条件、循环、数组、面向对象

### 不要一开始就纠结
- 配置太多复杂选项
- 各种高级插件
- 深入调试工具

先能写、能运行、能看懂输出最重要。

---

## 9. 本章小结

### IDE 是什么？
IDE 是集成开发环境，能提高 Java 开发效率。[1][2][3]

### Java 常见 IDE
- Eclipse
- IntelliJ IDEA
- NetBeans

### 用 IDEA 写第一个 Java 程序的步骤
1. 创建项目
2. 创建类
3. 编写 `main` 方法
4. 运行程序
5. 查看控制台输出

---

## 10. 练习题

### 练习 1
用 IDEA 创建一个 `Hello` 类，输出：

```text
Hello, Java!
```

### 练习 2
把输出内容改成你的名字，例如：

```text
Hello, Tom!
```

### 练习 3
尝试修改 `println` 里的内容，观察控制台输出变化。

---

## 参考来源

- [1] https://baike.baidu.com/item/%E4%BD%BF%E7%94%A8/7741550
- [2] https://www.hanyuguoxue.com/cidian/ci-a2f4edadb
- [3] https://www.zdic.net/hans/%E4%BD%BF%E7%94%A8

如果你愿意，我下一步可以继续帮你整理成：

1. **“IntelliJ IDEA 安装与配置教程”**
2. **“Java 基础第 1 课完整讲义”**
3. **“带练习答案的教材版”**