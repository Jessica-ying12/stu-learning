## 一、邮件发送概述

### 1.1 前置准备

**需要的库：**

1. **JavaMail API** (Version 1.2+)
2. **Java Activation Framework (JAF)** (Version 1.1.1+)

**配置步骤：**

1. 下载 `mail.jar` 和 `activation.jar`
2. 将这两个 JAR 文件添加到 CLASSPATH

### 1.2 核心组件

|组件|说明|
|---|---|
|`Session`|邮件会话对象|
|`MimeMessage`|邮件消息对象|
|`Transport`|邮件传输对象|
|`InternetAddress`|邮件地址对象|

## 二、发送简单邮件

### 2.1 基本步骤

1. 设置邮件服务器属性
2. 获取 Session 对象
3. 创建 MimeMessage 对象
4. 设置发件人、收件人、主题、内容
5. 发送邮件

### 2.2 完整示例

```java
// 文件名: SendEmail.java
import java.util.*;
import javax.mail.*;
import javax.mail.internet.*;
import javax.activation.*;

public class SendEmail {
    public static void main(String[] args) {
        // 收件人电子邮箱
        String to = "abcd@gmail.com";
        
        // 发件人电子邮箱
        String from = "web@gmail.com";
        
        // 指定发送邮件的主机
        String host = "localhost";
        
        // 获取系统属性
        Properties properties = System.getProperties();
        
        // 设置邮件服务器
        properties.setProperty("mail.smtp.host", host);
        
        // 获取默认 session 对象
        Session session = Session.getDefaultInstance(properties);
        
        try {
            // 创建默认的 MimeMessage 对象
            MimeMessage message = new MimeMessage(session);
            
            // Set From: 头部字段
            message.setFrom(new InternetAddress(from));
            
            // Set To: 头部字段
            message.addRecipient(Message.RecipientType.TO,
                                new InternetAddress(to));
            
            // Set Subject: 头部字段
            message.setSubject("This is the Subject Line!");
            
            // 设置消息体
            message.setText("This is actual message");
            
            // 发送消息
            Transport.send(message);
            System.out.println("Sent message successfully....");
            
        } catch (MessagingException mex) {
            mex.printStackTrace();
        }
    }
}
```

### 2.3 运行结果

```bash
$ java SendEmail
Sent message successfully....
```

## 三、发送给多个收件人

### 3.1 方法签名

```java
void addRecipients(Message.RecipientType type, 
                   Address[] addresses) throws MessagingException
```

### 3.2 收件人类型

|类型|说明|
|---|---|
|`Message.RecipientType.TO`|收件人|
|`Message.RecipientType.CC`|抄送 (Carbon Copy)|
|`Message.RecipientType.BCC`|密送 (Blind Carbon Copy)|

### 3.3 示例代码

```java
// 多个收件人
InternetAddress[] toAddresses = {
    new InternetAddress("user1@example.com"),
    new InternetAddress("user2@example.com"),
    new InternetAddress("user3@example.com")
};
message.addRecipients(Message.RecipientType.TO, toAddresses);

// 抄送
InternetAddress[] ccAddresses = {
    new InternetAddress("cc1@example.com"),
    new InternetAddress("cc2@example.com")
};
message.addRecipients(Message.RecipientType.CC, ccAddresses);

// 密送
InternetAddress[] bccAddresses = {
    new InternetAddress("bcc1@example.com")
};
message.addRecipients(Message.RecipientType.BCC, bccAddresses);
```

## 四、发送 HTML 邮件

### 4.1 关键方法

```java
message.setContent(String content, String contentType);
```

### 4.2 完整示例

```java
// 文件名: SendHTMLEmail.java
import java.util.*;
import javax.mail.*;
import javax.mail.internet.*;
import javax.activation.*;

public class SendHTMLEmail {
    public static void main(String[] args) {
        // 收件人电子邮箱
        String to = "abcd@gmail.com";
        
        // 发件人电子邮箱
        String from = "web@gmail.com";
        
        // 指定发送邮件的主机
        String host = "localhost";
        
        // 获取系统属性
        Properties properties = System.getProperties();
        
        // 设置邮件服务器
        properties.setProperty("mail.smtp.host", host);
        
        // 获取默认的 Session 对象
        Session session = Session.getDefaultInstance(properties);
        
        try {
            // 创建默认的 MimeMessage 对象
            MimeMessage message = new MimeMessage(session);
            
            // Set From: 头部字段
            message.setFrom(new InternetAddress(from));
            
            // Set To: 头部字段
            message.addRecipient(Message.RecipientType.TO,
                                new InternetAddress(to));
            
            // Set Subject: 头字段
            message.setSubject("This is the Subject Line!");
            
            // 发送 HTML 消息，可以插入 HTML 标签
            message.setContent("<h1>This is actual message</h1>",
                              "text/html");
            
            // 发送消息
            Transport.send(message);
            System.out.println("Sent message successfully....");
            
        } catch (MessagingException mex) {
            mex.printStackTrace();
        }
    }
}
```

### 4.3 HTML 内容示例

```java
String htmlContent = 
    "<html>" +
    "<head><style>body{font-family: Arial;}</style></head>" +
    "<body>" +
    "<h1 style='color: blue;'>欢迎使用我们的服务</h1>" +
    "<p>这是一封 <strong>HTML</strong> 格式的邮件。</p>" +
    "<ul>" +
    "<li>功能1</li>" +
    "<li>功能2</li>" +
    "<li>功能3</li>" +
    "</ul>" +
    "</body>" +
    "</html>";

message.setContent(htmlContent, "text/html; charset=UTF-8");
```

## 五、发送带附件的邮件

### 5.1 核心类

|类|说明|
|---|---|
|`MimeBodyPart`|邮件体部分|
|`Multipart`|多部分消息|
|`DataSource`|数据源|
|`DataHandler`|数据处理器|

### 5.2 完整示例

```java
// 文件名: SendFileEmail.java
import java.util.*;
import javax.mail.*;
import javax.mail.internet.*;
import javax.activation.*;

public class SendFileEmail {
    public static void main(String[] args) {
        // 收件人电子邮箱
        String to = "abcd@gmail.com";
        
        // 发件人电子邮箱
        String from = "web@gmail.com";
        
        // 指定发送邮件的主机
        String host = "localhost";
        
        // 获取系统属性
        Properties properties = System.getProperties();
        
        // 设置邮件服务器
        properties.setProperty("mail.smtp.host", host);
        
        // 获取默认的 Session 对象
        Session session = Session.getDefaultInstance(properties);
        
        try {
            // 创建默认的 MimeMessage 对象
            MimeMessage message = new MimeMessage(session);
            
            // Set From: 头部字段
            message.setFrom(new InternetAddress(from));
            
            // Set To: 头部字段
            message.addRecipient(Message.RecipientType.TO,
                                new InternetAddress(to));
            
            // Set Subject: 头字段
            message.setSubject("This is the Subject Line!");
            
            // 创建消息部分
            BodyPart messageBodyPart = new MimeBodyPart();
            
            // 消息文本
            messageBodyPart.setText("This is message body");
            
            // 创建多重消息
            Multipart multipart = new MimeMultipart();
            
            // 设置文本消息部分
            multipart.addBodyPart(messageBodyPart);
            
            // 附件部分
            messageBodyPart = new MimeBodyPart();
            String filename = "file.txt";
            DataSource source = new FileDataSource(filename);
            messageBodyPart.setDataHandler(new DataHandler(source));
            messageBodyPart.setFileName(filename);
            multipart.addBodyPart(messageBodyPart);
            
            // 发送完整消息
            message.setContent(multipart);
            
            // 发送消息
            Transport.send(message);
            System.out.println("Sent message successfully....");
            
        } catch (MessagingException mex) {
            mex.printStackTrace();
        }
    }
}
```

### 5.3 多个附件示例

```java
// 创建多重消息
Multipart multipart = new MimeMultipart();

// 文本部分
BodyPart textPart = new MimeBodyPart();
textPart.setText("邮件正文内容");
multipart.addBodyPart(textPart);

// 附件1
BodyPart attachment1 = new MimeBodyPart();
DataSource source1 = new FileDataSource("document.pdf");
attachment1.setDataHandler(new DataHandler(source1));
attachment1.setFileName("document.pdf");
multipart.addBodyPart(attachment1);

// 附件2
BodyPart attachment2 = new MimeBodyPart();
DataSource source2 = new FileDataSource("image.jpg");
attachment2.setDataHandler(new DataHandler(source2));
attachment2.setFileName("image.jpg");
multipart.addBodyPart(attachment2);

// 设置完整消息
message.setContent(multipart);
```

## 六、用户认证

### 6.1 认证配置

```java
Properties properties = System.getProperties();
properties.setProperty("mail.smtp.host", "smtp.example.com");
properties.put("mail.smtp.auth", "true");  // 启用认证
properties.setProperty("mail.user", "myuser");
properties.setProperty("mail.password", "mypwd");
```

### 6.2 使用 Authenticator

```java
Session session = Session.getDefaultInstance(properties, new Authenticator() {
    public PasswordAuthentication getPasswordAuthentication() {
        return new PasswordAuthentication("username", "password");
    }
});
```

## 七、QQ 邮箱发送示例

### 7.1 QQ 邮箱配置

**前置步骤：**

1. 登录 QQ 邮箱
2. 进入"设置" → "账户"
3. 开启 POP3/SMTP 服务
4. 获取授权码（不是 QQ 密码）

### 7.2 完整代码

```java
// 文件名: SendEmail2.java
import java.util.Properties;
import javax.mail.Authenticator;
import javax.mail.Message;
import javax.mail.MessagingException;
import javax.mail.PasswordAuthentication;
import javax.mail.Session;
import javax.mail.Transport;
import javax.mail.internet.InternetAddress;
import javax.mail.internet.MimeMessage;

public class SendEmail2 {
    public static void main(String[] args) {
        // 收件人电子邮箱
        String to = "xxx@qq.com";
        
        // 发件人电子邮箱
        String from = "xxx@qq.com";
        
        // 指定发送邮件的主机为 QQ 邮件服务器
        String host = "smtp.qq.com";
        
        // 获取系统属性
        Properties properties = System.getProperties();
        
        // 设置邮件服务器
        properties.setProperty("mail.smtp.host", host);
        properties.put("mail.smtp.auth", "true");
        
        // 获取默认 session 对象
        Session session = Session.getDefaultInstance(properties, new Authenticator() {
            public PasswordAuthentication getPasswordAuthentication() {
                // 发件人邮箱用户名、授权码（不是QQ密码）
                return new PasswordAuthentication("xxx@qq.com", "授权码");
            }
        });
        
        try {
            // 创建默认的 MimeMessage 对象
            MimeMessage message = new MimeMessage(session);
            
            // Set From: 头部字段
            message.setFrom(new InternetAddress(from));
            
            // Set To: 头部字段
            message.addRecipient(Message.RecipientType.TO,
                                new InternetAddress(to));
            
            // Set Subject: 头部字段
            message.setSubject("This is the Subject Line!");
            
            // 设置消息体
            message.setText("This is actual message");
            
            // 发送消息
            Transport.send(message);
            System.out.println("Sent message successfully....from w3cschool.cn");
            
        } catch (MessagingException mex) {
            mex.printStackTrace();
        }
    }
}
```

## 八、常用邮件服务器配置

### 8.1 主流邮件服务器

|邮件服务商|SMTP 服务器|端口|SSL 端口|
|---|---|---|---|
|**QQ 邮箱**|smtp.qq.com|25|465/587|
|**163 邮箱**|smtp.163.com|25|465/994|
|**126 邮箱**|smtp.126.com|25|465/994|
|**Gmail**|smtp.gmail.com|587|465|
|**Outlook**|smtp.office365.com|587|-|
|**新浪邮箱**|smtp.sina.com|25|465|

### 8.2 SSL/TLS 配置

```java
Properties properties = new Properties();
properties.put("mail.smtp.host", "smtp.qq.com");
properties.put("mail.smtp.port", "465");
properties.put("mail.smtp.auth", "true");

// 启用 SSL
properties.put("mail.smtp.ssl.enable", "true");
properties.put("mail.smtp.ssl.protocols", "TLSv1.2");

// 或使用 STARTTLS
properties.put("mail.smtp.starttls.enable", "true");
properties.put("mail.smtp.starttls.required", "true");
```

## 九、完整的邮件工具类

### 9.1 EmailUtil 工具类

```java
import java.util.*;
import javax.mail.*;
import javax.mail.internet.*;
import javax.activation.*;

public class EmailUtil {
    
    // 邮件配置
    private String host;
    private String username;
    private String password;
    private String from;
    
    public EmailUtil(String host, String username, String password, String from) {
        this.host = host;
        this.username = username;
        this.password = password;
        this.from = from;
    }
    
    /**
     * 发送简单文本邮件
     */
    public void sendTextEmail(String to, String subject, String content) 
            throws MessagingException {
        Session session = createSession();
        MimeMessage message = new MimeMessage(session);
        
        message.setFrom(new InternetAddress(from));
        message.addRecipient(Message.RecipientType.TO, new InternetAddress(to));
        message.setSubject(subject);
        message.setText(content);
        
        Transport.send(message);
        System.out.println("文本邮件发送成功！");
    }
    
    /**
     * 发送 HTML 邮件
     */
    public void sendHtmlEmail(String to, String subject, String htmlContent) 
            throws MessagingException {
        Session session = createSession();
        MimeMessage message = new MimeMessage(session);
        
        message.setFrom(new InternetAddress(from));
        message.addRecipient(Message.RecipientType.TO, new InternetAddress(to));
        message.setSubject(subject);
        message.setContent(htmlContent, "text/html; charset=UTF-8");
        
        Transport.send(message);
        System.out.println("HTML 邮件发送成功！");
    }
    
    /**
     * 发送带附件的邮件
     */
    public void sendEmailWithAttachment(String to, String subject, 
                                       String content, String filePath) 
            throws MessagingException {
        Session session = createSession();
        MimeMessage message = new MimeMessage(session);
        
        message.setFrom(new InternetAddress(from));
        message.addRecipient(Message.RecipientType.TO, new InternetAddress(to));
        message.setSubject(subject);
        
        // 创建消息部分
        BodyPart messageBodyPart = new MimeBodyPart();
        messageBodyPart.setText(content);
        
        // 创建多重消息
        Multipart multipart = new MimeMultipart();
        multipart.addBodyPart(messageBodyPart);
        
        // 附件部分
        messageBodyPart = new MimeBodyPart();
        DataSource source = new FileDataSource(filePath);
        messageBodyPart.setDataHandler(new DataHandler(source));
        messageBodyPart.setFileName(new java.io.File(filePath).getName());
        multipart.addBodyPart(messageBodyPart);
        
        message.setContent(multipart);
        
        Transport.send(message);
        System.out.println("带附件的邮件发送成功！");
    }
    
    /**
     * 发送给多个收件人
     */
    public void sendToMultipleRecipients(String[] toList, String subject, 
                                        String content) throws MessagingException {
        Session session = createSession();
        MimeMessage message = new MimeMessage(session);
        
        message.setFrom(new InternetAddress(from));
        
        // 添加多个收件人
        InternetAddress[] addresses = new InternetAddress[toList.length];
        for (int i = 0; i < toList.length; i++) {
            addresses[i] = new InternetAddress(toList[i]);
        }
        message.addRecipients(Message.RecipientType.TO, addresses);
        
        message.setSubject(subject);
        message.setText(content);
        
        Transport.send(message);
        System.out.println("群发邮件发送成功！");
    }
    
    /**
     * 创建 Session
     */
    private Session createSession() {
        Properties properties = new Properties();
        properties.put("mail.smtp.host", host);
        properties.put("mail.smtp.auth", "true");
        properties.put("mail.smtp.ssl.enable", "true");
        properties.put("mail.smtp.port", "465");
        
        return Session.getInstance(properties, new Authenticator() {
            protected PasswordAuthentication getPasswordAuthentication() {
                return new PasswordAuthentication(username, password);
            }
        });
    }
}
```

### 9.2 使用示例

```java
public class EmailDemo {
    public static void main(String[] args) {
        // 创建邮件工具对象
        EmailUtil emailUtil = new EmailUtil(
            "smtp.qq.com",           // SMTP 服务器
            "your@qq.com",           // 用户名
            "your_auth_code",        // 授权码
            "your@qq.com"            // 发件人
        );
        
        try {
            // 1. 发送简单文本邮件
            emailUtil.sendTextEmail(
                "recipient@example.com",
                "测试邮件",
                "这是一封测试邮件"
            );
            
            // 2. 发送 HTML 邮件
            String htmlContent = 
                "<h1>欢迎</h1>" +
                "<p>这是一封 <strong>HTML</strong> 邮件</p>";
            emailUtil.sendHtmlEmail(
                "recipient@example.com",
                "HTML 邮件",
                htmlContent
            );
            
            // 3. 发送带附件的邮件
            emailUtil.sendEmailWithAttachment(
                "recipient@example.com",
                "带附件的邮件",
                "请查收附件",
                "/path/to/file.pdf"
            );
            
            // 4. 群发邮件
            String[] recipients = {
                "user1@example.com",
                "user2@example.com",
                "user3@example.com"
            };
            emailUtil.sendToMultipleRecipients(
                recipients,
                "群发邮件",
                "这是一封群发邮件"
            );
            
        } catch (MessagingException e) {
            e.printStackTrace();
        }
    }
}
```

## 十、常见问题与解决方案

### 10.1 常见异常

|异常|原因|解决方案|
|---|---|---|
|`AuthenticationFailedException`|认证失败|检查用户名和密码/授权码|
|`MessagingException`|消息异常|检查邮件格式和配置|
|`SendFailedException`|发送失败|检查收件人地址和网络|
|`ConnectException`|连接失败|检查 SMTP 服务器和端口|

### 10.2 QQ 邮箱常见问题

```java
// 问题1：535 Login Fail
// 原因：使用了 QQ 密码而不是授权码
// 解决：使用 QQ 邮箱的授权码

// 问题2：连接超时
// 原因：未启用 SSL 或端口错误
// 解决：
properties.put("mail.smtp.ssl.enable", "true");
properties.put("mail.smtp.port", "465");

// 问题3：发送失败
// 原因：未开启 SMTP 服务
// 解决：在 QQ 邮箱设置中开启 POP3/SMTP 服务
```

### 10.3 调试技巧

```java
// 启用调试模式
Session session = Session.getInstance(properties, authenticator);
session.setDebug(true);  // 打印详细的调试信息
```

## 十一、高级功能

### 11.1 设置邮件优先级

```java
// 设置优先级
message.setHeader("X-Priority", "1");  // 1=最高, 3=普通, 5=最低
```

### 11.2 设置发送时间

```java
// 设置发送时间
message.setSentDate(new Date());
```

### 11.3 请求回执

```java
// 请求已读回执
message.setHeader("Disposition-Notification-To", from);
```

### 11.4 嵌入图片的 HTML 邮件

```java
public void sendHtmlEmailWithImage(String to, String subject) 
        throws MessagingException {
    Session session = createSession();
    MimeMessage message = new MimeMessage(session);
    
    message.setFrom(new InternetAddress(from));
    message.addRecipient(Message.RecipientType.TO, new InternetAddress(to));
    message.setSubject(subject);
    
    // HTML 部分
    MimeBodyPart htmlPart = new MimeBodyPart();
    String htmlContent = 
        "<html><body>" +
        "<h1>欢迎</h1>" +
        "<img src='cid:image1'>" +  // 引用图片
        "</body></html>";
    htmlPart.setContent(htmlContent, "text/html; charset=UTF-8");
    
    // 图片部分
    MimeBodyPart imagePart = new MimeBodyPart();
    DataSource fds = new FileDataSource("image.jpg");
    imagePart.setDataHandler(new DataHandler(fds));
    imagePart.setHeader("Content-ID", "<image1>");  // 设置 Content-ID
    
    // 组合
    MimeMultipart multipart = new MimeMultipart("related");
    multipart.addBodyPart(htmlPart);
    multipart.addBodyPart(imagePart);
    
    message.setContent(multipart);
    Transport.send(message);
}
```

## 十二、最佳实践

### 12.1 配置管理

```java
// 使用配置文件管理邮件配置
public class EmailConfig {
    private static Properties config = new Properties();
    
    static {
        try {
            config.load(new FileInputStream("email.properties"));
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    public static String getHost() {
        return config.getProperty("mail.smtp.host");
    }
    
    public static String getUsername() {
        return config.getProperty("mail.username");
    }
    
    public static String getPassword() {
        return config.getProperty("mail.password");
    }
}
```

**email.properties 文件：**

```properties
mail.smtp.host=smtp.qq.com
mail.smtp.port=465
mail.smtp.auth=true
mail.smtp.ssl.enable=true
mail.username=your@qq.com
mail.password=your_auth_code
mail.from=your@qq.com
```

### 12.2 异常处理

```java
public boolean sendEmail(String to, String subject, String content) {
    try {
        // 发送邮件逻辑
        return true;
    } catch (AuthenticationFailedException e) {
        System.err.println("认证失败：" + e.getMessage());
        return false;
    } catch (SendFailedException e) {
        System.err.println("发送失败：" + e.getMessage());
        return false;
    } catch (MessagingException e) {
        System.err.println("邮件异常：" + e.getMessage());
        return false;
    }
}
```

### 12.3 邮件模板

```java
public class EmailTemplate {
    
    public static String getWelcomeEmail(String username) {
        return String.format(
            "<html><body>" +
            "<h1>欢迎 %s！</h1>" +
            "<p>感谢您注册我们的服务。</p>" +
            "<p>点击下面的链接激活账户：</p>" +
            "<a href='http://example.com/activate'>激活账户</a>" +
            "</body></html>",
            username
        );
    }
    
    public static String getPasswordResetEmail(String resetLink) {
        return String.format(
            "<html><body>" +
            "<h1>重置密码</h1>" +
            "<p>点击下面的链接重置密码：</p>" +
            "<a href='%s'>重置密码</a>" +
            "<p>链接将在 24 小时后失效。</p>" +
            "</body></html>",
            resetLink
        );
    }
}
```

## 十三、总结

### 13.1 核心要点

|要点|说明|
|---|---|
|**JavaMail API**|必须添加到 CLASSPATH|
|**Session**|邮件会话对象|
|**MimeMessage**|邮件消息对象|
|**Transport**|发送邮件|
|**认证**|使用授权码而非密码|

### 13.2 发送流程

```
1. 配置属性 (Properties)
   ↓
2. 创建会话 (Session)
   ↓
3. 创建消息 (MimeMessage)
   ↓
4. 设置内容 (From/To/Subject/Content)
   ↓
5. 发送邮件 (Transport.send)
```

### 13.3 注意事项

1. **使用授权码**：QQ、163 等邮箱需要使用授权码，不是登录密码
2. **启用 SSL**：生产环境建议使用 SSL/TLS 加密
3. **异常处理**：妥善处理各种异常情况
4. **配置管理**：使用配置文件管理敏感信息
5. **测试环境**：先在测试环境验证再部署到生产

### 13.4 记忆口诀

- **Properties 配置**：服务器地址和认证
- **Session 会话**：创建邮件会话对象
- **MimeMessage 消息**：设置发件人收件人
- **Transport 传输**：调用 send 发送邮件
- **授权码认证**：QQ 邮箱要用授权码