### **📚 第一阶段：计算机科学基础（零基础入门）**

> **目标**：理解程序运行的宏观环境和基本原理，为后续学习打下认知基础。此阶段不求甚解，重在建立概念。

#### **1.1 计算机组成原理**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **计算机组成原理** | 二进制、八进制、十六进制转换 | 计算机为什么只用0和1 |
| | 原码、反码、补码 | 负数在内存中是如何表示的 |
| | 定点数与浮点数（IEEE754标准） | 小数为什么有精度丢失 |
| | 字符编码：ASCII、Unicode、UTF-8 | 字母和汉字如何变成二进制 |
| | CPU内部结构：ALU、CU、寄存器 | 代码是谁执行的 |
| | 指令执行过程：取指、译码、执行、回写 | 一行代码背后发生了什么 |
| | 时钟周期、机器周期、指令周期 | CPU主频是什么意思 |
| | 流水线技术 | 为什么多核能提高性能 |
| | 中断机制 | 键盘按下时CPU怎么知道 |
| | 存储层次结构：寄存器→缓存→内存→硬盘 | 为什么要有这么多级存储 |
| | RAM分类：SRAM vs DRAM | 内存和CPU缓存有什么区别 |
| | ROM分类 | 关机后数据为什么不丢失 |
| | 内存地址、字节编址 | 变量在内存中是如何被找到的 |
| | 主存扩展设计：位扩展、字扩展 | 怎么用多颗芯片组成内存条 |
| | 多体交叉存储器 | 如何提高内存访问速度 |
| | Cache工作原理、命中率 | 为什么缓存能加速 |
| | 映射方式：直接映射、全相联、组相联 | 缓存如何找到数据 |
| | 替换算法：LRU、FIFO、随机 | 缓存满了怎么办 |
| | 写策略：写直达、写回 | 修改缓存时内存怎么办 |
| | I/O接口、端口地址 | 外设怎么和CPU通信 |
| | I/O控制方式：程序查询、中断、DMA | 数据拷贝谁来做 |
| | 外设工作原理：显示器、键盘、硬盘 | 屏幕怎么显示像素 |

#### **1.2 操作系统基础**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **操作系统基础** | 进程 vs 程序 | 程序运行起来变成什么 |
| | 进程状态：新建、就绪、运行、阻塞、终止 | 一个进程的完整生命周期 |
| | 进程控制块PCB | 操作系统怎么记住每个进程 |
| | 进程上下文切换 | 多个程序怎么轮流运行 |
| | 进程创建：fork()、exec() | 新进程怎么来的 |
| | 进程终止、僵尸进程、孤儿进程 | 进程死了还占资源吗 |
| | 线程 vs 进程 | 为什么要有线程 |
| | 用户级线程 vs 内核级线程 | 线程由谁管理 |
| | 多线程模型：多对一、一对一、多对多 | React Native的JS线程属于哪种 |
| | 线程池 | 频繁创建线程不好怎么办 |
| | 协程/纤程 | 比线程更轻量的并发单位 |
| | 临界区问题 | 多个进程访问同一资源的问题 |
| | 互斥、同步、死锁 | 这些词到底什么意思 |
| | 信号量、P操作、V操作 | 经典的同步工具 |
| | 经典同步问题：生产者-消费者、读者-写者、哲学家进餐 | 真实场景怎么用信号量 |
| | 管程 | 高级语言的同步机制 |
| | 死锁产生的4个必要条件 | 死锁为什么会发生 |
| | 死锁预防：破坏4个条件 | 怎么不让死锁发生 |
| | 死锁避免：银行家算法 | 动态判断会不会死锁 |
| | 死锁检测与恢复 | 死了怎么活过来 |
| | 调度层次：长程、中程、短程调度 | 什么时候决定谁运行 |
| | 调度算法：FCFS、SJF、优先级、时间片轮转、多级反馈队列 | 各种调度策略优劣 |
| | 逻辑地址 vs 物理地址 | 程序里的地址是谁的地址 |
| | 地址绑定、动态重定位 | 地址怎么转换 |
| | 连续分配：单一分区、固定分区、动态分区 | 老式内存管理 |
| | 碎片问题：外部碎片、内部碎片 | 内存为什么越来越乱 |
| | 伙伴系统、Slab分配 | Linux怎么管理内存 |
| | 分页原理、页框、页 | 内存怎么切块管理 |
| | 页表、页表项结构 | 虚拟地址怎么变物理地址 |
| | 快表TLB | 加速地址转换的硬件 |
| | 两级页表、多级页表 | 页表太大怎么办 |
| | 倒排页表 | 反过来记节省内存 |
| | 分段原理、段表 | 按逻辑分段的好处 |
| | 段页式结合 | 既有分段又有分页 |
| | 虚拟内存思想 | 内存不够了怎么办 |
| | 请求分页、缺页中断 | 需要的页不在内存怎么办 |
| | 页面置换算法：OPT、FIFO、LRU、Clock、NRU | 腾地方该踢谁出去 |
| | 工作集、抖动 | 频繁缺页导致性能崩盘 |
| | 内存映射文件 | 文件也能当内存用 |
| | 文件、目录、路径 | 文件怎么组织 |
| | 文件逻辑结构：流式、记录式 | 文件内部怎么排 |
| | 目录结构：单级、两级、树形、图 | 文件夹怎么组织 |
| | 文件物理结构：连续、链接、索引 | 文件在磁盘上怎么存 |
| | FAT、NTFS、ext4文件系统 | 常见文件系统实现 |
| | 空闲空间管理：位图、链表、索引 | 磁盘空位怎么找 |
| | 设备独立性、逻辑设备名到物理设备名 | 程序怎么不管用啥打印机 |
| | 缓冲区技术：单缓冲、双缓冲、循环缓冲 | 数据中转站 |
| | SPOOLing技术 | 打印机怎么变成共享的 |
| | 磁盘结构、磁盘调度：FCFS、SSTF、SCAN、C-SCAN | 磁盘读写怎么优化 |

#### **1.3 计算机网络基础**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **计算机网络基础** | OSI七层模型 vs TCP/IP四层模型 | 网络分层的思想 |
| | 数据封装与解封装 | 数据发送时怎么一层层包起来 |
| | 协议、服务、接口 | 这些术语的区别 |
| | 信号：模拟信号、数字信号 | 比特怎么变成电信号 |
| | 调制与编码：ASK、FSK、PSK、QAM | 数字变模拟怎么变 |
| | 传输介质：双绞线、同轴电缆、光纤、无线 | 不同介质的区别 |
| | 复用技术：FDM、TDM、WDM、CDMA | 一条线怎么传多路信号 |
| | 成帧、透明传输 | 比特流怎么切分成帧 |
| | 差错控制：奇偶校验、CRC、海明码 | 传错了怎么发现 |
| | 流量控制：停等协议、滑动窗口 | 发太快了怎么办 |
| | 可靠传输：ARQ协议（停等、回退N、选择重传） | 丢了怎么重传 |
| | MAC地址、ARP协议 | 局域网内怎么找到设备 |
| | 以太网协议、CSMA/CD | 有线网络怎么抢着发 |
| | IP协议、IPv4地址结构 | 互联网地址规则 |
| | 子网划分、子网掩码、CIDR | 网络怎么切分成子网 |
| | 路由 vs 转发 | 找路和走路谁负责 |
| | 路由算法：距离向量（RIP）、链路状态（OSPF） | 路由器怎么知道路在哪 |
| | 路由器工作原理、路由表 | 路由器内部长啥样 |
| | ICMP协议：Ping、Traceroute | 网络诊断工具原理 |
| | IP分片与重组 | 大包怎么过小管道 |
| | NAT、端口映射 | 一个公网IP怎么给多台电脑用 |
| | IPv6 | 地址不够了怎么办 |
| | 端口号、套接字 | 怎么找到具体的应用 |
| | UDP协议：特点、报文格式、应用 | 不靠谱但快的协议 |
| | TCP协议：特点、报文格式 | 可靠传输怎么做到 |
| | 三次握手、四次挥手 | 连接怎么建立和关闭 |
| | 可靠传输：序号、确认、超时重传 | 如何保证不丢不乱 |
| | 流量控制：滑动窗口、零窗口 | 根据接收方能力调整 |
| | 拥塞控制：慢开始、拥塞避免、快重传、快恢复 | 根据网络状况调整 |
| | DNS：域名解析过程、递归查询、迭代查询 | 网址怎么变IP |
| | HTTP/HTTPS：报文格式、方法、状态码 | 网页请求的结构 |
| | HTTP 1.0/1.1/2.0/3.0演进 | 协议怎么越来越快 |
| | Cookie、Session、Token | 服务器怎么记住你是谁 |
| | FTP：主动模式、被动模式 | 文件传输协议 |
| | Email：SMTP、POP3、IMAP | 邮件怎么收发 |
| | WebSocket | 全双工通信怎么实现 |

#### **1.4 数据结构基础**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **数据结构基础** | 时间复杂度：大O、大Ω、大Θ | 算法快慢怎么衡量 |
| | 空间复杂度 | 内存占用怎么算 |
| | 最好、最坏、平均情况 | 不同场景的表现 |
| | 顺序表（数组）：实现、插入、删除、查找 | 连续存储怎么操作 |
| | 链表：单链表、双链表、循环链表 | 离散存储怎么串起来 |
| | 链表操作：头插、尾插、删除、反转 | 链表的增删改查 |
| | 静态链表 | 数组模拟链表 |
| | 栈定义：后进先出LIFO | 弹夹式结构 |
| | 顺序栈 vs 链栈 | 两种实现方式 |
| | 栈应用：函数调用栈、表达式求值、括号匹配 | 实际哪里用到了 |
| | 递归与栈的关系 | 递归底层怎么实现 |
| | 队列定义：先进先出FIFO | 排队式结构 |
| | 顺序队列 vs 链队列 | 两种实现方式 |
| | 循环队列 | 解决假溢出问题 |
| | 双端队列、优先队列 | 队列变种 |
| | 队列应用：消息队列、任务队列 | React Native事件队列的原理 |
| | 字符串存储、模式匹配 | 文字怎么存 |
| | BF算法（暴力匹配） | 最简单的匹配方法 |
| | KMP算法、next数组 | 怎么匹配得更快 |
| | 树的定义、术语：根、叶子、深度、度 | 树形结构基本概念 |
| | 二叉树定义、性质 | 为什么二叉树特殊 |
| | 满二叉树、完全二叉树 | 特殊形态 |
| | 二叉树的存储：顺序存储、链式存储 | 树在内存里怎么存 |
| | 前序遍历（根左右） | 先访问根 |
| | 中序遍历（左根右） | 中间访问根 |
| | 后序遍历（左右根） | 最后访问根 |
| | 层序遍历（BFS） | 一层一层访问 |
| | 递归遍历 vs 非递归遍历 | 两种实现方式 |
| | 二叉搜索树BST：查找、插入、删除 | 快速查找的数据结构 |
| | 平衡二叉树AVL：LL、RR、LR、RL旋转 | 怎么让树保持平衡 |
| | 哈夫曼树、哈夫曼编码 | 数据压缩原理 |
| | 堆：大根堆、小根堆、堆化、堆排序 | 优先队列的底层 |
| | B-树、B+树 | 数据库索引结构 |
| | 红黑树 | 平衡树的另一种实现 |
| | 线段树、树状数组 | 区间查询问题 |
| | Trie树（字典树） | 前缀匹配的数据结构 |
| | 图定义：顶点、边、有向/无向、带权 | 网状结构 |
| | 图的存储：邻接矩阵、邻接表 | 两种存储方式 |
| | 图的遍历：DFS、BFS | 怎么走遍所有节点 |
| | 生成树、最小生成树：Prim、Kruskal | 怎么连所有点且总长最短 |
| | 最短路径：Dijkstra、Floyd、Bellman-Ford | 地图导航原理 |
| | 拓扑排序 | 任务依赖怎么排 |
| | 关键路径 | 项目工期怎么算 |
| | 静态查找：顺序查找、折半查找、插值查找 | 有序数组怎么找 |
| | 分块查找 | 索引查找 |
| | 动态查找：二叉搜索树、平衡树 | 频繁增删怎么找 |
| | 哈希查找：哈希函数、冲突解决（链地址、开放地址、再哈希） | 键值对存储原理 |
| | 一致性哈希 | 分布式系统怎么扩容 |
| | 插入排序：直接插入、希尔排序 | 像打牌一样排序 |
| | 交换排序：冒泡排序、快速排序 | 两两比较交换 |
| | 选择排序：简单选择、堆排序 | 选最小的放前面 |
| | 归并排序 | 分治思想的排序 |
| | 基数排序、计数排序、桶排序 | 不比较的排序 |
| | 排序稳定性、复杂度分析 | 各种排序优劣 |

---

### **🎯 第二阶段：JavaScript 核心深度剖析**

> **目标**：**扎实、透彻**地掌握 JavaScript 语言本身，包括其核心语法、运行机制和独特范式。这是你未来所有开发工作的基石。

#### **2.1 语法基础**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **语法基础** | `var` 声明、作用域、变量提升 | 函数作用域，可重复声明 |
| | `let` 声明、块作用域、暂时性死区 | 块作用域，不可重复声明 |
| | `const` 声明、常量、引用不可变 | 块作用域，值不可改（引用类型注意） |
| | 数据类型分类：原始类型 vs 引用类型 | 存储方式不同，赋值行为不同 |
| | 原始类型：number、string、boolean、undefined、null、symbol、bigint | 各自的特点和用法 |
| | 引用类型：object（包括数组、函数、日期等） | 通过引用访问，可扩展属性 |
| | `typeof` 运算符 | 检测变量类型，但有一些历史遗留问题 |
| | `instanceof` 运算符 | 检测对象是否属于某个构造函数的实例 |
| | 显式类型转换：String()、Number()、Boolean() | 手动转换类型 |
| | 隐式类型转换：`+`、`==`、条件判断中的转换 | 自动转换规则，容易出错 |
| | 算术运算符：`+ - * / % **` | 基本数学运算 |
| | 赋值运算符：`=`、`+=`、`-=`等 | 赋值并计算 |
| | 比较运算符：`==`、`===`、`!=`、`!==`、`>`、`<`、`>=`、`<=` | `==`允许类型转换，`===`禁止 |
| | 逻辑运算符：`&&`、`||`、`!` | 短路求值，用于条件组合和默认值 |
| | 位运算符：`&`、`|`、`^`、`~`、`<<`、`>>`、`>>>` | 二进制位操作，用于底层优化 |
| | 三元运算符：`条件 ? 值1 : 值2` | 简洁的条件表达式 |
| | `if-else` 语句 | 根据条件执行不同代码块 |
| | `switch` 语句 | 多分支选择，注意 `break` |
| | `for` 循环：`for`、`for...in`、`for...of` | 不同遍历方式 |
| | `while` 和 `do-while` 循环 | 条件循环 |
| | `break` 和 `continue` | 跳出或继续循环 |

#### **2.2 函数**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **函数** | 函数声明 | 使用 `function` 关键字定义，会提升 |
| | 函数表达式 | 将函数赋值给变量，不提升 |
| | 箭头函数 | 语法简洁，不绑定 `this`、`arguments` |
| | 参数：形参、实参 | 传递数据给函数 |
| | 默认参数 | 为参数提供默认值 |
| | 剩余参数 `...args` | 捕获剩余所有参数为数组 |
| | `arguments` 对象（箭头函数没有） | 类数组对象，包含所有传入的参数 |
| | 返回值 `return` | 函数输出，无 `return` 返回 `undefined` |
| | 全局作用域 | 在任何函数之外声明的变量 |
| | 函数作用域 | 在函数内部声明的变量，外部不可访问 |
| | 块作用域（`let`/`const`） | 在 `{}` 内部声明的变量 |
| | 作用域链 | 变量查找从内到外，直到全局 |
| | 闭包定义 | 函数能够记住并访问其词法作用域，即使在其作用域外执行 |
| | 闭包常见用途：封装私有变量、防抖节流、回调函数 | 实际应用场景 |
| | 闭包内存模型 | 函数为什么能记住外部变量 |
| | 高阶函数：函数作为参数 | 例如 `setTimeout`、`map` 中的回调 |
| | 高阶函数：函数作为返回值 | 例如生成特定功能的函数 |
| | 回调函数 | 将函数作为参数传入，在某个时刻被调用 |

#### **2.3 对象与数组**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **对象** | 对象字面量 `{}` 创建 | 最常用方式 |
| | 属性访问：点语法 `obj.key`、方括号 `obj['key']` | 方括号可用于动态属性名 |
| | 添加/修改属性 | 直接赋值 |
| | 删除属性：`delete` 操作符 | 移除属性 |
| | 方法定义：对象中的函数属性 | 可以简写 `method() {}` |
| | `this` 在对象方法中 | 指向调用该方法的对象 |
| | 遍历对象：`for...in` | 遍历可枚举属性（包括原型链） |
| | 遍历对象：`Object.keys()`、`Object.values()`、`Object.entries()` | 返回自身属性的数组 |
| | 对象合并：`Object.assign()` | 将源对象属性复制到目标对象 |
| | 对象合并：展开运算符 `{...obj1, ...obj2}` | 简洁的合并方式 |
| | 对象冻结 `Object.freeze()` | 使对象不可修改 |
| | 对象密封 `Object.seal()` | 不能添加/删除属性，但可修改 |
| **数组** | 数组字面量 `[]` 创建 | 常用方式 |
| | `length` 属性 | 数组长度，可写（截断或扩展） |
| | 添加/删除元素：`push()`、`pop()` | 末尾操作 |
| | 添加/删除元素：`unshift()`、`shift()` | 开头操作 |
| | 删除/插入/替换：`splice()` | 通用方法 |
| | 提取子数组：`slice()` | 不改变原数组 |
| | 连接数组：`concat()` | 返回新数组 |
| | 查找元素：`indexOf()`、`lastIndexOf()` | 返回索引或 -1 |
| | 判断包含：`includes()` | 返回布尔值 |
| | 查找元素：`find()` | 返回第一个满足条件的元素 |
| | 查找索引：`findIndex()` | 返回第一个满足条件的索引 |
| | 遍历：`forEach()` | 对每个元素执行回调，不返回新数组 |
| | 映射：`map()` | 对每个元素处理，返回新数组 |
| | 过滤：`filter()` | 返回满足条件的元素组成的新数组 |
| | 归并：`reduce()` | 累积计算，返回一个值 |
| | 判断：`some()`、`every()` | 检查是否有/所有元素满足条件 |
| | 排序：`sort()` | 默认按字符串排序，可传入比较函数 |
| | 反转：`reverse()` | 颠倒数组顺序 |
| | 数组转字符串：`join()` | 用指定分隔符连接元素 |
| | 扁平化：`flat()`、`flatMap()` | 将嵌套数组展开 |
| **解构赋值** | 数组解构：`[a, b] = [1, 2]` | 按位置提取 |
| | 对象解构：`{name, age} = person` | 按属性名提取 |
| | 默认值：`[a=1] = []` | 防止 `undefined` |
| | 重命名：`{name: userName}` | 将 `name` 赋值给 `userName` |
| **展开运算符** | 数组展开：`[...arr]` | 复制数组或合并数组 |
| | 对象展开：`{...obj}` | 复制对象或合并对象 |

#### **2.4 原型与继承**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **原型与继承** | 构造函数 | 普通函数，通过 `new` 调用，约定首字母大写 |
| | `new` 操作符的过程 | 创建空对象，链接原型，绑定 `this`，返回对象 |
| | 实例属性与方法 | 在构造函数内部通过 `this` 添加 |
| | 静态属性与方法 | 直接挂载到构造函数上（`Array.isArray`） |
| | `prototype` 属性 | 每个函数都有，指向原型对象 |
| | 原型对象 | 包含可以被所有实例共享的属性和方法 |
| | `__proto__` 属性 | 每个对象都有，指向其构造函数的 `prototype` |
| | 原型链 | 对象通过 `__proto__` 连接，形成链，属性查找沿链向上 |
| | `hasOwnProperty()` | 检查属性是否为对象自身所有（而非原型链） |
| | `in` 操作符 | 检查属性是否在对象或其原型链上 |
| | 原型链继承 | 将子类的 `prototype` 指向父类实例 |
| | 借用构造函数继承 | 在子类构造函数中调用父类构造函数（`Parent.call(this)`） |
| | 组合继承 | 原型链继承方法，借用构造函数继承属性 |
| | ES6 `class` 语法 | 更清晰的语法，本质仍是原型链 |
| | `extends` 关键字 | 继承父类 |
| | `super` 关键字 | 调用父类构造函数或方法 |

#### **2.5 异步编程**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **异步编程** | 同步 vs 异步 | 阻塞和非阻塞的区别 |
| | 事件循环（Event Loop） | JavaScript 单线程如何实现非阻塞的异步操作 |
| | 调用栈 | 函数执行顺序的记录 |
| | 宏任务（MacroTask） | `setTimeout`、`setInterval`、I/O、UI渲染 |
| | 微任务（MicroTask） | `Promise.then`、`MutationObserver`、`queueMicrotask` |
| | 执行顺序 | 先同步，再微任务，再宏任务 |
| | 回调函数 | 将函数作为参数，在异步操作完成后执行 |
| | 回调地狱 | 多层嵌套导致代码难以维护 |
| | Promise 状态：pending、fulfilled、rejected | 不可逆的状态机 |
| | Promise 构造函数：`new Promise((resolve, reject) => {})` | 创建 Promise 对象 |
| | `.then()` 方法 | 注册成功回调，返回新 Promise |
| | `.catch()` 方法 | 注册失败回调，返回新 Promise |
| | `.finally()` 方法 | 无论成功失败都会执行 |
| | 链式调用 | `.then()` 返回 Promise 可以继续 `.then()` |
| | 值传递 | `.then()` 回调的返回值会传递给下一个 |
| | 错误冒泡 | 错误会沿着链向下找最近的 `.catch()` |
| | `Promise.resolve()` | 返回一个成功的 Promise |
| | `Promise.reject()` | 返回一个失败的 Promise |
| | `Promise.all()` | 全部成功则成功，一个失败则失败 |
| | `Promise.race()` | 第一个完成的 Promise 决定结果 |
| | `Promise.allSettled()` | 等待所有完成，返回每个的结果 |
| | `async` 函数 | 返回 Promise，内部可以使用 `await` |
| | `await` 表达式 | 等待 Promise 完成，并获取结果 |
| | `try...catch` 处理 `async/await` 错误 | 捕获异步错误 |
| | 并行执行：`await Promise.all([...])` | 同时发起多个异步任务 |

#### **2.6 模块化**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **模块化** | CommonJS 规范（Node.js） | `require` 导入，`module.exports` 导出 |
| | ES Module 规范 | `import` 导入，`export` / `export default` 导出 |
| | 默认导出 vs 命名导出 | 一个模块只能有一个默认导出，可以有多个命名导出 |
| | 导入别名：`import { a as b }` | 重命名导入 |
| | 整体导入：`import * as obj from 'module'` | 将所有导出作为一个对象 |
| | 动态导入：`import('module')` | 返回 Promise，实现代码分割 |

#### **2.7 其他重要概念**

| **模块**      | **详细知识点**                                                              | **需要理解的核心**                    |
| :---------- | :--------------------------------------------------------------------- | :----------------------------- |
| **this 绑定** | 默认绑定：非严格模式下指向全局对象，严格模式下为 `undefined`                                   | 函数独立调用                         |
|             | 隐式绑定：作为对象方法调用，`this` 指向该对象                                             | 调用位置决定                         |
|             | 显式绑定：`call`、`apply`、`bind`                                             | 手动指定 `this`                    |
|             | `new` 绑定：作为构造函数调用，`this` 指向新创建的对象                                      | 优先级最高                          |
|             | 箭头函数：没有自己的 `this`，继承外层作用域的 `this`                                      | 词法作用域                          |
| **严格模式**    | `"use strict"` 指令                                                      | 消除 JavaScript 一些不合理之处，启用更严格的检查 |
| **错误处理**    | `try...catch...finally`                                                | 捕获并处理异常                        |
|             | `throw` 语句                                                             | 主动抛出错误                         |
|             | `Error` 对象及其子类型                                                        | 不同类型的错误                        |
| **正则表达式**   | 创建：字面量 `/pattern/`、`new RegExp()`                                      | 两种方式                           |
|             | 常用方法：`test()`、`exec()`、字符串的 `match()`、`replace()`、`search()`、`split()` | 使用场景                           |
|             | 元字符、量词、分组、断言                                                           | 基本语法                           |
- **2.7 其他重要概念** 的补充：
    - `WeakMap` 和 `WeakSet`（内存管理相关）
    - `Proxy` 和 `Reflect`（元编程）
    - `Generator` 函数和 `yield`（迭代器协议）
    - `Symbol` 的实际应用场景

---

### **⚛️ 第三阶段：React 核心原理（Web 基础）**

> **目标**：深入理解 React 的组件化思想、核心 API 及其背后的设计哲学，为 React Native 的学习打下坚实的概念基础。

#### **3.1 JSX**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **JSX** | JSX 语法规则 | JSX 是 `React.createElement` 的语法糖 |
| | 嵌入表达式 `{ }` | 在 JSX 中使用 JavaScript 表达式 |
| | 属性命名：camelCase | 如 `className`、`onClick` |
| | 子元素 | JSX 可以嵌套子元素 |
| | 片段 `<></>` 或 `<React.Fragment>` | 返回多个元素而不添加额外节点 |
- 补充 **3.13 错误边界（Error Boundaries）**
    - `componentDidCatch` 和 `getDerivedStateFromError`
    - 错误边界的使用场景和限制
- 补充 **3.14 Portals**
    - `ReactDOM.createPortal` 的使用场景（模态框、提示框）
#### **3.2 组件**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **组件** | 函数组件 | 接收 `props` 返回 JSX，最简单 |
| | 类组件 | 继承 `React.Component`，拥有 `render` 方法、状态、生命周期 |
| | 组件组合 | 组件可以嵌套使用，构建复杂 UI |
| | 受控组件 vs 非受控组件 | 表单值由 React 状态控制 vs 由 DOM 自身控制 |

#### **3.3 Props**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **Props** | 传递数据 | 父组件通过属性向子组件传递数据 |
| | 只读性 | 子组件不能修改 props |
| | 默认属性：`defaultProps` | 为 props 提供默认值 |
| | 类型检查：`propTypes` | 开发时校验 props 类型，有助于捕获错误 |
| | 子组件向父组件通信 | 父组件传递回调函数，子组件调用并传递数据 |

#### **3.4 State**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **State** | `useState` 语法 | `const [state, setState] = useState(initialState)` |
| | 声明状态 | 在函数组件顶层调用 |
| | 更新状态 | `setState(newState)` 或 `setState(prev => newState)` |
| | 状态不可变性 | 不能直接修改 state，必须通过 setter 替换 |
| | 状态合并 | 类组件中 `setState` 会自动合并，函数组件需手动合并对象 |
| | 惰性初始化 | `useState` 可以传入函数，在初次渲染时执行 |

#### **3.5 生命周期与副作用**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **useEffect** | 基本语法 | `useEffect(() => { ... }, [dependencies])` |
| | 执行副作用 | 数据获取、订阅、手动修改 DOM、设置定时器等 |
| | 依赖数组的作用 | 控制 `useEffect` 何时执行 |
| | 无依赖：每次渲染后执行 | 相当于 `componentDidUpdate` + `componentDidMount` |
| | 空数组 `[]`：只执行一次 | 相当于 `componentDidMount` |
| | 有依赖：依赖变化时执行 | 相当于 `componentDidUpdate`（特定依赖） |
| | 清理函数 | `useEffect` 可以返回一个函数，在组件卸载或下次 effect 前执行，用于清除订阅、定时器等 |
| | `useLayoutEffect` | 与 `useEffect` 类似，但在浏览器绘制前同步执行，用于读取布局、避免闪烁 |
| | 类组件生命周期 | `componentDidMount`、`componentDidUpdate`、`componentWillUnmount` 等（了解即可） |

#### **3.6 上下文**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **Context** | `createContext` | 创建上下文对象，包含 `Provider` 和 `Consumer` |
| | `Context.Provider` | 提供值给后代组件，接收 `value` 属性 |
| | `useContext` | 在函数组件中消费上下文的值 |
| | `Context.Consumer` | 在类组件或需要 JSX 中消费上下文（较少用） |
| | 更新上下文 | 通过 Provider 的 value 变化触发消费组件更新 |

#### **3.7 Refs**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **Refs** | `useRef` | 创建 ref 对象，`.current` 属性可存储任意值，且改变不会触发重新渲染 |
| | 访问 DOM 元素 | 将 ref 赋值给元素的 `ref` 属性，可以获取真实 DOM 节点 |
| | 保存可变值 | 用于保存跨渲染周期的数据，如定时器 ID、前一个 props |
| | `forwardRef` | 将 ref 从父组件传递到子组件内部的 DOM 元素或组件实例 |
| | `useImperativeHandle` | 自定义暴露给父组件的实例值或方法 |

#### **3.8 性能优化**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **性能优化** | `React.memo` | 高阶组件，对函数组件进行浅比较 props，避免不必要的重渲染 |
| | `useMemo` | 缓存计算结果，依赖项未变时跳过重新计算 |
| | `useCallback` | 缓存函数，避免子组件因函数引用变化而重渲染 |
| | `useMemo` 与 `useCallback` 的区别 | `useMemo` 返回值，`useCallback` 返回函数 |
| | 避免内联对象/函数 | 每次渲染创建新对象/函数会导致子组件重渲染 |
| | 列表项使用稳定的 `key` | 帮助 React 识别元素变化，减少重绘 |
| | 虚拟化长列表 | 使用 `react-window` 或 `react-virtualized` |

#### **3.9 自定义 Hook**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **自定义 Hook** | 规则 | 名称以 `use` 开头，只能在函数组件或自定义 Hook 顶层调用 |
| | 提取逻辑 | 将组件逻辑封装为可复用的函数 |
| | 示例：`useLocalStorage` | 将状态同步到 `localStorage` |
| | 示例：`useFetch` | 封装数据获取逻辑，处理 loading、error、data |
| | 组合多个 Hook | 自定义 Hook 内部可以使用其他 Hook |

#### **3.10 条件渲染与列表渲染**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **条件渲染** | `if` 语句 | 在函数组件内部使用，返回不同 JSX |
| | 三元运算符 `? :` | 简洁的条件渲染 |
| | 逻辑与 `&&` | 当条件为 true 时渲染右侧元素 |
| | 逻辑或 `||` | 用于默认值（但注意 0 的情况） |
| **列表渲染** | 使用 `map()` 生成元素 | 遍历数组，为每个元素返回 JSX |
| | `key` 属性 | 帮助 React 识别哪些项被修改/添加/删除，必须是稳定唯一的值（如 id） |
| | 不要用索引作为 key（除非列表静态且不会重新排序） | 索引会导致性能问题和状态错误 |

#### **3.11 事件处理**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **事件处理** | 合成事件（SyntheticEvent） | React 封装的事件系统，具有跨浏览器一致性 |
| | 事件绑定 | 如 `onClick={handleClick}`，直接传递函数 |
| | 事件对象 `e` | 合成事件对象，包含原生事件属性和方法 |
| | 阻止默认行为 | `e.preventDefault()` |
| | 阻止冒泡 | `e.stopPropagation()` |
| | 向事件处理函数传递额外参数 | 使用箭头函数包裹：`onClick={() => handleClick(id)}` |
| | 事件池（React 16 及之前） | 合成事件对象会被复用，异步访问需要调用 `e.persist()`（17+ 已弃用） |

#### **3.12 表单**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **表单** | 受控组件 | 表单元素的值由 React state 控制，通过 `onChange` 更新 state |
| | 非受控组件 | 使用 `ref` 从 DOM 直接获取值，不通过 state |
| | 处理多个输入 | 给每个输入指定 `name`，使用计算属性名更新 state |
| | 选择框 `<select>` | 通过 `value` 属性控制选中项 |
| | 复选框 `<input type="checkbox">` | 通过 `checked` 属性控制 |
| | 文件输入 `<input type="file">` | 非受控组件，用 ref 获取文件对象 |

---

### **📱 第四阶段：React Native 核心深度剖析**

> **目标**：掌握 React Native 的核心组件、导航、网络和存储，并理解其跨平台渲染原理。

#### **4.1 架构与原理**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **架构概览** | JavaScript 引擎（Hermes / JavaScriptCore） | 执行所有 JS 代码（业务逻辑、React 核心等）的环境 |
| | 原生线程（主线程/UI 线程） | 负责原生 UI 渲染和用户交互 |
| | 桥接（Bridge） | 早期 RN 的通信方式，JS 线程和原生线程通过异步队列传递消息 |
| | JSI（JavaScript Interface） | 新架构中的同步通信方式，允许 JS 直接持有 C++ 对象的引用 |
| | Fabric 渲染器 | 新架构中的渲染系统，让渲染更同步、性能更好 |
| | Turbo Modules | 新架构中 Native 模块的懒加载机制 |
| **渲染原理** | React Native 如何将 `<View>` 映射为原生控件 | JSX → Shadow Tree → Yoga 布局 → 原生视图（UIView / ViewGroup） |
| | Yoga 布局引擎 | 跨平台的 Flexbox 布局实现，将布局结果转换为原生坐标 |
| | Shadow Tree | 在 JS 和原生之间的一层，用于布局计算 |
| **开发模式** | Expo | 一套工具链和托管服务，简化开发，适合大部分应用，无需原生代码 |
| | Expo Go | 快速预览和调试，但无法使用需要自定义原生代码的模块 |
| | React Native CLI | 原生代码暴露给开发者，可以完全控制原生部分，适合需要自定义原生模块的项目 |

#### **4.2 开发环境搭建（Expo 路线）**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **环境搭建** | Node.js 安装 | 选择 LTS 版本，确保稳定 |
| | 安装 Expo CLI | `npm install -g expo-cli` |
| | 创建项目 | `expo init my-app`，选择模板 |
| | 项目结构 | `App.js` 入口，`app.json` 配置，`assets` 资源文件夹 |
| | 运行项目 | `expo start` 或 `npm start`，打开 Metro 打包器 |
| | 真机预览 | 安装 Expo Go 应用，扫码预览 |
| | 模拟器配置 | Android Studio（安卓模拟器）或 Xcode（iOS 模拟器）的配置 |
| | 清理缓存 | `expo start -c` 清除 Metro 缓存 |

#### **4.3 核心组件系统**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **View** | 最基础的容器组件 | 支持 Flexbox 布局、触摸事件、样式 |
| | 常用属性：`style`、`onLayout`、`hitSlop`、`pointerEvents` | 控制布局和交互区域 |
| **Text** | 显示文本 | 唯一可以直接显示文本的组件 |
| | 嵌套文本 | `Text` 内部可以嵌套 `Text`，子 `Text` 可以覆盖样式 |
| | 常用属性：`numberOfLines`、`onPress`、`selectable` | 控制文本显示和交互 |
| | 样式继承 | 在 `Text` 内部，子 `Text` 会继承父 `Text` 的样式 |
| **Image** | 显示图片 | 支持本地图片（`require`）和网络图片（`{uri: '...'}`） |
| | 本地图片：`require('./path.png')` | 打包时会被包含进应用 |
| | 网络图片：`{ uri: 'https://...' }` | 需指定宽高，否则不显示 |
| | 属性：`resizeMode`（cover、contain、stretch、center、repeat） | 控制图片适应方式 |
| | 缓存策略：`cache` 属性 | 仅对 iOS 有效，安卓默认缓存 |
| **ScrollView** | 可滚动的容器 | 所有内容一次性渲染，适合内容较少的情况 |
| | 属性：`horizontal`（水平滚动）、`pagingEnabled`（分页）、`showsVerticalScrollIndicator` | 控制滚动行为 |
| | 滚动事件：`onScroll`、`onScrollBeginDrag` 等 | 监听滚动状态 |
| **FlatList** | 高性能列表组件 | 用于长列表渲染，支持懒加载和回收复用 |
| | 必须属性：`data`（数组）、`renderItem`（渲染函数）、`keyExtractor`（提取唯一 key） | 基本配置 |
| | 优化属性：`getItemLayout`（指定每个项的高度，避免动态计算）、`removeClippedSubviews`（移出屏幕的组件不渲染） | 性能优化 |
| | 刷新控制：`onRefresh`、`refreshing` | 实现下拉刷新 |
| | 加载更多：`onEndReached`、`onEndReachedThreshold` | 实现上拉加载 |
| | 空状态：`ListEmptyComponent` | 列表为空时显示的内容 |
| | 分割线：`ItemSeparatorComponent` | 自定义项之间的分割线 |
| | 头部和尾部：`ListHeaderComponent`、`ListFooterComponent` | 添加列表头部/尾部 |
| **SectionList** | 分组列表 | 类似通讯录，支持分组标题 |
| | 必须属性：`sections`（数组，每个对象包含 `title` 和 `data`）、`renderItem`、`renderSectionHeader` | 基本配置 |
| **TextInput** | 文本输入框 | 接收用户输入 |
| | 属性：`value`、`onChangeText`、`placeholder`、`secureTextEntry`（密码）、`keyboardType`（键盘类型）、`multiline`（多行） | 控制输入行为和样式 |
| | 焦点管理：`autoFocus`、`onFocus`、`onBlur`、`ref` + `focus()` | 控制焦点 |
| **Button** | 简单按钮 | 样式有限，适合简单场景 |
| | 属性：`title`、`onPress`、`disabled`、`color` | 基本功能 |
| **TouchableOpacity** | 触摸反馈组件 | 按下时透明度变化，常用 |
| | 属性：`onPress`、`onLongPress`、`disabled`、`activeOpacity` | 控制触摸响应 |
| **Pressable** | 更灵活的触摸组件 | 可以检测按压、悬停等状态 |
| | 属性：`onPress`、`onPressIn`、`onPressOut`、`onLongPress`、`hitSlop`、`pressRetentionOffset` | 精细控制触摸区域和反馈 |
| | 状态回调：`children` 可以是函数，接收 `{ pressed }` 状态 | 根据状态改变样式 |
- **4.3 核心组件系统** 中补充：
    
    - `Modal` 组件（模态框）
    - `Switch` 组件（开关）
    - `ActivityIndicator`（加载指示器）
    - `KeyboardAvoidingView`（键盘避让）
    - `SafeAreaView`（安全区域）
    - `StatusBar`（状态栏控制）
#### **4.4 样式**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **StyleSheet** | `StyleSheet.create` | 创建样式对象，优化性能，提供类型提示 |
| | 内联样式 | 直接在 `style` 属性中写对象，性能稍差，但适合动态样式 |
| | 合并样式 | 可以传入数组，后面的覆盖前面的 |
| **Flexbox** | 默认主轴：`column`（垂直） | 与 Web 默认的 `row` 不同 |
| | 容器属性：`flexDirection`、`flexWrap`、`justifyContent`、`alignItems`、`alignContent` | 用法同 CSS |
| | 项目属性：`flex`（数字，表示占据比例）、`alignSelf`、`flexGrow`、`flexShrink`、`flexBasis` | 用法同 CSS |
| | 注意：`justifyContent: 'space-evenly'` 在 RN 中不支持（需用 space-between 模拟） | 平台差异 |
| **尺寸单位** | 无单位数字 | 表示逻辑像素（dp/pt），自动适配不同屏幕 |
| | 百分比 | 相对于父容器的宽高 |
| | `Dimensions` API | 获取屏幕宽高：`Dimensions.get('window')` |
| | `PixelRatio` API | 获取像素密度，处理 1px 边框等问题 |
| **Platform 特定样式** | `Platform.OS` | 判断当前平台：`ios` 或 `android` |
| | `Platform.select` | 根据平台返回不同值：`Platform.select({ ios: {...}, android: {...} })` |
| | 文件扩展名 | 可以创建 `.ios.js` 和 `.android.js` 文件，自动加载对应平台的代码 |
| **样式继承** | Text 组件会继承父 Text 的样式 | 但 View 不会继承任何样式 |
| | 全局样式 | 可以通过自定义组件或主题库实现 |

#### **4.5 导航**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **React Navigation** | 安装核心库和依赖 | `@react-navigation/native`、`@react-navigation/native-stack`、`react-native-screens`、`react-native-safe-area-context` 等 |
| | 导航容器 | `NavigationContainer` 包裹整个应用 |
| **Stack Navigator** | `createNativeStackNavigator` | 创建栈导航器 |
| | `Navigator` 和 `Screen` | 定义导航结构和各个屏幕 |
| | 屏幕跳转：`navigation.navigate('ScreenName', { params })` | 传递参数 |
| | 返回：`navigation.goBack()`、`navigation.popToTop()` | 返回上一页或首页 |
| | 参数接收：`route.params` | 在屏幕组件中获取参数 |
| | 配置：`options` 属性（标题、头样式、返回按钮等） | 自定义屏幕头部 |
| **Bottom Tab Navigator** | `createBottomTabNavigator` | 创建底部标签导航器 |
| | 配置图标：结合 `react-native-vector-icons` 或自定义图标组件 | 为每个标签设置图标和标签 |
| | 监听焦点：`useFocusEffect`、`navigation.addListener('focus')` | 在标签获得焦点时执行操作 |
| **Drawer Navigator** | `createDrawerNavigator` | 创建侧边栏导航器 |
| | 配置抽屉内容：自定义抽屉组件 | 可以包含用户信息、菜单项等 |
| **嵌套导航** | 在 Tab 内嵌套 Stack | 常见模式：Tab 页内还有页面栈 |
| | 导航器之间的通信 | 可以通过 `navigation` 对象或全局状态管理 |
| **导航事件** | `navigation.addListener` | 监听 `focus`、`blur`、`beforeRemove` 等事件 |

#### **4.6 网络请求**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **Fetch API** | `fetch(url, options)` | 返回 Promise |
| | 常用选项：`method`、`headers`、`body` | 配置请求 |
| | 处理响应：`res.json()`、`res.text()` | 解析响应体 |
| | 错误处理：网络错误（如断网）会进入 `catch`；HTTP 错误状态码（如 404）不会进入 `catch`，需要检查 `res.ok` | 需要区分两类错误 |
| **Axios** | 安装 `axios` | 第三方 HTTP 客户端 |
| | 优势：自动转换 JSON、拦截器、取消请求、更好的错误处理 | 相比 fetch 更强大 |
| | 基本用法：`axios.get(url)`、`axios.post(url, data)` | 返回 Promise |
| | 错误处理：HTTP 错误状态码会进入 `catch` | 与 fetch 不同 |
| **加载与错误状态** | 使用 `useState` 管理 `loading`、`error`、`data` | 典型的三状态管理 |
| | 显示加载指示器：`ActivityIndicator` 组件 | 告知用户正在加载 |
| | 显示错误提示：`Text` 或弹窗，提供重试功能 | 友好的错误处理 |
| | 使用 `useEffect` 触发请求 | 在组件挂载或依赖变化时发起请求 |
| **请求取消** | `AbortController`（fetch）或 axios 的 `CancelToken` | 在组件卸载时取消请求，避免内存泄漏 |

#### **4.7 本地存储**

| **模块**           | **详细知识点**                                                           | **需要理解的核心**        |
| :--------------- | :------------------------------------------------------------------ | :----------------- |
| **AsyncStorage** | 安装 `@react-native-async-storage/async-storage`                      | 社区维护的存储库           |
|                  | 方法：`setItem(key, value)`、`getItem(key)`、`removeItem(key)`、`clear()` | 基本操作               |
|                  | 存储限制：约 6 MB（不同平台略有差异）                                               | 适合存储简单数据，不适合存储大量数据 |
|                  | 异步操作                                                                | 所有方法返回 Promise     |
|                  | 数据序列化：只能存储字符串，存储对象需 `JSON.stringify`/`parse`                        | 注意转换               |
| **安全存储**         | `expo-secure-store` 或 `react-native-keychain`                       | 存储敏感信息（如 token）    |
- 补充 **4.8 动画系统**
    
    - `Animated` API：`Animated.Value`、`Animated.timing`、`Animated.spring`
    - 组合动画：`parallel`、`sequence`、`stagger`
    - `LayoutAnimation`（布局动画）
    - `Reanimated` 库（高性能动画方案）
    - `useNativeDriver` 的使用和限制
- 补充 **4.9 手势系统**
    
    - `PanResponder` API
    - `react-native-gesture-handler` 库
    - 常见手势：滑动、拖拽、捏合、旋转
- **4.10 常见 UI 模式实现**
    - 下拉刷新和上拉加载
    - 无限滚动列表
    - 轮播图
    - 底部弹出菜单
    - 搜索框和筛选
    - 骨架屏
---

### **🗂️ 第五阶段：状态管理深度解析**

> **目标**：在应用规模变大时，能够合理、高效地管理复杂的全局或共享状态。

#### **5.1 Context API + useReducer**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **Context API** | `React.createContext` | 创建上下文对象 |
| | `Context.Provider` | 在组件树顶层提供值，接收 `value` 属性 |
| | `useContext` | 在函数组件中消费上下文的值 |
| | `Context.Consumer` | 在类组件或需要 JSX 中消费上下文（较少用） |
| | 更新 Context | Provider 的 value 变化时，所有消费组件会重新渲染 |
| | 性能优化：拆分 Context | 避免一个大 Context 导致无关组件重渲染 |
| **useReducer** | 语法：`const [state, dispatch] = useReducer(reducer, initialState)` | 管理复杂状态逻辑 |
| | `reducer` 函数：`(state, action) => newState` | 纯函数，根据当前 state 和 action 返回新 state |
| | `action` 通常包含 `type` 和 `payload` | 描述发生了什么 |
| | 适用场景：多个状态相互关联，或状态更新逻辑复杂 | 替代多个 `useState` |
| **结合使用** | 将 `dispatch` 通过 Context 传递 | 实现全局状态管理，类似 Redux 的轻量版 |
| | 将 `state` 也通过 Context 传递 | 全局状态可被任何组件访问 |
| | 示例：实现一个全局的购物车状态 | 增删改查通过 `dispatch` 触发 |

#### **5.2 Redux Toolkit (RTK)**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **核心概念** | Store（单一数据源） | 整个应用的状态存储在一个对象树中 |
| | Action（描述事件） | 一个包含 `type` 和 `payload` 的普通对象 |
| | Reducer（根据 action 更新 state） | 纯函数，接收当前 state 和 action，返回新 state |
| | Dispatch（触发 action） | 通过 `dispatch(action)` 发送 action 给 reducer |
| | Selector（从 store 中提取数据） | 函数，用于获取部分 state |
| **安装依赖** | `@reduxjs/toolkit`、`react-redux` | 安装核心库和 React 绑定 |
| **配置 Store** | `configureStore` | 自动组合 reducers、添加中间件（thunk）、启用 Redux DevTools |
| **创建 Slice** | `createSlice` | 接收 name、initialState、reducers，自动生成 action creators |
| | reducers 中的函数 | 可以直接“修改”状态（Immer 库支持不可变更新） |
| | 导出 slice 的 reducer 和 actions | 在 store 中使用 reducer，在组件中使用 actions |
| **在组件中使用** | `Provider` 包裹根组件 | 将 store 提供给 React 组件树 |
| | `useSelector` | 从 store 中读取数据，订阅更新 |
| | `useDispatch` | 获取 dispatch 函数，用于触发 actions |
| **异步逻辑** | Redux Thunk（RTK 内置） | 允许 action creator 返回函数，处理异步操作 |
| | `createAsyncThunk` | 简化异步请求，自动生成 pending/fulfilled/rejected 三种 action |
| | 处理异步生命周期 | 在 extraReducers 中监听这些 action，更新状态 |
| **中间件** | 中间件概念 | 在 dispatch action 和到达 reducer 之间进行扩展，如日志、异步 |
| | 自定义中间件 | 了解基本结构即可 |

#### **5.3 其他状态管理方案**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **Zustand** | 轻量级状态管理库 | 更简单的 API，无需 Provider |
| | 创建 store：`create(set => ({ ... }))` | 返回自定义 Hook |
| | 使用：`const { count, increment } = useStore()` | 直接使用，自动订阅 |
| **MobX** | 响应式状态管理 | 通过 observable 和 observer 实现自动更新 |
- **5.3 其他状态管理方案** 中补充：
    - `Jotai`（原子化状态管理）
    - `Recoil`（Facebook 出品）
    - 各方案的对比和选择建议
---

### **🔌 第六阶段：原生功能与设备能力**

> **目标**：让 App 能够调用手机的原生硬件和系统能力，如相机、相册、定位等。

#### **6.1 权限请求**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **权限模型** | Android 权限系统 | 安装时权限组（旧） vs 运行时权限（6.0+） |
| | iOS 权限系统 | 首次使用时会弹窗询问，需要在 Info.plist 中添加描述 |
| **Expo 权限** | `expo-permissions`（老版） | 已逐步废弃，被各模块自带的权限请求取代 |
| | 模块自带权限请求：如 `expo-camera` 的 `requestCameraPermissionsAsync` | 在调用功能前请求权限 |
| | 检查权限状态：`getCameraPermissionsAsync` | 了解用户是否已授权 |
| **react-native-permissions** | 安装和配置 | 适用于 React Native CLI 项目 |
| | 请求权限：`check`、`request` | 跨平台统一 API |
| | 处理权限结果 | 根据用户选择给出反馈 |

#### **6.2 常用原生模块**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **相机 (expo-camera)** | 安装和配置 | `expo install expo-camera` |
| | 请求权限 | `Camera.requestCameraPermissionsAsync()` |
| | `Camera` 组件 | 显示相机预览，接受 `ref` 控制拍照 |
| | 拍照：`cameraRef.takePictureAsync(options)` | 返回照片对象，包含 uri |
| | 录制视频：`recordAsync()`、`stopRecording()` | 录制视频 |
| | 相机设置：`type`（前后置）、`flashMode`、`zoom` | 控制相机行为 |
| **图片选择 (expo-image-picker)** | 安装 | `expo install expo-image-picker` |
| | 请求权限（相册/相机） | `requestMediaLibraryPermissionsAsync()`、`requestCameraPermissionsAsync()` |
| | 从相册选择：`launchImageLibraryAsync(options)` | 返回选择的图片信息 |
| | 拍照：`launchCameraAsync(options)` | 调用相机拍照 |
| | 选项：`allowsEditing`（是否允许裁剪）、`quality`（图片质量 0-1）、`base64`（是否返回 base64） | 自定义选取行为 |
| **地理位置 (expo-location)** | 安装 | `expo install expo-location` |
| | 请求权限 | `requestForegroundPermissionsAsync()` |
| | 获取当前位置：`getCurrentPositionAsync(options)` | 单次获取 |
| | 监听位置变化：`watchPositionAsync(options, callback)` | 持续监听，返回订阅对象用于移除 |
| | 地理编码：`geocodeAsync(address)` 和 `reverseGeocodeAsync(location)` | 地址 ↔ 经纬度转换 |
| **本地通知 (expo-notifications)** | 安装和配置 | `expo install expo-notifications`，需配置推送证书（iOS） |
| | 请求权限 | `requestPermissionsAsync()` |
| | 安排通知：`scheduleNotificationAsync` | 指定触发时间、内容、数据 |
| | 处理通知点击 | 通过 `addNotificationResponseReceivedListener` 监听 |
| **振动 (expo-haptics / Vibration)** | `expo-haptics` | 提供精细的触觉反馈（iOS 和部分 Android） |
| | `Vibration.vibrate()` | 简单振动（跨平台） |
| **文件系统 (expo-file-system)** | 读写文件 | 操作应用沙盒内的文件 |

#### **6.3 原生模块原理（了解）**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **平台通道** | MethodChannel（RN 旧版） | JS 通过桥接调用原生方法，异步 |
| | JSI（新架构） | JS 直接持有 C++ 对象，同步调用 |
| | 编写自定义原生模块 | 了解流程即可，实际开发中很少需要 |

---

### **📊 第七阶段：性能优化与调试**

> **目标**：让 App 运行流畅（不卡顿）、不崩溃，并能够熟练使用工具定位和解决性能问题。

#### **7.1 调试工具**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **Chrome DevTools** | 通过“Debug JS Remotely”打开 | 在 Chrome 中调试 JS 代码 |
| | Console 面板 | 查看日志、执行代码 |
| | Sources 面板 | 断点调试、查看调用栈 |
| | Network 面板 | 查看网络请求、响应 |
| **React Native Debugger** | 独立应用 | 集成了 React DevTools 和 Redux DevTools |
| **Flipper** | Facebook 官方调试工具 | 支持日志、网络、布局、性能、内存等 |
| | 安装 Flipper 和桌面客户端 | 查看应用状态 |
| | 插件：React DevTools、Prefetch、Network 等 | 扩展功能 |
| **React DevTools** | 组件树查看 | 检查组件的 props、state、hooks |
| | 高亮更新 | 查看哪些组件在重渲染 |

#### **7.2 渲染优化**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **避免不必要的重渲染** | `React.memo` | 对函数组件进行浅比较 props |
| | `useCallback` | 缓存函数，避免子组件因函数引用变化而重渲染 |
| | `useMemo` | 缓存计算结果 |
| | 使用 `key` 帮助识别元素 | 稳定唯一 key 避免不必要重建 |
| **列表优化** | `FlatList` 的 `getItemLayout` | 指定每个项的高度/宽度，避免动态计算 |
| | `removeClippedSubviews` | 移出屏幕的组件不渲染 |
| | `maxToRenderPerBatch`、`windowSize` | 控制渲染批次和可视区域 |
| | `initialNumToRender` | 控制初始渲染条数 |
| **图片优化** | 使用缩略图 | 根据显示尺寸请求合适大小的图片 |
| | 图片缓存：`expo-image` 或 `react-native-fast-image` | 避免重复下载 |
| | 图片格式：WebP（Android 支持好，iOS 14+ 支持） | 减少图片体积 |
| | 图片懒加载：`react-native-lazy-load` | 图片进入可视区域再加载 |
| **动画优化** | 使用 `Animated` 库时开启 `useNativeDriver: true` | 将动画交给原生线程执行，避免阻塞 JS 线程 |
| | 避免在动画中执行复杂计算 | 在 `useEffect` 或事件处理中提前计算 |
| | 使用 `InteractionManager` | 延迟执行非关键任务，避免干扰动画 |
| **内存管理** | 清除定时器、取消网络请求、移除事件监听 | 在 `useEffect` 清理函数中进行 |
| | 避免闭包持有大量数据 | 注意函数内部引用外部变量 |
| | 图片释放 | 不需要时及时将图片引用置为 null |

#### **7.3 性能分析**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **Flipper 性能插件** | 查看 FPS（帧率） | 判断是否卡顿 |
| | 查看 UI 线程和 JS 线程占用 | 定位瓶颈在哪一线程 |
| **React Native 性能监视器** | 通过开发者菜单开启 | 显示 FPS、内存等 |
| **Hermes 引擎** | 启用 Hermes 可减少启动时间、内存占用 | 适用于 Android |
| **包体积分析** | `npx react-native bundle --entry-file index.js --platform android --dev false --bundle-output bundle.android --sourcemap-output sourcemap.android` 然后分析 sourcemap | 找出哪些模块占用空间大 |
- 补充 **7.4 启动优化**
    - 启动时间分析
    - 懒加载和代码分割
    - 预加载策略
    - Hermes 引擎的配置和优化
---

### **📦 第八阶段：打包、发布与工程化**

> **目标**：将开发完成的 App 打包成安装文件（APK/IPA），并上架到应用商店。

#### **8.1 Android 打包**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **生成签名密钥** | `keytool -genkey -v -keystore my-release-key.keystore -alias my-key-alias -keyalg RSA -keysize 2048 -validity 10000` | 生成用于签名的 keystore 文件 |
| | 保护密钥 | 不要提交到版本控制，妥善保管 |
| **配置签名** | 将 keystore 文件放到 `android/app` 目录 | 配置 `android/app/build.gradle` 中的 signingConfigs |
| | 创建 `gradle.properties` 存储密钥信息（可选） | 避免硬编码 |
| **生成 APK/AAB** | `cd android && ./gradlew assembleRelease` | 生成 APK 文件 |
| | `./gradlew bundleRelease` | 生成 AAB（Android App Bundle），推荐上传 Google Play |
| | 输出文件位置 | `android/app/build/outputs/apk/release/` 或 `bundle/release/` |
| **混淆** | 启用 ProGuard/R8 | 在 `build.gradle` 中设置 `minifyEnabled true`，减小包大小并混淆代码 |
| | 配置混淆规则 | 添加 `proguard-rules.pro` 文件，避免第三方库被错误混淆 |

#### **8.2 iOS 打包**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **Apple Developer 账号** | 注册账号（每年 99 美元） | 上架 App Store 必需 |
| **证书和描述文件** | 创建开发证书和发布证书 | 在 Apple Developer 网站或 Xcode 中管理 |
| | 创建 Bundle Identifier | 应用的唯一标识 |
| | 创建描述文件（Provisioning Profile） | 绑定开发者、App ID 和设备（开发）或分发方式（发布） |
| **Xcode 配置** | 打开项目中的 `ios/` 文件夹 | 用 Xcode 打开 `.xcworkspace`（如果使用 CocoaPods） |
| | 配置签名 | 在 Signing & Capabilities 中选择团队和描述文件 |
| | 配置版本号和构建号 | 在 General 或 Info.plist 中设置 |
| **Archive 并导出** | 选择目标设备为“Any iOS Device” | Product → Archive |
| | Organizer 窗口出现后，点击 Distribute App | 选择分发方式：App Store Connect、Development 等 |
| | 按提示选择描述文件和证书 | 最终导出 `.ipa` 文件 |
| **TestFlight 测试** | 在 App Store Connect 中上传构建版本 | 添加内部测试人员或外部测试人员 |

#### **8.3 上架流程**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **Google Play Console** | 创建应用 | 填写应用名称、默认语言、应用类型 |
| | 填写应用信息 | 说明、更新内容、分类、标签等 |
| | 上传截图、图标、特色图形 | 不同尺寸截图要求 |
| | 设置内容分级 | 填写问卷，系统自动评定分级 |
| | 设置定价和分发范围 | 免费还是付费，哪些国家可用 |
| | 发布到内部测试/封闭测试/开放测试 | 先进行测试，再发布到正式版 |
| **App Store Connect** | 创建应用记录 | 输入 Bundle ID、名称、SKU |
| | 填写应用信息 | 描述、关键词、支持网址、营销网址、隐私政策网址 |
| | 上传截图和预览视频 | 必须提供 5.5 英寸和 6.5 英寸 iPhone 截图，iPad 可选 |
| | 设置分级 | 填写问卷 |
| | 提交审核 | 等待审核，一般 1-3 天 |
| | 常见被拒原因 | 使用私有 API、缺乏隐私政策、界面不完整、有 bug 等 |

#### **8.4 热更新**

| **模块** | **详细知识点** | **需要理解的核心** |
| :--- | :--- | :--- |
| **CodePush** | 微软服务 | 允许动态更新 JS 和资源，无需重新打包 |
| | 安装 `react-native-code-push` | 配置原生部分 |
| | 发布更新：`appcenter codepush release-react` | 更新到 CodePush 服务器 |
| | 客户端集成：`codePush` 高阶组件 | 检查并应用更新 |
| **EAS Update** | Expo 官方服务 | Expo 应用的热更新解决方案 |
| | 配置 `eas.json` | 指定更新策略 |
| | 发布更新：`eas update` | 发布到 EAS 服务器 |
| | 客户端集成 | Expo 应用自动支持 |
- 补充 **8.5 持续集成/持续部署（CI/CD）**
    - GitHub Actions 配置
    - Fastlane 自动化打包
    - EAS Build（Expo 应用服务）
---
## 补充：

1. 将 **TypeScript** 单独作为一个阶段（在 JavaScript 之后，React 之前）：
	- TypeScript 基础语法
	- 类型系统：基础类型、联合类型、交叉类型
	- 接口和类型别名
	- 泛型
	- 在 React/RN 中使用 TypeScript

2. 在第七阶段或第八阶段前补充 **测试**：
	- Jest 单元测试
	- React Native Testing Library
	- E2E 测试（Detox 或 Appium）
	- 测试覆盖率

## **✅ 每周学习计划模板**

| 周 | 主攻方向 | 具体任务 | 代码练习 |
| :--- | :--- | :--- | :--- |
| 1 | 计算机基础 | 二进制、进程线程、网络分层、数组链表概念 | 手写进制转换、用伪代码模拟生产者-消费者 |
| 2 | 计算机基础 | 内存管理、文件系统、死锁 | 模拟页表地址转换、用代码实现 LRU 缓存 |
| 3 | 数据结构 | 栈、队列、树 | 手写栈和队列、实现二叉树的三种遍历 |
| 4 | 数据结构 | 图、查找、排序 | 实现图的 DFS/BFS、手写快速排序 |
| 5-7 | JavaScript 核心 | 变量、函数、对象、数组、闭包 | 用数组方法处理数据、手写 `map`/`filter`/`reduce` |
| 8-9 | JavaScript 进阶 | 异步（Promise/async/await）、事件循环 | 用 Promise 封装延时、分析代码执行顺序 |
| 10 | JavaScript 模块化 | ES Module、CommonJS | 将之前的代码拆分成模块，使用 `import`/`export` |
| 11-12 | React 基础 | JSX、函数组件、Props、State | 创建计数器、待办事项列表 |
| 13-14 | React Hooks | `useEffect`、`useContext`、`useRef` | 从 API 获取数据并展示、实现主题切换 |
| 15 | React 性能优化 | `React.memo`、`useMemo`、`useCallback` | 优化待办事项，避免不必要的重渲染 |
| 16 | RN 环境与基础 | 搭建 Expo 环境、运行 Hello World | 在真机上运行第一个 RN App |
| 17-18 | RN 核心组件 | `View`、`Text`、`Image`、`ScrollView`、`FlatList` | 用 `FlatList` 实现联系人列表，包含头像和姓名 |
| 19 | RN 样式与布局 | Flexbox 在 RN 中的应用 | 实现一个常见的 App 个人资料页布局 |
| 20-21 | RN 导航 | Stack、Tab 导航配置与跳转 | 搭建一个包含登录页、主页、详情页的多页面应用 |
| 22 | RN 网络与存储 | `fetch` 数据、`AsyncStorage` 读写 | 做一个新闻列表阅读器，并支持本地收藏 |
| 23-24 | 状态管理 | Context + useReducer 或 Redux Toolkit | 将新闻阅读器的收藏状态用 Redux 管理，并添加用户登录模拟 |
| 25 | 原生功能 | 权限请求、调用相机/相册、获取定位 | 做一个“随手拍”App，拍照并显示位置 |
| 26 | 性能优化 | 使用 React DevTools 和 Flipper 分析项目 | 分析自己 App 的渲染次数，并用 `React.memo` 优化 |
| 27 | 打包与发布 | 配置图标、生成签名、打包 APK | 将自己完成的一个 App 打包并在自己手机上安装 |
| 28+ | 完整项目迭代 | 构思一个完整项目，独立开发、测试、优化、打包 | 上线一个属于自己的小应用（或至少完成一个高质量的 Demo） |
