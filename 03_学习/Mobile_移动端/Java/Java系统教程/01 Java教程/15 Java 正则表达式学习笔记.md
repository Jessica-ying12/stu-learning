## 一、概述

正则表达式定义了字符串的模式，可用于搜索、编辑或处理文本。Java 正则表达式与 Perl 最为相似。

## 二、核心类（java.util.regex 包）

### 1. Pattern 类

- 正则表达式的编译表示
- 无公共构造方法
- 通过静态方法 `compile()` 创建对象

```java
Pattern pattern = Pattern.compile("正则表达式");
```

### 2. Matcher 类

- 对输入字符串进行解释和匹配的引擎
- 无公共构造方法
- 通过 Pattern 对象的 `matcher()` 方法获取

```java
Matcher matcher = pattern.matcher("输入字符串");
```

### 3. PatternSyntaxException 类

- 非强制异常类
- 表示正则表达式模式中的语法错误

## 三、捕获组

### 概念

将多个字符作为单独单元处理，通过括号分组创建。

### 编号规则

从左至右计算开括号来编号。

### 示例

```java
((A)(B(C)))  // 有 4 个捕获组：
// 组 0: ((A)(B(C)))  - 整个表达式（特殊组，不计入 groupCount）
// 组 1: ((A)(B(C)))
// 组 2: (A)
// 组 3: (B(C))
// 组 4: (C)
```

### 基础示例

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegexMatches {
    public static void main(String args[]) {
        String line = "This order was placed for QT3000! OK?";
        String pattern = "(.*)(\\d+)(.*)";
        
        Pattern r = Pattern.compile(pattern);
        Matcher m = r.matcher(line);
        
        if (m.find()) {
            System.out.println("Found value: " + m.group(0));  // 整个匹配
            System.out.println("Found value: " + m.group(1));  // 第一组
            System.out.println("Found value: " + m.group(2));  // 第二组
        } else {
            System.out.println("NO MATCH");
        }
    }
}
```

## 四、正则表达式语法

### 基本字符

|字符|说明|
|---|---|
|`\`|转义字符|
|`^`|匹配输入字符串开始位置|
|`$`|匹配输入字符串结尾位置|
|`.`|匹配除 `\r\n` 外的任何单个字符|

### 量词

|字符|说明|
|---|---|
|`*`|零次或多次匹配（等效于 `{0,}`）|
|`+`|一次或多次匹配（等效于 `{1,}`）|
|`?`|零次或一次匹配（等效于 `{0,1}`）|
|`{n}`|正好匹配 n 次|
|`{n,}`|至少匹配 n 次|
|`{n,m}`|匹配至少 n 次，至多 m 次|

### 贪婪与非贪婪

|模式|说明|
|---|---|
|`o+`|贪婪模式，匹配尽可能长的字符串|
|`o+?`|非贪婪模式，匹配尽可能短的字符串|

### 分组与捕获

|语法|说明|
|---|---|
|`(pattern)`|捕获匹配的子表达式|
|`(?:pattern)`|非捕获匹配|
|`(?=pattern)`|正向预测先行（不占用字符）|
|`(?!pattern)`|反向预测先行（不占用字符）|

### 字符类

|语法|说明|
|---|---|
|`[xyz]`|字符集，匹配任一字符|
|`[^xyz]`|反向字符集，匹配未包含的字符|
|`[a-z]`|字符范围|
|`[^a-z]`|反向范围|
|`x|y`|

### 预定义字符类

|字符|说明|
|---|---|
|`\d`|数字字符，等效于 `[0-9]`|
|`\D`|非数字字符，等效于 `[^0-9]`|
|`\s`|空白字符，等效于 `[ \f\n\r\t\v]`|
|`\S`|非空白字符|
|`\w`|字类字符（含下划线），等效于 `[A-Za-z0-9_]`|
|`\W`|非单词字符|

### 边界匹配

|字符|说明|
|---|---|
|`\b`|字边界（字与空格间的位置）|
|`\B`|非字边界|

### 特殊字符

|字符|说明|
|---|---|
|`\n`|换行符|
|`\r`|回车符|
|`\t`|制表符|
|`\f`|换页符|
|`\v`|垂直制表符|

## 五、Matcher 类方法

### 1. 索引方法

|方法|说明|
|---|---|
|`int start()`|返回匹配的初始索引|
|`int start(int group)`|返回指定组的初始索引|
|`int end()`|返回最后匹配字符之后的偏移量|
|`int end(int group)`|返回指定组最后字符之后的偏移量|

**示例：**

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegexMatches {
    private static final String REGEX = "\\bcat\\b";
    private static final String INPUT = "cat cat cat cattie cat";
    
    public static void main(String args[]) {
        Pattern p = Pattern.compile(REGEX);
        Matcher m = p.matcher(INPUT);
        int count = 0;
        
        while(m.find()) {
            count++;
            System.out.println("Match number " + count);
            System.out.println("start(): " + m.start());
            System.out.println("end(): " + m.end());
        }
    }
}
```

### 2. 研究方法

|方法|说明|
|---|---|
|`boolean lookingAt()`|从区域开头尝试匹配|
|`boolean find()`|查找下一个匹配的子序列|
|`boolean find(int start)`|从指定索引开始查找|
|`boolean matches()`|尝试匹配整个区域|

**matches vs lookingAt：**

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegexMatches {
    private static final String REGEX = "foo";
    private static final String INPUT = "fooooooooooooooooo";
    
    public static void main(String args[]) {
        Pattern pattern = Pattern.compile(REGEX);
        Matcher matcher = pattern.matcher(INPUT);
        
        System.out.println("lookingAt(): " + matcher.lookingAt());  // true
        System.out.println("matches(): " + matcher.matches());      // false
    }
}
```

### 3. 替换方法

|方法|说明|
|---|---|
|`String replaceFirst(String replacement)`|替换首次匹配|
|`String replaceAll(String replacement)`|替换所有匹配|
|`Matcher appendReplacement(StringBuffer sb, String replacement)`|非终端添加和替换|
|`StringBuffer appendTail(StringBuffer sb)`|终端添加和替换|
|`static String quoteReplacement(String s)`|返回字面替换字符串|

**replaceAll 示例：**

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegexMatches {
    private static String REGEX = "dog";
    private static String INPUT = "The dog says meow. All dogs say meow.";
    private static String REPLACE = "cat";
    
    public static void main(String[] args) {
        Pattern p = Pattern.compile(REGEX);
        Matcher m = p.matcher(INPUT);
        INPUT = m.replaceAll(REPLACE);
        System.out.println(INPUT);
        // 输出: The cat says meow. All cats say meow.
    }
}
```

**appendReplacement 示例：**

```java
import java.util.regex.Matcher;
import java.util.regex.Pattern;

public class RegexMatches {
    private static String REGEX = "a*b";
    private static String INPUT = "aabfooaabfooabfoob";
    private static String REPLACE = "-";
    
    public static void main(String[] args) {
        Pattern p = Pattern.compile(REGEX);
        Matcher m = p.matcher(INPUT);
        StringBuffer sb = new StringBuffer();
        
        while(m.find()) {
            m.appendReplacement(sb, REPLACE);
        }
        m.appendTail(sb);
        System.out.println(sb.toString());
        // 输出: -foo-foo-foo-
    }
}
```

## 六、PatternSyntaxException 类方法

|方法|说明|
|---|---|
|`String getDescription()`|获取错误描述|
|`int getIndex()`|获取错误索引|
|`String getPattern()`|获取错误的正则表达式模式|
|`String getMessage()`|返回包含错误详细信息的多行字符串|

## 七、常用正则表达式示例

```java
// 匹配邮箱
String emailRegex = "^[A-Za-z0-9+_.-]+@[A-Za-z0-9.-]+$";

// 匹配手机号（中国）
String phoneRegex = "^1[3-9]\\d{9}$";

// 匹配 URL
String urlRegex = "^(https?|ftp)://[^\\s/$.?#].[^\\s]*$";

// 匹配 IP 地址
String ipRegex = "^((25[0-5]|2[0-4]\\d|[01]?\\d\\d?)\\.){3}(25[0-5]|2[0-4]\\d|[01]?\\d\\d?)$";

// 匹配日期（yyyy-MM-dd）
String dateRegex = "^\\d{4}-(0[1-9]|1[0-2])-(0[1-9]|[12]\\d|3[01])$";
```

## 八、注意事项

1. **转义字符**：在 Java 字符串中使用正则表达式时，需要双重转义（如 `\\d` 表示 `\d`）
2. **性能考虑**：复杂的正则表达式可能影响性能，应避免过度使用
3. **贪婪匹配**：默认是贪婪模式，需要时使用 `?` 转为非贪婪
4. **组编号**：组 0 始终代表整个表达式
5. **边界匹配**：使用 `\b` 确保完整单词匹配，避免部分匹配