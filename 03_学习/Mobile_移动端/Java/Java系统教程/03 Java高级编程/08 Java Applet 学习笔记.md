## 一、Applet 概述

### 1.1 什么是 Applet？

- **定义**：运行在支持 Java 的 Web 浏览器内的 Java 程序
- **特点**：有完整的 Java API 支持，是全功能的 Java 应用程序
- **运行环境**：嵌入在 HTML 页面中

### 1.2 Applet 与应用程序的区别

|特性|Java 应用程序|Applet|
|---|---|---|
|**继承**|无特定要求|继承 `java.applet.Applet` 类|
|**main() 方法**|必须有|没有 main() 方法|
|**运行方式**|独立运行|嵌入在 HTML 页面中|
|**启动方式**|java 命令|浏览器或 appletviewer|
|**安全限制**|较少|沙箱安全机制|

### 1.3 Applet 的特点

|特点|说明|
|---|---|
|**下载执行**|代码下载到用户机器上执行|
|**需要 JVM**|浏览器插件或独立运行时环境|
|**安全限制**|沙箱安全机制|
|**JAR 打包**|可以用 JAR 文件打包|

## 二、Applet 生命周期

### 2.1 生命周期方法

|方法|调用时机|说明|
|---|---|---|
|`init()`|Applet 首次加载|初始化操作，只调用一次|
|`start()`|Applet 启动或重新访问|可以被多次调用|
|`stop()`|用户离开页面|可以被多次调用|
|`destroy()`|浏览器正常关闭|清理资源，只调用一次|
|`paint()`|需要重绘时|绘制 Applet 界面|

### 2.2 生命周期流程

```
加载 Applet
    ↓
init() - 初始化
    ↓
start() - 开始运行
    ↓
paint() - 绘制界面
    ↓
stop() - 停止运行（用户离开页面）
    ↓
start() - 重新开始（用户返回页面）
    ↓
destroy() - 销毁（浏览器关闭）
```

### 2.3 生命周期示例

```java
import java.applet.*;
import java.awt.*;

public class LifeCycleDemo extends Applet {
    
    @Override
    public void init() {
        System.out.println("init() 方法被调用");
        // 初始化代码
    }
    
    @Override
    public void start() {
        System.out.println("start() 方法被调用");
        // 启动代码
    }
    
    @Override
    public void stop() {
        System.out.println("stop() 方法被调用");
        // 停止代码
    }
    
    @Override
    public void destroy() {
        System.out.println("destroy() 方法被调用");
        // 清理代码
    }
    
    @Override
    public void paint(Graphics g) {
        System.out.println("paint() 方法被调用");
        g.drawString("Applet 生命周期演示", 20, 20);
    }
}
```

## 三、Hello World Applet

### 3.1 基本示例

```java
import java.applet.*;
import java.awt.*;

public class HelloWorldApplet extends Applet {
    public void paint(Graphics g) {
        g.drawString("Hello World", 25, 50);
    }
}
```

### 3.2 必要的 import 语句

```java
import java.applet.*;  // Applet 类
import java.awt.*;     // Graphics 类
```

### 3.3 HTML 调用

```html
<html>
<title>The Hello, World Applet</title>
<hr>
<applet code="HelloWorldApplet.class" width="320" height="120">
    If your browser was Java-enabled, a "Hello, World" 
    message would appear here.
</applet>
<hr>
</html>
```

## 四、Applet 标签

### 4.1 基本语法

```html
<applet code="类名.class" width="宽度" height="高度">
    不支持 Java 的浏览器显示的文本
</applet>
```

### 4.2 常用属性

|属性|说明|
|---|---|
|`code`|Applet 类文件名|
|`width`|Applet 宽度（像素）|
|`height`|Applet 高度（像素）|
|`codebase`|Applet 代码所在目录|
|`archive`|JAR 文件名|
|`name`|Applet 实例名称|

### 4.3 指定代码路径

```html
<!-- 指定代码库路径 -->
<applet codebase="http://example.com/applets" 
        code="HelloWorldApplet.class" 
        width="320" 
        height="120">
</applet>

<!-- 指定包名 -->
<applet code="mypackage.subpackage.TestApplet.class" 
        width="320" 
        height="120">
</applet>
```

## 五、Applet 参数

### 5.1 传递参数

```html
<applet code="CheckerApplet.class" width="480" height="320">
    <param name="color" value="blue">
    <param name="squaresize" value="30">
</applet>
```

### 5.2 获取参数

```java
import java.applet.*;
import java.awt.*;

public class CheckerApplet extends Applet {
    int squareSize = 50;  // 默认值
    
    public void init() {
        // 获取参数
        String squareSizeParam = getParameter("squareSize");
        parseSquareSize(squareSizeParam);
        
        String colorParam = getParameter("color");
        Color fg = parseColor(colorParam);
        
        setBackground(Color.black);
        setForeground(fg);
    }
    
    private void parseSquareSize(String param) {
        if (param == null) return;
        try {
            squareSize = Integer.parseInt(param);
        } catch (Exception e) {
            // 保留默认值
        }
    }
    
    private Color parseColor(String param) {
        if (param == null) return Color.white;
        
        if (param.equals("red")) return Color.red;
        if (param.equals("blue")) return Color.blue;
        if (param.equals("green")) return Color.green;
        
        return Color.white;
    }
    
    public void paint(Graphics g) {
        // 绘制棋盘
        for (int row = 0; row < 8; row++) {
            for (int col = 0; col < 8; col++) {
                if ((row + col) % 2 == 0) {
                    g.fillRect(col * squareSize, row * squareSize, 
                              squareSize, squareSize);
                }
            }
        }
    }
}
```

## 六、事件处理

### 6.1 鼠标事件示例

```java
import java.awt.event.MouseListener;
import java.awt.event.MouseEvent;
import java.applet.Applet;
import java.awt.Graphics;

public class ExampleEventHandling extends Applet 
                                  implements MouseListener {
    StringBuffer strBuffer;
    
    public void init() {
        addMouseListener(this);
        strBuffer = new StringBuffer();
        addItem("initializing the applet ");
    }
    
    public void start() {
        addItem("starting the applet ");
    }
    
    public void stop() {
        addItem("stopping the applet ");
    }
    
    public void destroy() {
        addItem("unloading the applet");
    }
    
    void addItem(String word) {
        System.out.println(word);
        strBuffer.append(word);
        repaint();
    }
    
    public void paint(Graphics g) {
        // 绘制矩形边框
        g.drawRect(0, 0, getWidth() - 1, getHeight() - 1);
        
        // 显示字符串
        g.drawString(strBuffer.toString(), 10, 20);
    }
    
    // MouseListener 接口方法
    public void mouseEntered(MouseEvent event) {}
    public void mouseExited(MouseEvent event) {}
    public void mousePressed(MouseEvent event) {}
    public void mouseReleased(MouseEvent event) {}
    
    public void mouseClicked(MouseEvent event) {
        addItem("mouse clicked! ");
    }
}
```

### 6.2 HTML 调用

```html
<html>
<title>Event Handling</title>
<hr>
<applet code="ExampleEventHandling.class" width="300" height="300">
</applet>
<hr>
</html>
```

## 七、显示图片

### 7.1 完整示例

```java
import java.applet.*;
import java.awt.*;
import java.net.*;

public class ImageDemo extends Applet {
    private Image image;
    private AppletContext context;
    
    public void init() {
        context = this.getAppletContext();
        String imageURL = this.getParameter("image");
        
        if (imageURL == null) {
            imageURL = "java.jpg";
        }
        
        try {
            URL url = new URL(this.getDocumentBase(), imageURL);
            image = context.getImage(url);
        } catch (MalformedURLException e) {
            e.printStackTrace();
            context.showStatus("Could not load image!");
        }
    }
    
    public void paint(Graphics g) {
        context.showStatus("Displaying image");
        g.drawImage(image, 0, 0, 200, 84, null);
        g.drawString("www.example.com", 35, 100);
    }
}
```

### 7.2 HTML 调用

```html
<html>
<title>The ImageDemo applet</title>
<hr>
<applet code="ImageDemo.class" width="300" height="200">
    <param name="image" value="java.jpg">
</applet>
<hr>
</html>
```

### 7.3 获取图片的方法

```java
// 方法1：通过 URL 获取
URL url = new URL("http://example.com/image.jpg");
Image image = getImage(url);

// 方法2：通过相对路径获取
Image image = getImage(getDocumentBase(), "image.jpg");

// 方法3：通过代码库路径获取
Image image = getImage(getCodeBase(), "images/logo.gif");
```

## 八、播放音频

### 8.1 AudioClip 接口

|方法|说明|
|---|---|
|`play()`|播放音频一次|
|`loop()`|循环播放音频|
|`stop()`|停止播放音频|

### 8.2 完整示例

```java
import java.applet.*;
import java.awt.*;
import java.net.*;

public class AudioDemo extends Applet {
    private AudioClip clip;
    private AppletContext context;
    
    public void init() {
        context = this.getAppletContext();
        String audioURL = this.getParameter("audio");
        
        if (audioURL == null) {
            audioURL = "default.au";
        }
        
        try {
            URL url = new URL(this.getDocumentBase(), audioURL);
            clip = context.getAudioClip(url);
        } catch (MalformedURLException e) {
            e.printStackTrace();
            context.showStatus("Could not load audio file!");
        }
    }
    
    public void start() {
        if (clip != null) {
            clip.loop();  // 循环播放
        }
    }
    
    public void stop() {
        if (clip != null) {
            clip.stop();  // 停止播放
        }
    }
}
```

### 8.3 HTML 调用

```html
<html>
<title>The AudioDemo applet</title>
<hr>
<applet code="AudioDemo.class" width="0" height="0">
    <param name="audio" value="test.wav">
</applet>
<hr>
</html>
```

### 8.4 音频控制示例

```java
import java.applet.*;
import java.awt.*;
import java.awt.event.*;

public class AudioControlDemo extends Applet implements ActionListener {
    private AudioClip clip;
    private Button playButton, loopButton, stopButton;
    
    public void init() {
        // 加载音频
        clip = getAudioClip(getCodeBase(), "music.au");
        
        // 创建按钮
        playButton = new Button("播放");
        loopButton = new Button("循环");
        stopButton = new Button("停止");
        
        // 添加监听器
        playButton.addActionListener(this);
        loopButton.addActionListener(this);
        stopButton.addActionListener(this);
        
        // 添加按钮
        add(playButton);
        add(loopButton);
        add(stopButton);
    }
    
    public void actionPerformed(ActionEvent e) {
        if (e.getSource() == playButton) {
            clip.play();
        } else if (e.getSource() == loopButton) {
            clip.loop();
        } else if (e.getSource() == stopButton) {
            clip.stop();
        }
    }
}
```

## 九、应用程序转换为 Applet

### 9.1 转换步骤

1. 编写 HTML 页面
2. 创建 JApplet 子类（设置为 public）
3. 删除 main() 方法
4. 删除框架窗口构造
5. 将初始化代码移到 init() 方法
6. 删除 setSize() 调用
7. 删除 setDefaultCloseOperation() 调用
8. 删除 setTitle() 调用
9. 删除 setVisible(true) 调用

### 9.2 转换示例

**原应用程序：**

```java
import javax.swing.*;
import java.awt.*;

public class MyApplication extends JFrame {
    public MyApplication() {
        setTitle("My Application");
        setSize(400, 300);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        
        JLabel label = new JLabel("Hello World");
        add(label);
        
        setVisible(true);
    }
    
    public static void main(String[] args) {
        new MyApplication();
    }
}
```

**转换后的 Applet：**

```java
import javax.swing.*;
import java.awt.*;

public class MyApplet extends JApplet {
    public void init() {
        JLabel label = new JLabel("Hello World");
        add(label);
    }
}
```

**HTML 文件：**

```html
<html>
<title>My Applet</title>
<applet code="MyApplet.class" width="400" height="300">
</applet>
</html>
```

## 十、Applet 安全限制

### 10.1 沙箱安全机制

|限制|说明|
|---|---|
|**文件访问**|不能读写本地文件|
|**网络访问**|只能连接到下载它的服务器|
|**系统属性**|只能读取部分系统属性|
|**程序执行**|不能执行本地程序|
|**类加载**|不能加载本地库|

### 10.2 允许的操作

```java
// ✅ 允许：读取特定系统属性
String javaVersion = System.getProperty("java.version");
String osName = System.getProperty("os.name");

// ✅ 允许：连接到下载服务器
URL url = new URL(getCodeBase(), "data.txt");

// ❌ 禁止：读写本地文件
// File file = new File("C:/data.txt");

// ❌ 禁止：连接到其他服务器
// URL url = new URL("http://other-server.com/data.txt");
```

## 十一、总结

### 11.1 核心要点

|要点|说明|
|---|---|
|**继承 Applet**|必须继承 java.applet.Applet|
|**生命周期**|init/start/stop/destroy/paint|
|**嵌入 HTML**|使用 标签|
|**参数传递**|使用 标签|
|**安全限制**|沙箱安全机制|

### 11.2 Applet 的现状

**注意**：Applet 技术已经过时，现代浏览器已不再支持：

- Java 9 标记为 deprecated
- Java 11 完全移除
- 现代替代方案：JavaFX、Web 技术（HTML5/JavaScript）

### 11.3 记忆口诀

- **继承 Applet 类**：java.applet.Applet
- **生命周期方法**：init/start/stop/destroy/paint
- **嵌入 HTML 页面**：使用 applet 标签
- **参数用 param**：name 和 value 属性
- **沙箱有限制**：文件网络都受限