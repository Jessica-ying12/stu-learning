## 一、注释类型概述

### 1.1 Java 三种注释方式

| 注释类型     | 语法            | 说明         |
| -------- | ------------- | ---------- |
| **单行注释** | `// 注释内容`     | 单行注释       |
| **多行注释** | `/* 注释内容 */`  | 多行注释       |
| **文档注释** | `/** 注释内容 */` | 可生成 API 文档 |

### 1.2 文档注释的特点

- **开始**：`/**`
- **结束**：`*/`
- **工具**：javadoc 工具生成 HTML 文档
- **作用**：在程序中嵌入程序信息

## 二、javadoc 标签

### 2.1 常用标签总览

|标签|说明|使用位置|
|---|---|---|
|`@author`|作者信息|类、接口|
|`@version`|版本信息|类、接口|
|`@param`|参数说明|方法|
|`@return`|返回值说明|方法|
|`@throws` / `@exception`|异常说明|方法|
|`@see`|参考链接|类、方法、字段|
|`@since`|引入版本|类、方法、字段|
|`@deprecated`|过期标记|类、方法、字段|

### 2.2 详细标签说明

#### @author - 作者标签

```java
/**
 * @author Zara Ali
 * @author John Doe
 */
public class MyClass {
}
```

#### @version - 版本标签

```java
/**
 * @version 1.2
 * @version 2.0.1
 */
public class MyClass {
}
```

#### @param - 参数标签

```java
/**
 * @param num The value to be squared
 * @param name The user's name
 */
public void method(int num, String name) {
}
```

#### @return - 返回值标签

```java
/**
 * @return The squared value
 * @return true if successful, false otherwise
 */
public double square(double num) {
    return num * num;
}
```

#### @throws / @exception - 异常标签

```java
/**
 * @throws IOException If an input error occurs
 * @exception NullPointerException If parameter is null
 */
public void readFile() throws IOException {
}
```

#### @see - 参考标签

```java
/**
 * @see java.lang.String
 * @see #otherMethod()
 * @see <a href="http://example.com">Example</a>
 */
public void method() {
}
```

#### @since - 引入版本标签

```java
/**
 * @since 1.5
 * @since JDK 1.8
 */
public void newMethod() {
}
```

#### @deprecated - 过期标签

```java
/**
 * @deprecated Use newMethod() instead
 * @deprecated This method is obsolete
 */
public void oldMethod() {
}
```

### 2.3 内联标签

|标签|说明|
|---|---|
|`{@link}`|插入链接|
|`{@linkplain}`|插入纯文本链接|
|`{@code}`|代码文本|
|`{@literal}`|字面文本|
|`{@value}`|显示常量值|
|`{@docRoot}`|文档根目录路径|
|`{@inheritDoc}`|继承父类注释|

#### 内联标签示例

```java
/**
 * 使用 {@link String} 类处理文本
 * 调用 {@link #calculate(int, int)} 方法
 * 常量值为 {@value MAX_SIZE}
 * 代码示例：{@code int x = 10;}
 */
public class Example {
    public static final int MAX_SIZE = 100;
}
```

## 三、文档注释格式

### 3.1 基本格式

```java
/**
 * 第一行：简短描述（摘要）
 * 
 * 详细描述可以有多行
 * 可以包含多个段落
 * 
 * @tag1 说明1
 * @tag2 说明2
 */
```

### 3.2 标签顺序建议

```java
/**
 * 类或方法的描述
 * 
 * @author 作者
 * @version 版本
 * @param 参数
 * @return 返回值
 * @throws 异常
 * @see 参考
 * @since 引入版本
 * @deprecated 过期说明
 */
```

## 四、完整示例

### 4.1 类文档注释

```java
import java.io.*;

/**
 * This class demonstrates documentation comments.
 * 这个类演示了文档注释的使用。
 * 
 * @author Ayan Amhed
 * @version 1.2
 * @since 1.0
 */
public class SquareNum {
    
    /**
     * This method returns the square of num.
     * This is a multiline description. You can use
     * as many lines as you like.
     * 
     * @param num The value to be squared.
     * @return num squared.
     */
    public double square(double num) {
        return num * num;
    }
    
    /**
     * This method inputs a number from the user.
     * 
     * @return The value input as a double.
     * @throws IOException On input error.
     * @see IOException
     */
    public double getNumber() throws IOException {
        InputStreamReader isr = new InputStreamReader(System.in);
        BufferedReader inData = new BufferedReader(isr);
        String str;
        str = inData.readLine();
        return (new Double(str)).doubleValue();
    }
    
    /**
     * This method demonstrates square().
     * 
     * @param args Unused.
     * @throws IOException On input error.
     * @see IOException
     */
    public static void main(String args[]) throws IOException {
        SquareNum ob = new SquareNum();
        double val;
        System.out.println("Enter value to be squared: ");
        val = ob.getNumber();
        val = ob.square(val);
        System.out.println("Squared value is " + val);
    }
}
```

### 4.2 更复杂的示例

```java
/**
 * 用户管理类
 * 提供用户的增删改查功能
 * 
 * <p>使用示例：
 * <pre>
 * UserManager manager = new UserManager();
 * User user = manager.createUser("张三", 25);
 * </pre>
 * 
 * @author 张三
 * @author 李四
 * @version 2.0
 * @since 1.0
 * @see User
 */
public class UserManager {
    
    /** 最大用户数量 */
    public static final int MAX_USERS = 1000;
    
    /**
     * 创建新用户
     * 
     * @param name 用户名，不能为空
     * @param age 年龄，必须大于0
     * @return 创建的用户对象
     * @throws IllegalArgumentException 如果参数无效
     * @throws UserLimitException 如果超过最大用户数
     * @see User
     * @since 1.0
     */
    public User createUser(String name, int age) 
            throws IllegalArgumentException, UserLimitException {
        if (name == null || name.isEmpty()) {
            throw new IllegalArgumentException("用户名不能为空");
        }
        if (age <= 0) {
            throw new IllegalArgumentException("年龄必须大于0");
        }
        return new User(name, age);
    }
    
    /**
     * 根据 ID 查找用户
     * 
     * @param id 用户 ID
     * @return 找到的用户对象，如果不存在返回 null
     * @see #findUserByName(String)
     * @since 1.0
     */
    public User findUserById(int id) {
        // 实现代码
        return null;
    }
    
    /**
     * 根据名称查找用户
     * 
     * @param name 用户名
     * @return 找到的用户对象，如果不存在返回 null
     * @see #findUserById(int)
     * @since 1.0
     */
    public User findUserByName(String name) {
        // 实现代码
        return null;
    }
    
    /**
     * 删除用户
     * 
     * @param id 要删除的用户 ID
     * @return true 如果删除成功，false 如果用户不存在
     * @deprecated 使用 {@link #removeUser(int)} 代替
     * @since 1.0
     */
    @Deprecated
    public boolean deleteUser(int id) {
        return removeUser(id);
    }
    
    /**
     * 移除用户
     * 
     * @param id 要移除的用户 ID
     * @return true 如果移除成功，false 如果用户不存在
     * @since 2.0
     */
    public boolean removeUser(int id) {
        // 实现代码
        return false;
    }
}
```

## 五、生成 javadoc 文档

### 5.1 命令行生成

```bash
# 基本用法
$ javadoc SquareNum.java

# 指定输出目录
$ javadoc -d docs SquareNum.java

# 生成多个文件
$ javadoc -d docs *.java

# 指定编码
$ javadoc -encoding UTF-8 -charset UTF-8 SquareNum.java

# 包含私有成员
$ javadoc -private -d docs SquareNum.java
```

### 5.2 javadoc 常用选项

|选项|说明|
|---|---|
|`-d <directory>`|指定输出目录|
|`-author`|包含 @author 信息|
|`-version`|包含 @version 信息|
|`-private`|显示所有类和成员|
|`-protected`|显示 protected 和 public|
|`-public`|只显示 public|
|`-encoding <name>`|源文件编码|
|`-charset <charset>`|输出文件编码|
|`-windowtitle <text>`|浏览器窗口标题|
|`-doctitle <html-code>`|文档标题|

### 5.3 生成输出示例

```bash
$ javadoc SquareNum.java
Loading source file SquareNum.java...
Constructing Javadoc information...
Standard Doclet version 1.8.0
Building tree for all the packages and classes...
Generating SquareNum.html...
Generating package-frame.html...
Generating package-summary.html...
Generating package-tree.html...
Generating constant-values.html...
Building index for all the packages and classes...
Generating overview-tree.html...
Generating index-all.html...
Generating deprecated-list.html...
Building index for all classes...
Generating allclasses-frame.html...
Generating allclasses-noframe.html...
Generating index.html...
Generating help-doc.html...
Generating stylesheet.css...
```

### 5.4 生成的文件结构

```
docs/
├── index.html              # 主页
├── overview-tree.html      # 类层次结构
├── allclasses-frame.html   # 所有类列表
├── deprecated-list.html    # 过期列表
├── index-all.html          # 索引
├── help-doc.html           # 帮助文档
├── stylesheet.css          # 样式表
└── SquareNum.html          # 类文档
```

## 六、IDE 中使用 javadoc

### 6.1 Eclipse

```
1. 选择项目
2. Project → Generate Javadoc
3. 配置选项
4. 点击 Finish
```

### 6.2 IntelliJ IDEA

```
1. Tools → Generate JavaDoc
2. 配置输出目录和选项
3. 点击 OK
```

### 6.3 NetBeans

```
1. 右键项目
2. Generate Javadoc
3. 配置选项
```

## 七、最佳实践

### 7.1 编写规范

```java
/**
 * ✅ 好的文档注释
 * 
 * 1. 简洁明了的描述
 * 2. 完整的参数说明
 * 3. 清晰的返回值说明
 * 4. 列出所有可能的异常
 * 5. 提供使用示例
 * 
 * @param input 输入参数的详细说明
 * @return 返回值的详细说明
 * @throws Exception 异常的详细说明
 */
public String goodMethod(String input) throws Exception {
    return input;
}

/**
 * ❌ 不好的文档注释
 * 
 * @param s
 * @return
 */
public String badMethod(String s) {
    return s;
}
```

### 7.2 注释模板

```java
/**
 * ${类的简短描述}
 * 
 * <p>${详细描述}
 * 
 * <p>使用示例：
 * <pre>
 * ${示例代码}
 * </pre>
 * 
 * @author ${作者}
 * @version ${版本}
 * @since ${引入版本}
 * @see ${相关类}
 */
public class Template {
    
    /**
     * ${方法的简短描述}
     * 
     * <p>${详细描述}
     * 
     * @param ${参数名} ${参数说明}
     * @return ${返回值说明}
     * @throws ${异常类} ${异常说明}
     * @see ${相关方法}
     * @since ${引入版本}
     */
    public void method(String param) throws Exception {
    }
}
```

### 7.3 HTML 标签使用

```java
/**
 * 支持的 HTML 标签：
 * 
 * <p>段落标签</p>
 * <br>换行标签
 * <b>粗体</b>
 * <i>斜体</i>
 * <code>代码</code>
 * <pre>预格式化文本</pre>
 * <ul>
 *   <li>列表项1</li>
 *   <li>列表项2</li>
 * </ul>
 * <a href="url">链接</a>
 * <table>表格</table>
 */
public class HtmlExample {
}
```

## 八、总结

### 8.1 核心要点

|要点|说明|
|---|---|
|**文档注释**|`/** ... */`|
|**javadoc 工具**|生成 HTML 文档|
|**常用标签**|@author, @param, @return, @throws|
|**标签位置**|必须在新行开始|
|**生成命令**|`javadoc ClassName.java`|

### 8.2 注释层次

```
1. 类级别注释
   - @author
   - @version
   - @since
   
2. 方法级别注释
   - @param
   - @return
   - @throws
   
3. 字段级别注释
   - 简短描述
   - @see
```

### 8.3 记忆口诀

- **三种注释方式**：单行、多行、文档
- **文档注释格式**：/** 开始 */ 结束
- **常用标签记忆**：作者版本参数返回异常
- **javadoc 生成**：命令行或 IDE 工具
- **输出 HTML 文档**：方便查看和分享

### 8.4 注意事项

1. **完整性**：确保所有公共 API 都有文档
2. **准确性**：文档要与代码保持同步
3. **清晰性**：使用简洁明了的语言
4. **示例**：提供使用示例
5. **更新**：代码修改时同步更新文档