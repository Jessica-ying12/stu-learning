## 一、网络编程概述

### 1.1 什么是网络编程？

**网络编程**：编写运行在多个设备（计算机）上的程序，这些设备通过网络连接进行通信。

### 1.2 java.net 包

Java 提供 `java.net` 包来支持网络编程，包含处理网络通信的类和接口。

### 1.3 支持的网络协议

|协议|全称|特点|
|---|---|---|
|**TCP**|传输控制协议|可靠、面向连接、保证数据顺序|
|**UDP**|用户数据报协议|无连接、不可靠、速度快|

---

## 二、TCP vs UDP

### 2.1 对比表

|特性|TCP|UDP|
|---|---|---|
|连接性|面向连接|无连接|
|可靠性|可靠传输|不可靠传输|
|速度|较慢|较快|
|数据顺序|保证顺序|不保证顺序|
|应用场景|文件传输、邮件、网页|视频流、语音通话、游戏|

### 2.2 TCP/IP 协议

**TCP/IP**：互联网协议套件，TCP 通常与 IP 协议一起使用。

---

## 三、Socket 编程基础

### 3.1 什么是 Socket？

**Socket（套接字）**：网络通信的端点，使用 TCP 提供两台计算机之间的通信机制。

### 3.2 Socket 通信流程

```
服务器端                          客户端
   |                                |
   | 1. 创建 ServerSocket            |
   | 2. 调用 accept() 等待           |
   |                                | 3. 创建 Socket
   |                                | 4. 请求连接
   | 5. accept() 返回新 Socket      |
   |<------------------------------>|
   | 6. 建立连接                     |
   |                                |
   | 7. 通过 I/O 流通信              |
   |<------------------------------>|
   |                                |
   | 8. 关闭连接                     |
```

### 3.3 通信步骤详解

1. **服务器端**：
    
    - 实例化 `ServerSocket`，指定端口
    - 调用 `accept()` 方法等待客户端连接
2. **客户端**：
    
    - 实例化 `Socket`，指定服务器地址和端口
    - 尝试连接服务器
3. **建立连接**：
    
    - 服务器的 `accept()` 返回新的 `Socket` 对象
    - 客户端和服务器都有 `Socket` 对象
4. **数据通信**：
    
    - 使用 I/O 流进行双向通信
    - 客户端输出流 → 服务器输入流
    - 服务器输出流 → 客户端输入流

---

## 四、ServerSocket 类

### 4.1 构造方法

|构造方法|描述|
|---|---|
|`ServerSocket(int port)`|创建绑定到指定端口的服务器套接字|
|`ServerSocket(int port, int backlog)`|指定端口和连接队列长度|
|`ServerSocket(int port, int backlog, InetAddress address)`|指定端口、队列长度和本地 IP|
|`ServerSocket()`|创建非绑定服务器套接字|

### 4.2 常用方法

|方法|描述|
|---|---|
|`int getLocalPort()`|返回监听的端口号|
|`Socket accept()`|监听并接受客户端连接（阻塞）|
|`void setSoTimeout(int timeout)`|设置超时时间（毫秒）|
|`void bind(SocketAddress host, int backlog)`|绑定到特定地址和端口|

### 4.3 示例

```java
// 创建服务器套接字，监听 8080 端口
ServerSocket serverSocket = new ServerSocket(8080);

// 设置超时时间为 10 秒
serverSocket.setSoTimeout(10000);

// 等待客户端连接
Socket clientSocket = serverSocket.accept();
```

---

## 五、Socket 类

### 5.1 构造方法

|构造方法|描述|
|---|---|
|`Socket(String host, int port)`|连接到指定主机和端口|
|`Socket(InetAddress host, int port)`|连接到指定 IP 地址和端口|
|`Socket(String host, int port, InetAddress localAddr, int localPort)`|指定本地地址和端口|
|`Socket()`|创建未连接的套接字|

### 5.2 常用方法

|方法|描述|
|---|---|
|`void connect(SocketAddress host, int timeout)`|连接到服务器，指定超时|
|`InetAddress getInetAddress()`|返回连接的地址|
|`int getPort()`|返回远程端口|
|`int getLocalPort()`|返回本地端口|
|`InputStream getInputStream()`|获取输入流|
|`OutputStream getOutputStream()`|获取输出流|
|`void close()`|关闭套接字|

### 5.3 示例

```java
// 连接到本地主机的 8080 端口
Socket socket = new Socket("localhost", 8080);

// 获取输入输出流
OutputStream out = socket.getOutputStream();
InputStream in = socket.getInputStream();

// 关闭连接
socket.close();
```

---

## 六、InetAddress 类

### 6.1 常用方法

|方法|描述|
|---|---|
|`static InetAddress getByAddress(byte[] addr)`|根据 IP 地址返回 InetAddress|
|`static InetAddress getByName(String host)`|根据主机名返回 InetAddress|
|`static InetAddress getLocalHost()`|返回本地主机|
|`String getHostAddress()`|返回 IP 地址字符串|
|`String getHostName()`|返回主机名|
|`String toString()`|转换为字符串|

### 6.2 示例

```java
import java.net.InetAddress;

public class InetAddressExample {
    public static void main(String[] args) throws Exception {
        // 获取本地主机
        InetAddress localHost = InetAddress.getLocalHost();
        System.out.println("Local Host: " + localHost);
        System.out.println("Host Name: " + localHost.getHostName());
        System.out.println("Host Address: " + localHost.getHostAddress());
        
        // 根据主机名获取地址
        InetAddress google = InetAddress.getByName("www.google.com");
        System.out.println("Google: " + google);
    }
}
```

---

## 七、完整的 Socket 通信示例

### 7.1 服务器端代码

```java
// 文件名: GreetingServer.java
import java.net.*;
import java.io.*;

public class GreetingServer extends Thread {
    private ServerSocket serverSocket;
    
    public GreetingServer(int port) throws IOException {
        serverSocket = new ServerSocket(port);
        serverSocket.setSoTimeout(10000);  // 10秒超时
    }
    
    public void run() {
        while (true) {
            try {
                System.out.println("Waiting for client on port " +
                    serverSocket.getLocalPort() + "...");
                
                // 等待客户端连接
                Socket server = serverSocket.accept();
                
                System.out.println("Just connected to " +
                    server.getRemoteSocketAddress());
                
                // 获取输入流
                DataInputStream in = new DataInputStream(server.getInputStream());
                System.out.println(in.readUTF());
                
                // 获取输出流
                DataOutputStream out = new DataOutputStream(server.getOutputStream());
                out.writeUTF("Thank you for connecting to " +
                    server.getLocalSocketAddress() + "\nGoodbye!");
                
                // 关闭连接
                server.close();
                
            } catch (SocketTimeoutException s) {
                System.out.println("Socket timed out!");
                break;
            } catch (IOException e) {
                e.printStackTrace();
                break;
            }
        }
    }
    
    public static void main(String[] args) {
        int port = Integer.parseInt(args[0]);
        try {
            Thread t = new GreetingServer(port);
            t.start();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 7.2 客户端代码

```java
// 文件名: GreetingClient.java
import java.net.*;
import java.io.*;

public class GreetingClient {
    public static void main(String[] args) {
        String serverName = args[0];
        int port = Integer.parseInt(args[1]);
        
        try {
            System.out.println("Connecting to " + serverName +
                " on port " + port);
            
            // 连接到服务器
            Socket client = new Socket(serverName, port);
            
            System.out.println("Just connected to " +
                client.getRemoteSocketAddress());
            
            // 发送数据到服务器
            OutputStream outToServer = client.getOutputStream();
            DataOutputStream out = new DataOutputStream(outToServer);
            out.writeUTF("Hello from " + client.getLocalSocketAddress());
            
            // 接收服务器响应
            InputStream inFromServer = client.getInputStream();
            DataInputStream in = new DataInputStream(inFromServer);
            System.out.println("Server says " + in.readUTF());
            
            // 关闭连接
            client.close();
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 7.3 运行示例

**启动服务器：**

```bash
$ javac GreetingServer.java
$ java GreetingServer 6066
Waiting for client on port 6066...
```

**启动客户端：**

```bash
$ javac GreetingClient.java
$ java GreetingClient localhost 6066
Connecting to localhost on port 6066
Just connected to localhost/127.0.0.1:6066
Server says Thank you for connecting to /127.0.0.1:6066
Goodbye!
```

---

## 八、实用的 Socket 编程示例

### 8.1 简单的回声服务器

```java
import java.net.*;
import java.io.*;

public class EchoServer {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(8080);
        System.out.println("Echo Server started on port 8080");
        
        while (true) {
            Socket clientSocket = serverSocket.accept();
            System.out.println("Client connected: " + clientSocket.getInetAddress());
            
            // 为每个客户端创建新线程
            new Thread(() -> {
                try {
                    BufferedReader in = new BufferedReader(
                        new InputStreamReader(clientSocket.getInputStream()));
                    PrintWriter out = new PrintWriter(
                        clientSocket.getOutputStream(), true);
                    
                    String inputLine;
                    while ((inputLine = in.readLine()) != null) {
                        System.out.println("Received: " + inputLine);
                        out.println("Echo: " + inputLine);
                        
                        if ("bye".equalsIgnoreCase(inputLine)) {
                            break;
                        }
                    }
                    
                    clientSocket.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }).start();
        }
    }
}
```

### 8.2 回声客户端

```java
import java.net.*;
import java.io.*;

public class EchoClient {
    public static void main(String[] args) throws IOException {
        Socket socket = new Socket("localhost", 8080);
        
        BufferedReader userInput = new BufferedReader(
            new InputStreamReader(System.in));
        PrintWriter out = new PrintWriter(socket.getOutputStream(), true);
        BufferedReader in = new BufferedReader(
            new InputStreamReader(socket.getInputStream()));
        
        String userInputLine;
        while ((userInputLine = userInput.readLine()) != null) {
            out.println(userInputLine);
            System.out.println("Server: " + in.readLine());
            
            if ("bye".equalsIgnoreCase(userInputLine)) {
                break;
            }
        }
        
        socket.close();
    }
}
```

### 8.3 多线程服务器

```java
import java.net.*;
import java.io.*;

public class MultiThreadServer {
    public static void main(String[] args) throws IOException {
        ServerSocket serverSocket = new ServerSocket(8080);
        System.out.println("Multi-thread Server started");
        
        while (true) {
            Socket clientSocket = serverSocket.accept();
            new ClientHandler(clientSocket).start();
        }
    }
}

class ClientHandler extends Thread {
    private Socket socket;
    
    public ClientHandler(Socket socket) {
        this.socket = socket;
    }
    
    public void run() {
        try {
            BufferedReader in = new BufferedReader(
                new InputStreamReader(socket.getInputStream()));
            PrintWriter out = new PrintWriter(
                socket.getOutputStream(), true);
            
            out.println("Welcome! Type 'quit' to exit.");
            
            String inputLine;
            while ((inputLine = in.readLine()) != null) {
                if ("quit".equalsIgnoreCase(inputLine)) {
                    break;
                }
                out.println("You said: " + inputLine);
            }
            
            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 九、Socket 编程最佳实践

### 9.1 资源管理

```java
// ✅ 推荐：使用 try-with-resources
try (ServerSocket serverSocket = new ServerSocket(8080);
     Socket clientSocket = serverSocket.accept();
     BufferedReader in = new BufferedReader(
         new InputStreamReader(clientSocket.getInputStream()));
     PrintWriter out = new PrintWriter(
         clientSocket.getOutputStream(), true)) {
    
    // 通信逻辑
    
} catch (IOException e) {
    e.printStackTrace();
}
```

### 9.2 异常处理

```java
try {
    Socket socket = new Socket("localhost", 8080);
    // 通信逻辑
} catch (UnknownHostException e) {
    System.err.println("Unknown host: " + e.getMessage());
} catch (IOException e) {
    System.err.println("I/O error: " + e.getMessage());
} finally {
    // 清理资源
}
```

### 9.3 超时设置

```java
Socket socket = new Socket();
socket.connect(new InetSocketAddress("localhost", 8080), 5000);  // 5秒连接超时
socket.setSoTimeout(10000);  // 10秒读取超时
```

### 9.4 缓冲区优化

```java
// 设置缓冲区大小
socket.setReceiveBufferSize(8192);
socket.setSendBufferSize(8192);

// 禁用 Nagle 算法（减少延迟）
socket.setTcpNoDelay(true);
```

---

## 十、常见问题与解决方案

### 10.1 端口被占用

```java
try {
    ServerSocket serverSocket = new ServerSocket(8080);
} catch (BindException e) {
    System.err.println("Port 8080 is already in use");
}
```

### 10.2 连接超时

```java
Socket socket = new Socket();
try {
    socket.connect(new InetSocketAddress("localhost", 8080), 5000);
} catch (SocketTimeoutException e) {
    System.err.println("Connection timeout");
}
```

### 10.3 读取超时

```java
socket.setSoTimeout(10000);  // 10秒超时
try {
    int data = inputStream.read();
} catch (SocketTimeoutException e) {
    System.err.println("Read timeout");
}
```

---

## 十一、重点总结

### 11.1 核心概念

1. **Socket**：网络通信的端点
2. **ServerSocket**：服务器端监听套接字
3. **TCP**：可靠的面向连接协议
4. **I/O 流**：通过输入输出流进行数据传输

### 11.2 通信流程

```
服务器：ServerSocket → accept() → Socket → I/O 流 → 通信
客户端：Socket → connect() → I/O 流 → 通信
```

### 11.3 最佳实践

|实践|说明|
|---|---|
|使用 try-with-resources|自动关闭资源|
|设置超时|避免无限等待|
|多线程处理|支持多客户端|
|异常处理|捕获并处理网络异常|
|缓冲区优化|提高性能|

### 11.4 记忆口诀

```
Socket 网络通信端点
ServerSocket 服务器监听
accept 等待客户连接
I/O 流进行数据传输
TCP 可靠面向连接
异常处理要完善
资源关闭要及时
多线程支持并发
```

### 11.5 下一步学习

- URL 处理和 HTTP 通信
- UDP 编程（DatagramSocket）
- NIO（非阻塞 I/O）
- 网络安全（SSL/TLS）