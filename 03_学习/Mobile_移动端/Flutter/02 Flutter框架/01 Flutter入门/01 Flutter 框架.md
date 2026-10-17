## 一、Flutter 简介与跨平台原理

### 1.1 Flutter 概述

#### 概念
- **是什么**：Flutter 是由 Google 开发并开源的一款**跨平台 UI 框架**（SDK），旨在通过**一套代码库**，帮助开发者构建能够运行在移动端（iOS、Android）、桌面端（Windows、macOS、Linux）以及 Web 平台上的**高性能、高保真**应用程序。
- **为什么需要**：传统跨平台开发存在性能瓶颈、不同平台UI不一致等问题，而原生开发需要为每个平台维护独立代码库，成本高昂。Google 创建 Flutter 的初衷是解决这些问题，创造能够美观、流畅且用户体验接近原生应用的应用程序。
- **解决什么问题**：允许代码在不同操作系统间复用，同时让应用能够直接与底层平台服务交互，实现既保持原生性能又能跨平台共享代码的目标。

#### 原理
- **底层实现**：Flutter 的核心是内嵌了高性能的 2D 图形渲染引擎 **Skia**（部分平台逐步过渡到 **Impeller**）。Flutter 将 UI 布局和控件直接绘制在 Skia 上，Skia 再直接与设备的 GPU 通信，将像素渲染到屏幕上。
- **关键技术点**：
  - **自绘渲染**：Flutter 不依赖平台原生控件，所有 UI 元素都由 Flutter 引擎自己绘制。
  - **统一代码路径**：所有绘制逻辑在 Engine 层实现，编译为平台原生代码，渲染核心不变，避免原生控件的碎片化。
  - **GPU 加速合成**：减少 CPU 开销，确保动画流畅度一致。

#### 使用方法
- **基本用法**：在项目中创建 Flutter 应用，编写 Dart 代码描述 UI，通过 `runApp()` 函数启动应用。
- **常见场景**：跨平台移动应用开发、桌面应用开发、Web 应用开发、嵌入式设备应用。
- **最佳实践**：优先阅读官方文档 [flutter.dev](https://flutter.dev) 与 [flutter.cn](https://flutter.cn)，配合 cookbook 与 CodeLab 循序渐进练习。

#### 注意事项
- **性能优化**：极端情况（如低端设备）可能影响性能，引擎通过动态调整（如降低图层复杂度）维持一致性。
- **Web 端特殊处理**：Web 采用不同的渲染路径（CanvasKit 与 Skwasm），与原生环境存在并发能力上的现实差异。

### 1.2 跨平台原理：自绘引擎

#### 概念
- **是什么**：Flutter 的跨平台能力基于**自绘引擎**实现，既不使用 WebView，也不通过"桥接"调用原生控件，而是提供了一套完整的、从零实现的跨平台技术栈。
- **为什么需要**：传统跨平台框架（如 React Native）依赖原生组件，容易导致碎片化和性能瓶颈。自绘引擎可以从根本上避免平台差异导致的样式不一致。
- **解决什么问题**：解决因依赖原生控件而导致的平台间 UI 差异、性能损耗和维护复杂性问题。

#### 原理
- **底层实现**：Flutter 引擎使用统一的代码库处理所有绘制操作。Widget 树被转换为 RenderObject 树，每个 RenderObject 负责计算自身的位置和大小（布局阶段），然后直接绘制到画布上（绘制阶段）。
- **工作流程**：
  1. **Widget 树**：描述 UI 的结构和样式
  2. **RenderObject 树**：负责布局和绘制的基类
  3. **Skia 图形库**：通过 Skia API 直接操作画布，执行绘图指令
  4. **GPU 合成**：图层在 GPU 上合成，使用硬件加速（OpenGL 或 Vulkan）
- **关键技术点**：
  - **自绘渲染**：按钮的绘制不依赖 Android 的 Button 或 iOS 的 UIButton，由 Flutter 引擎用相同算法渲染
  - **数学表达**：绘制坐标和变换使用矩阵运算，确保所有平台计算一致
  - **黄金测试**：Flutter 团队使用黄金测试（Golden Tests）比较不同平台的渲染输出，自动检测并修复偏差

#### 使用方法
- **基本用法**：开发者正常编写 Widget 代码，无需关心底层绘制差异，Flutter 自动处理跨平台渲染一致性。
- **常见场景**：复杂 UI（如渐变背景、自定义形状）中，Flutter 直接绘制，避免因系统主题导致的差异。

#### 注意事项
- **性能考量**：自绘引擎虽然保证一致性，但需要关注复杂 UI 在低端设备上的表现。
- **Impeller 演进**：Flutter 正持续集成 Impeller（新渲染后端），在 iOS、Android 与部分桌面平台上逐步替代 Skia，核心原理不变但性能更优。

### 1.3 核心优势

#### 概念
- **是什么**：Flutter 相比其他跨平台框架的独特优势和价值点。
- **为什么需要**：帮助开发者和企业在技术选型时理解 Flutter 的适用场景和核心竞争力。
- **解决什么问题**：克服传统跨平台方法的限制，提供高性能、高效率的开发体验。

#### 原理
- **底层实现**：
  - **一套代码，多端运行**：一份 Dart 代码通过 Flutter 框架编译，可生成兼容 Android、iOS、Web 和桌面的应用
  - **高性能**：AOT（预先编译）编译成原生 ARM 代码，通过 Skia 直接渲染，性能媲美原生应用
  - **高保真与一致体验**：UI 由 Skia 统一绘制，在不同平台保持绝对一致的视觉效果
  - **热重载**：利用 Dart 的 JIT 编译能力，实现有状态的热重载，代码修改几乎实时生效

- **关键技术点**：
  - **AOT + JIT**：发布模式使用 AOT 编译，开发模式使用 JIT 支持热重载
  - **开发效率提升**：使用 Flutter 开发时间通常会减少 30-40%
  - **单一代码库**：前后端只需一套 Dart 代码，无需切换语言

#### 使用方法
- **基本用法**：创建 Flutter 项目，在开发过程中充分利用热重载快速迭代，发布时构建 release 版本获得最佳性能。
- **常见场景**：初创公司快速将产品发布到多个平台，在有限时间和预算内将软件推向市场。
- **最佳实践**：
  - 开发阶段使用 Debug 模式 + 热重载
  - 性能测试使用 Profile 模式
  - 发布使用 Release 模式（AOT 编译）

#### 注意事项
- **热重载限制**：热重载只在 Debug 模式生效，Profile/Release 不提供该能力
- **构建模式差异**：调试构建下使用 JIT，可能因即时编译造成卡顿；Release 构建预编译为机器码，性能最佳

## 二、Flutter 三层架构

### 2.1 架构层级概览

#### 概念
- **是什么**：Flutter 被设计为一个**可扩展的分层系统**，由下至上主要由三层构成：Embedder（嵌入层）、Engine（引擎层）、Framework（框架层）。
- **为什么需要**：分层架构确保结构清晰、易于维护、组织灵活、数据安全，上层组件无法越权访问更底层的内容。
- **解决什么问题**：实现一次编码多平台适配，同时包容不同平台差异，让开发者用更少的代码做更多的事情。

#### 原理
- **底层实现**：每一层都构建在其下层之上，组件之间无法跨层访问。
- **关键技术点**：
  - **嵌入层**：平台特定适配器，负责 Engine 创建、管理与销毁，提供绘制 UI 的接口
  - **引擎层**：Flutter 核心，C++ 编写，提供图形绘制、文本布局、网络请求等核心 API
  - **框架层**：Dart 编写，开发者直接接触的层，提供 Material/Cupertino 组件、手势、动画等

#### 使用方法
- **基本用法**：开发者主要在 Framework 层工作，通过嵌入层将 Flutter 集成到各平台，引擎层在底层提供支持。
- **常见场景**：
  - **纯 Flutter 应用**：使用 Framework 层开发，通过嵌入层打包成各平台应用
  - **混合集成**：Flutter 代码可以通过嵌入层，以模块方式集成到现有应用中

#### 注意事项
- **平台差异**：嵌入层由适合当前平台的语言编写（Android 用 Java/C++，iOS/macOS 用 Objective-C/Objective-C++），需要了解各平台特性。

### 2.2 嵌入层 (Embedder)

#### 概念
- **是什么**：嵌入层是 Flutter 架构的最底层，是特定平台的适配层，负责将 Flutter 引擎"嵌入"到各个平台环境中。
- **为什么需要**：使 Flutter 应用程序能够运行在各种平台上，每个平台都有一个与之对应的嵌入层实现。
- **解决什么问题**：协调与底层操作系统的服务（渲染 surface、输入、线程管理等），提供统一的接入点。

#### 原理
- **底层实现**：由适合于当前平台的语言编写（Android 使用 Java 和 C++，iOS/macOS 使用 Objective-C/Objective-C++，Windows/Linux 使用 C++）。
- **工作流程**：
  1. 提供程序入口点
  2. 管理事件循环（Message Loop）
  3. 协调渲染表面、辅助功能、输入法等系统服务
  4. 最终由 Runner 打包成平台特定应用
- **关键技术点**：
  - **Android**：默认以 Activity 形式承载 FlutterView
  - **iOS/macOS**：以 UIViewController 或 NSViewController 方式集成
  - **Windows**：使用 ANGLE 转译到 DirectX

#### 使用方法
- **基本用法**：通常开发者无需直接操作嵌入层，由 Flutter 工具链自动生成各平台的 Runner 工程。
- **常见场景**：
  - **纯 Flutter 应用**：使用默认的嵌入层实现
  - **混合开发**：需要了解嵌入层 API，将 Flutter 集成到现有原生应用

#### 注意事项
- **平台特定代码**：当需要深度集成平台功能时，可能需要修改嵌入层代码。
- **线程管理**：嵌入层负责线程设置，需要理解各平台的线程模型。

### 2.3 引擎层 (Engine)

#### 概念
- **是什么**：引擎层是 Flutter 的核心关键，主要使用 C++ 编写，提供了 Flutter 的核心 API 的具体实现。
- **为什么需要**：作为 Flutter 的中枢，将底层 C++ 能力暴露给上层 Framework，同时执行实际的渲染、布局和计算任务。
- **解决什么问题**：封装底层平台差异，提供统一的图形绘制、文本布局、网络通信等基础能力。

#### 原理
- **底层实现**：引擎将底层的 C++ 代码包装成 Dart 代码，通过 `dart:ui` 库暴露给 Flutter 框架层。
- **核心组件**：
  - **Skia 图形引擎**：开源的 2D 图形库，负责将绘制指令栅格化，输出到屏幕
  - **Impeller**（演进中）：通过预编译着色器集合，减少运行时编译卡顿
  - **Dart 运行时**：支持 Dart 代码执行，包括垃圾回收、管理隔离区（Isolate）
  - **文本渲染**：通过文本引擎提供跨平台文本布局
- **关键技术点**：
  - **JIT/AOT 支持**：Debug 模式支持 JIT（热重载），Release 模式支持 AOT 编译
  - **dart:ui 库**：暴露最底层的原语，包括驱动输入、图形、文本渲染的类

#### 使用方法
- **基本用法**：开发者通过 Framework 间接使用引擎能力，通常无需直接调用 `dart:ui`。
- **常见场景**：
  - **自定义绘制**：高级场景可能需要直接使用 `dart:ui` 的 Canvas、Paint 等类
  - **性能调优**：了解引擎工作机制有助于优化应用性能

#### 注意事项
- **Web 端差异**：由于引擎是 C++ 编写，面向操作系统而非浏览器，Web 采用了不同的渲染路径（CanvasKit/Skwasm）。
- **渲染后端演进**：Impeller 正在成为 iOS、Android 默认路径，但 Skia 并未完全退出历史舞台。

### 2.4 框架层 (Framework)

#### 概念
- **是什么**：框架层是开发者最常接触的层，完全用 Dart 语言编写，提供了丰富的 API 用于构建用户界面。
- **为什么需要**：为开发者提供高层次的抽象，简化 UI 开发流程，避免直接操作底层引擎的复杂性。
- **解决什么问题**：让开发者能够通过声明式的方式快速构建美观、流畅的用户界面。

#### 原理
- **内部层次结构**（自底向上）：
  1. **基础层**：动画（animation）、绘画（painting）、手势（gestures）等基础服务抽象
  2. **渲染层 (Rendering)**：引入渲染树（Render Tree），处理布局和绘制逻辑
  3. **Widget 层**：提供不可变的类（Widget）配置和组合 UI，引入响应式编程模型
  4. **Material / Cupertino 库**：封装符合 Material Design 和 iOS 设计语言的控件

- **关键技术点**：
  - **dart:ui 绑定**：对 Engine 中 Skia 库的 C++ 接口的绑定，向上层提供 window、text、canvas 等通用绘制能力
  - **RenderObject**：真正绘制到屏幕的渲染对象，处理实际的布局和绘制

#### 使用方法
- **基本用法**：继承 `StatelessWidget` 或 `StatefulWidget`，实现 `build` 方法描述 UI。
- **常见场景**：
  - **基础组件**：使用 Text、Row、Column、Stack、Container 等
  - **Material 组件**：使用 MaterialApp、Scaffold、AppBar 等构建符合 Material Design 的应用
  - **手势处理**：使用 GestureDetector 检测用户输入

#### 代码示例
```dart
import 'package:flutter/material.dart';

void main() {
  runApp(const MyApp());
}

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Flutter Demo',
      theme: ThemeData(primarySwatch: Colors.blue),
      home: const MyHomePage(),
    );
  }
}

class MyHomePage extends StatefulWidget {
  const MyHomePage({super.key});

  @override
  State<MyHomePage> createState() => _MyHomePageState();
}

class _MyHomePageState extends State<MyHomePage> {
  int _counter = 0;

  void _incrementCounter() {
    setState(() {
      _counter++;
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Flutter Demo')),
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: [
            const Text('You have pushed the button this many times:'),
            Text('$_counter', style: Theme.of(context).textTheme.headlineMedium),
          ],
        ),
      ),
      floatingActionButton: FloatingActionButton(
        onPressed: _incrementCounter,
        tooltip: 'Increment',
        child: const Icon(Icons.add),
      ),
    );
  }
}
```

#### 注意事项
- **性能优化**：避免在 `build` 方法中执行耗时操作，使用 `const` 构造函数重用 widget 实例。
- **状态管理**：根据应用复杂度选择合适的 state 管理方案（setState、Provider、Bloc 等）。

## 三、为什么 Dart 适合 Flutter？

### 3.1 同时满足 JIT 与 AOT

#### 概念
- **是什么**：Dart 语言同时支持 JIT（Just-In-Time，即时编译）和 AOT（Ahead-Of-Time，预先编译）两种编译方式。
- **为什么需要**：开发阶段需要快速迭代（JIT），发布阶段需要高性能（AOT），Dart 能同时满足这两种需求。
- **解决什么问题**：在开发效率和运行时性能之间取得完美平衡。

#### 原理
- **底层实现**：
  - **JIT 编译**：在开发模式下，Dart 代码边编译边执行，支持热重载
  - **AOT 编译**：在发布模式下，Dart 代码直接编译为本地机器码，提高启动速度和运行性能
- **关键技术点**：
  - **调试构建**：使用 JIT，可能因即时编译造成卡顿
  - **Profile/Release 构建**：预编译为机器码，面向用户的最终交付形态

#### 使用方法
- **基本用法**：Flutter 工具链自动管理编译模式，`flutter run` 使用 JIT，`flutter build` 使用 AOT。
- **常见场景**：
  - **开发阶段**：Debug 模式 + JIT + 热重载
  - **测试阶段**：Profile 模式进行性能分析
  - **发布阶段**：Release 模式 + AOT 编译

#### 注意事项
- **热重载限制**：热重载只在 Debug 模式生效，Profile/Release 不提供该能力。
- **性能差异**：调试构建可能因 JIT 造成卡顿，最终用户应使用 Release 构建。

### 3.2 UI 即代码 (UI as Code) 与响应式编程

#### 概念
- **是什么**：Flutter 采用声明式 UI 构建方式，即 `UI = f(state)`，UI 随状态变化而重建。
- **为什么需要**：让开发者只需关注状态到 UI 的映射，当状态改变时，框架自动更新界面。
- **解决什么问题**：简化 UI 开发流程，避免手动操作 DOM 或视图的复杂性。

#### 原理
- **底层实现**：
  - **对象创建高效**：Dart 支持快速的对象分配和垃圾回收（分代垃圾回收器），轻松应对 UI 重建产生的大量短期 Widget 对象
  - **无布局语言**：直接使用 Dart 代码构建布局（而非 XML 或 JSX），布局逻辑更加灵活、强大且可复用
- **工作流程**：
  1. 状态改变触发 `setState`
  2. 框架调用 `build` 方法重建 Widget 树
  3. 框架比对前后 Widget 树，计算最小变更
  4. 更新底层渲染树

#### 使用方法
- **基本用法**：使用 `StatelessWidget` 或 `StatefulWidget`，在 `build` 方法中返回组合的 Widget。
- **最佳实践**：
  - 减少 `build` 方法中的 Widget 创建，避免过度嵌套
  - 使用 `const` 构造函数重用 Widget 实例
  - 优先使用 Widget 而非辅助函数构建可复用 UI

#### 代码示例
```dart
// 响应式 UI 示例：点击按钮更新计数器
class CounterWidget extends StatefulWidget {
  const CounterWidget({super.key});

  @override
  State<CounterWidget> createState() => _CounterWidgetState();
}

class _CounterWidgetState extends State<CounterWidget> {
  int _count = 0;

  void _increment() {
    setState(() {
      _count++; // 状态改变触发 UI 重建
    });
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('Count: $_count'),
        ElevatedButton(
          onPressed: _increment,
          child: const Text('Increment'),
        ),
      ],
    );
  }
}
```

#### 注意事项
- **性能优化**：避免在 `build` 中创建耗时操作，合理使用 `const` 关键字。
- **状态管理**：选择合适的状态管理方案，避免不必要的重建。

### 3.3 语言特性优势

#### 概念
- **是什么**：Dart 语言本身具备一系列特性，使其特别适合用于构建 UI 框架。
- **为什么需要**：UI 框架需要语言支持高效的对象创建、并发处理、类型安全等特性。
- **解决什么问题**：为 Flutter 提供强大的语言基础，简化开发流程，提高运行效率。

#### 原理
- **底层实现**：
  - **单线程模型与 Isolate**：Dart 默认单线程运行（避免锁机制），通过 Isolate 提供并发能力
  - **健全的类型安全与空安全**：编译时静态代码分析保护免受空异常
  - **对象导向**：与 Flutter"万物皆 Widget"理念完美契合

- **关键技术点**：
  - **主 Isolate**：执行 UI 代码
  - **后台 Isolate**：处理耗时任务（图片处理、JSON 解析），不卡顿 UI 线程
  - **空安全**：在编译时通过静态代码分析防止空引用异常

#### 使用方法
- **基本用法**：
  - **并发处理**：使用 `compute` 函数或手动创建 Isolate 处理耗时任务
  - **空安全**：声明变量时明确是否可为空（`String?` 表示可为空，`String` 表示不可为空）

#### 代码示例
```dart
import 'dart:convert';
import 'package:flutter/foundation.dart';

// 使用 compute 在后台 Isolate 解析 JSON
List<String> parseItems(String jsonStr) {
  final map = json.decode(jsonStr) as Map<String, dynamic>;
  final list = (map['items'] as List).cast<String>();
  return list;
}

Future<void> loadData() async {
  const jsonData = '{"items": ["苹果", "香蕉", "西瓜"]}';
  // compute 会在后台 Isolate 执行 parseItems 函数
  final result = await compute(parseItems, jsonData);
  print(result); // 主线程使用结果，UI 不被阻塞
}
```

#### 注意事项
- **Web 端 Isolate 限制**：在 Web 端，Dart 的 Isolate 特性目前并不直接可用，通常需要借助 Web Worker 思路替代。
- **Isolate 通信**：Isolate 之间通过消息传递通信，不共享内存，避免数据竞争与加锁复杂度。

### 3.4 满足 UI 框架需求

#### 概念
- **是什么**：Dart 语言的设计使其能够满足现代 UI 框架的各种需求，特别是对屏幕每个像素的精细控制。
- **为什么需要**：Flutter 需要一种能够直接控制图形渲染、无需通过桥接与原生通信的语言。
- **解决什么问题**：让 Flutter 能够通过 `dart:ui` 库直接调用引擎层的 C++ 代码，实现对图形、输入等底层的精细控制。

#### 原理
- **底层实现**：
  - **dart:ui 库**：对 Engine 中 Skia 库的 C++ 接口的绑定，提供 Canvas、Paint 等底层绘制类
  - **直接调用**：无需桥接，Dart 代码可直接调用 C++ 实现的引擎功能
- **关键技术点**：
  - **垃圾收集和内存管理**：Dart 的特性有助于简化开发流程，提高应用运行效率
  - **async/await**：提供 Future 与 Stream 等现代异步能力

#### 使用方法
- **基本用法**：通常通过 Framework 层间接使用底层能力，高级场景可直接导入 `dart:ui`。
- **常见场景**：
  - **自定义绘制**：使用 CustomPaint 和 Canvas 直接绘制
  - **平台通道**：通过 Platform Channels 与原生代码交互

#### 代码示例
```dart
import 'dart:ui' as ui;
import 'package:flutter/material.dart';

class CustomPainterWidget extends StatelessWidget {
  const CustomPainterWidget({super.key});

  @override
  Widget build(BuildContext context) {
    return CustomPaint(
      painter: _CirclePainter(),
      child: Container(),
    );
  }
}

class _CirclePainter extends CustomPainter {
  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()
      ..color = Colors.blue
      ..style = PaintingStyle.fill;
    
    canvas.drawCircle(Offset(size.width / 2, size.height / 2), 50, paint);
  }

  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => false;
}
```

#### 注意事项
- **平台通道性能**：频繁的平台通道调用可能影响性能，应尽量减少跨语言通信。
- **学习曲线**：直接使用 `dart:ui` 需要理解底层图形概念，通常仅在特殊场景使用。

## 四、扩展知识：核心概念与渲染流程

### 4.1 响应式用户界面与 Widget

#### 概念
- **是什么**：Flutter 的核心思想是使用 Widget 构建用户界面，采用响应式编程模型。
- **为什么需要**：让 UI 开发更加声明式、可预测，简化状态管理。
- **解决什么问题**：使开发者能够专注于描述 UI 应该是什么样子，而非如何操作 UI 变化。

#### 原理
- **底层实现**：
  - **Widget 是不可变的**：Widget 描述 UI 的一部分，存储配置信息
  - **Element 树**：管理 Widget 的生命周期，维护状态
  - **RenderObject 树**：处理实际的布局和绘制
- **关键技术点**：
  - **StatelessWidget**：无状态，配置后不变
  - **StatefulWidget**：有状态，可通过 `setState` 更新
  - **组合优于继承**：通过组合小型 Widget 构建复杂功能

#### 使用方法
- **基本用法**：继承 `StatelessWidget` 或 `StatefulWidget`，实现 `build` 方法。
- **常见场景**：
  - **静态内容**：使用 StatelessWidget
  - **动态交互**：使用 StatefulWidget + setState
  - **复杂状态**：使用状态管理库（Provider、Bloc 等）

#### 注意事项
- **性能优化**：合理使用 `const` 构造函数，避免不必要的重建。
- **状态提升**：将状态放在合适的层级，避免状态分散。

### 4.2 从代码到屏幕的渲染流程

#### 概念
- **是什么**：Flutter 将 Widget 树转换为最终屏幕像素的完整过程，包含多个阶段。
- **为什么需要**：理解渲染流程有助于开发者编写高性能应用，定位性能问题。
- **解决什么问题**：将声明式 UI 描述高效转换为 GPU 可执行的绘制指令。

#### 原理
- **工作流程**（七个阶段）：
  1. **用户输入阶段 (User Input)**：用户交互触发事件监听器，修改应用状态
  2. **动画阶段 (Animation)**：动画系统持续更新属性（位置、不透明度、大小）
  3. **构建阶段 (Build)**：调用 `build()` 方法，框架比较新旧 Widget 树，更新必要部分
  4. **布局阶段 (Layout)**：深度优先遍历渲染树，父节点传递约束，子节点返回大小
  5. **绘制阶段 (Paint)**：调用 RenderObject 的 `paint()` 方法，生成绘制指令发送给 Engine
  6. **合成阶段 (Compositing)**：将绘制的 Widget 组织成图层，优化渲染过程
  7. **栅格化阶段 (Rasterization)**：GPU 将合成图层转换为屏幕上的像素

- **关键技术点**：
  - **约束布局系统**：父节点传递最小/最大宽高约束，子节点计算大小
  - **重绘边界**：隔离复杂区域，避免无关区域重绘
  - **显示列表**：Engine 将绘制指令编译为有序命令集

#### 使用方法
- **基本用法**：开发者正常编写 Widget，渲染流程自动执行。
- **常见场景**：
  - **性能优化**：了解各阶段特征，针对性优化
  - **调试布局**：使用 Flutter Inspector 调试布局问题

#### 注意事项
- **布局阶段优化**：避免在布局阶段执行耗时操作。
- **绘制阶段优化**：合理使用重绘边界，减少重绘区域。
- **动画性能**：确保动画帧率稳定（60fps），即每帧时间 $t_f = \frac{1}{60} \approx 16.67ms$。