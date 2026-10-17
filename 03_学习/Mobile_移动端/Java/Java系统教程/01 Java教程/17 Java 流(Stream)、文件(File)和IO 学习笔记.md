## 一、Java I/O 概述

### 1.1 基本概念

- **java.io 包**：包含所有操作输入、输出需要的类
- **流(Stream)**：数据的序列
    - **输入流**：从源读取数据
    - **输出流**：向目标写数据
- **支持格式**：基本类型、对象、本地化字符集等

---

## 二、控制台输入输出

### 2.1 读取控制台输入

#### 创建 BufferedReader

```java
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
```

#### 读取单个字符

```java
import java.io.*;

public class BRRead {
    public static void main(String args[]) throws IOException {
        char c;
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        System.out.println("输入字符, 按下 'q' 键退出.");
        
        do {
            c = (char) br.read();
            System.out.println(c);
        } while(c != 'q');
    }
}
```

**输出示例：**

```
输入字符, 按下 'q' 键退出.
123abcq
1
2
3
a
b
c
q
```

#### 读取字符串

```java
import java.io.*;

public class BRReadLines {
    public static void main(String args[]) throws IOException {
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String str;
        System.out.println("Enter lines of text.");
        System.out.println("Enter 'end' to quit.");
        
        do {
            str = br.readLine();
            System.out.println(str);
        } while(!str.equals("end"));
    }
}
```

### 2.2 控制台输出

#### 使用 write() 方法

```java
import java.io.*;

public class WriteDemo {
    public static void main(String args[]) {
        int b = 'A';
        System.out.write(b);
        System.out.write('\n');
    }
}
// 输出：A
```

**注意**：write() 方法不常用，print() 和 println() 更方便

---

## 三、文件读写

### 3.1 FileInputStream（文件输入流）

#### 创建方式

```java
// 方式1：使用文件名
InputStream f = new FileInputStream("C:/java/hello");

// 方式2：使用 File 对象
File f = new File("C:/java/hello");
InputStream in = new FileInputStream(f);
```

#### 常用方法

|方法|描述|返回值|
|---|---|---|
|`read(int r)`|读取指定字节的数据|整数值，结尾返回-1|
|`read(byte[] r)`|读取 r.length 长度的字节|读取的字节数|
|`available()`|返回可读取的字节数|整数值|
|`close()`|关闭流并释放资源|void|
|`finalize()`|清除与文件的连接|void|

### 3.2 FileOutputStream（文件输出流）

#### 创建方式

```java
// 方式1：使用文件名
OutputStream f = new FileOutputStream("C:/java/hello");

// 方式2：使用 File 对象
File f = new File("C:/java/hello");
OutputStream out = new FileOutputStream(f);
```

#### 常用方法

|方法|描述|
|---|---|
|`write(int w)`|写入指定字节|
|`write(byte[] w)`|写入字节数组|
|`close()`|关闭流并释放资源|
|`finalize()`|清除与文件的连接|

### 3.3 文件读写示例

#### 示例1：二进制读写

```java
import java.io.*;

public class fileStreamTest {
    public static void main(String args[]) {
        try {
            // 写入文件
            byte bWrite[] = {11, 21, 3, 40, 5};
            OutputStream os = new FileOutputStream("test.txt");
            for (int x = 0; x < bWrite.length; x++) {
                os.write(bWrite[x]);
            }
            os.close();
            
            // 读取文件
            InputStream is = new FileInputStream("test.txt");
            int size = is.available();
            for (int i = 0; i < size; i++) {
                System.out.print((char) is.read() + "  ");
            }
            is.close();
        } catch (IOException e) {
            System.out.print("Exception");
        }
    }
}
```

#### 示例2：文本读写（解决乱码）

```java
import java.io.*;

public class fileStreamTest2 {
    public static void main(String[] args) throws IOException {
        File f = new File("a.txt");
        
        // 写入文件
        FileOutputStream fop = new FileOutputStream(f);
        OutputStreamWriter writer = new OutputStreamWriter(fop, "UTF-8");
        
        writer.append("中文输入");
        writer.append("\r\n");
        writer.append("English");
        
        writer.close();
        fop.close();
        
        // 读取文件
        FileInputStream fip = new FileInputStream(f);
        InputStreamReader reader = new InputStreamReader(fip, "UTF-8");
        
        StringBuffer sb = new StringBuffer();
        while (reader.ready()) {
            sb.append((char) reader.read());
        }
        System.out.println(sb.toString());
        
        reader.close();
        fip.close();
    }
}
```

---

## 四、File 类操作

### 4.1 创建目录

```java
import java.io.File;

public class CreateDir {
    public static void main(String args[]) {
        String dirname = "/tmp/user/java/bin";
        File d = new File(dirname);
        
        // mkdir()：创建单个目录
        // mkdirs()：创建目录及所有父目录
        d.mkdirs();
    }
}
```

**区别：**

- `mkdir()`：只创建一个文件夹，父目录必须存在
- `mkdirs()`：创建文件夹及所有不存在的父文件夹

### 4.2 读取目录

```java
import java.io.File;

public class DirList {
    public static void main(String args[]) {
        String dirname = "/tmp";
        File f1 = new File(dirname);
        
        if (f1.isDirectory()) {
            System.out.println("Directory of " + dirname);
            String s[] = f1.list();
            
            for (int i = 0; i < s.length; i++) {
                File f = new File(dirname + "/" + s[i]);
                if (f.isDirectory()) {
                    System.out.println(s[i] + " 是一个目录");
                } else {
                    System.out.println(s[i] + " 是一个文件");
                }
            }
        } else {
            System.out.println(dirname + " 不是一个目录");
        }
    }
}
```

**输出示例：**

```
目录 /tmp
bin 是一个目录
lib 是一个目录
demo 是一个目录
test.txt 是一个文件
README 是一个文件
index.html 是一个文件
include 是一个目录
```

### 4.3 删除目录或文件

```java
import java.io.File;

public class DeleteFileDemo {
    public static void main(String args[]) {
        File folder = new File("/tmp/java/");
        deleteFolder(folder);
    }
    
    // 递归删除文件及目录
    public static void deleteFolder(File folder) {
        File[] files = folder.listFiles();
        if (files != null) {
            for (File f : files) {
                if (f.isDirectory()) {
                    deleteFolder(f);  // 递归删除子目录
                } else {
                    f.delete();       // 删除文件
                }
            }
        }
        folder.delete();  // 删除空目录
    }
}
```

**注意事项：**

- 删除目录时，必须保证目录为空
- 需要先删除目录中的所有文件和子目录
- 使用递归方式删除整个目录树

---

## 五、重要的 I/O 类

### 5.1 字节流

|类|用途|
|---|---|
|`InputStream`|字节输入流的抽象类|
|`OutputStream`|字节输出流的抽象类|
|`FileInputStream`|文件字节输入流|
|`FileOutputStream`|文件字节输出流|
|`ByteArrayInputStream`|字节数组输入流|
|`ByteArrayOutputStream`|字节数组输出流|
|`DataInputStream`|数据输入流|
|`DataOutputStream`|数据输出流|

### 5.2 字符流

|类|用途|
|---|---|
|`Reader`|字符输入流的抽象类|
|`Writer`|字符输出流的抽象类|
|`FileReader`|文件字符输入流|
|`FileWriter`|文件字符输出流|
|`InputStreamReader`|字节流转字符流（输入）|
|`OutputStreamWriter`|字节流转字符流（输出）|
|`BufferedReader`|缓冲字符输入流|
|`BufferedWriter`|缓冲字符输出流|

### 5.3 File 类常用方法

|方法|描述|
|---|---|
|`exists()`|判断文件或目录是否存在|
|`isFile()`|判断是否为文件|
|`isDirectory()`|判断是否为目录|
|`getName()`|获取文件名|
|`getPath()`|获取路径|
|`getAbsolutePath()`|获取绝对路径|
|`length()`|获取文件大小（字节）|
|`list()`|列出目录中的文件名|
|`listFiles()`|列出目录中的 File 对象|
|`createNewFile()`|创建新文件|
|`delete()`|删除文件或目录|
|`mkdir()`|创建目录|
|`mkdirs()`|创建多级目录|

---

## 六、最佳实践

### 6.1 资源管理

```java
// 推荐：使用 try-with-resources（Java 7+）
try (FileInputStream fis = new FileInputStream("file.txt");
     FileOutputStream fos = new FileOutputStream("output.txt")) {
    // 读写操作
} catch (IOException e) {
    e.printStackTrace();
}
// 自动关闭资源
```

### 6.2 编码处理

```java
// 明确指定编码，避免乱码
OutputStreamWriter writer = new OutputStreamWriter(
    new FileOutputStream("file.txt"), "UTF-8"
);

InputStreamReader reader = new InputStreamReader(
    new FileInputStream("file.txt"), "UTF-8"
);
```

### 6.3 路径分隔符

```java
// 跨平台路径处理
String path = "dir" + File.separator + "file.txt";

// 或使用 /（Java 会自动转换）
String path2 = "dir/file.txt";
```

---

## 七、总结对比

### 7.1 字节流 vs 字符流

|特性|字节流|字符流|
|---|---|---|
|基本单位|byte（8位）|char（16位）|
|适用场景|二进制文件（图片、音频）|文本文件|
|基类|InputStream/OutputStream|Reader/Writer|
|编码处理|不处理|自动处理字符编码|

### 7.2 常见操作模板

```java
// 1. 读取文本文件
BufferedReader br = new BufferedReader(
    new FileReader("file.txt")
);
String line;
while ((line = br.readLine()) != null) {
    System.out.println(line);
}
br.close();

// 2. 写入文本文件
BufferedWriter bw = new BufferedWriter(
    new FileWriter("file.txt")
);
bw.write("Hello World");
bw.newLine();
bw.close();

// 3. 复制文件
FileInputStream fis = new FileInputStream("source.txt");
FileOutputStream fos = new FileOutputStream("dest.txt");
byte[] buffer = new byte[1024];
int length;
while ((length = fis.read(buffer)) > 0) {
    fos.write(buffer, 0, length);
}
fis.close();
fos.close();
```

### 7.3 注意事项

1. **及时关闭流**：避免资源泄漏
2. **异常处理**：所有 I/O 操作都可能抛出 IOException
3. **编码一致**：读写文件时保持编码一致
4. **缓冲区**：使用 Buffered 类提高性能
5. **路径处理**：注意不同操作系统的路径分隔符