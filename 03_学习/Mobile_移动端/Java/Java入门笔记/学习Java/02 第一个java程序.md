## 1. 代码结构解析
编写 Java 程序时，必须遵循特定的语法规则。以下是一个最简单的“Hello World”程序：

```java
public class Hello {
    public static void main(String[] args) {
        System.out.println("Hello, world!");
    }
}
```

### 核心组成部分：
*   **`public class Hello`**：定义一个公开的类，类名为 `Hello`。
    *   **规则**：类名首字母大写；文件名必须与类名完全一致（即 `Hello.java`），且大小写敏感。
*   **`public static void main(String[] args)`**：Java 程序的**固定入口方法**。
    *   程序总是从这里开始执行。
*   **`System.out.println(...)`**：标准输出语句，用于在屏幕上打印内容。
*   **语法规范**：
    *   代码块由 `{}` 包裹。
    *   每行代码必须以 `;` 结尾。
    *   缩进（4个空格或1个Tab）虽非强制，但强烈建议使用以提升可读性。

---

## 2. Java 程序的运行流程

Java 程序运行分为两个阶段：**编译**与**执行**。

### 阶段一：编译 (Compile)
使用 `javac` 工具将源代码文件转换为字节码文件。
*   **命令**：`javac Hello.java`
*   **结果**：生成 `Hello.class` 文件（字节码）。

### 阶段二：运行 (Execute)
使用 `java` 工具启动虚拟机（JVM）并加载字节码。
*   **命令**：`java Hello`（注意：此处不需要加 `.class` 后缀）。

> **原理图示：**
> `Hello.java` (源码)  ——(javac)→ `Hello.class` (字节码) ——(java)→ **JVM 执行**

---

## 3. 常见问题与进阶技巧

### 常见报错：`ClassNotFoundException`
*   **原因**：JVM 在当前目录下找不到指定的类文件。
*   **解决方法**：确保当前工作目录包含 `Hello.class` 文件，并确认执行命令时类名拼写正确。

### 进阶：Java 11+ 的单文件运行
从 Java 11 开始，对于不依赖第三方库的单文件程序，可以直接运行源码，无需显式编译：
*   **命令**：`java Hello.java`

> **教学提示**：在实际企业级开发中，项目通常依赖大量第三方库，因此“编译-打包-运行”是标准流程，直接运行源码仅适用于简单的脚本测试。

---

## 4. 学习小结（教学重点）
1.  **文件一致性**：一个 `.java` 文件只能有一个 `public class`，且名称必须与文件名完全匹配。
2.  **入口唯一**：`main` 方法是程序的起点，签名缺一不可。
3.  **工具区分**：`javac` 是编译器（生成字节码），`java` 是虚拟机（执行字节码）。