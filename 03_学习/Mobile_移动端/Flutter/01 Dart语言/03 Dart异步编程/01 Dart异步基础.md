#### [知识点名称] 同步 vs 异步 & 阻塞 vs 非阻塞

##### 概念
- **是什么**：同步和异步是描述程序执行流程的两种模式。阻塞和非阻塞是描述操作是否挂起调用线程的状态。
  - **同步**：代码按顺序执行，前一个操作完成后才能执行下一个。如果一个操作耗时，后续代码必须等待。
  - **异步**：发起一个操作后，程序无需等待其完成即可继续执行后续代码。操作完成后通过回调、Future 或事件通知结果。
  - **阻塞**：调用会挂起当前线程直到操作完成，期间线程无法执行其他任务。
  - **非阻塞**：调用立即返回，不挂起线程。操作完成后通过某种机制通知结果。
- **为什么需要**：在 UI 应用或高并发场景中，如果使用同步阻塞操作，会导致线程被长时间占用，造成界面卡顿或资源利用率低下。异步非阻塞模型允许在等待 I/O 等耗时操作时，线程去处理其他任务，从而提高响应性和吞吐量。
- **解决什么问题**：主要解决单线程环境下避免因 I/O 等待而浪费 CPU 时间的问题，使单线程也能高效处理大量并发任务，同时简化了多线程编程的复杂性（无需显式加锁）。

##### 原理
- **底层实现**：依赖操作系统提供的非阻塞 I/O 机制（如 epoll、kqueue、IOCP）以及事件通知。Dart 将这些底层机制封装成高级异步 API（如 Future、Stream）。
- **工作流程**：
  1. 发起异步操作（如网络请求），立即返回一个 Future 对象，表示未来的结果。
  2. 实际 I/O 操作交由操作系统内核或专门线程池处理，Dart 线程继续执行事件循环。
  3. 操作完成后，系统将完成事件放入 Dart 的事件队列。
  4. 事件循环从队列中取出事件，执行对应的回调（如 Future 的 `then` 或 `await` 之后的代码）。
- **关键技术点**：单线程 + 事件循环 + 非阻塞 I/O。

##### 使用方法
- **基本用法**：Dart 中通过 `Future`、`async/await` 和 `Stream` 来编写异步代码。
  ```dart
  // 同步方式（阻塞）
  String data = readFileSync(); // 会阻塞线程直到文件读完
  print(data);

  // 异步方式（非阻塞）
  Future<String> future = readFileAsync();
  future.then((data) => print(data));
  print('继续执行其他任务'); // 这行会立即打印，不会等待文件读取
  ```
- **常见场景**：网络请求、文件读写、数据库操作、定时器、用户输入事件等。
- **最佳实践**：尽可能使用 `async/await` 代替原始的 `then` 链，使代码更易读；避免在异步回调中执行耗时同步操作，以免阻塞事件循环。

##### 注意事项
- **常见错误**：
  - 忘记在异步函数前加 `await`，导致得到 `Future` 而不是实际值。
  - 在 `async` 函数中执行耗时同步计算，阻塞事件循环。
  - 错误地认为异步代码会自动并行执行（实际上仍是单线程交替执行）。
- **性能问题**：过度创建微任务可能导致事件队列被饿死；大量异步任务嵌套可能引发回调地狱（可用 `async/await` 解决）。
- **兼容性**：所有 Dart 环境（VM、Flutter、Web）都支持相同的异步模型，但底层实现可能略有差异。

##### 代码示例
```dart
// 同步阻塞示例
void syncExample() {
  print('开始');
  sleep(Duration(seconds: 2)); // 模拟耗时操作，阻塞2秒
  print('结束'); // 2秒后才打印
}

// 异步非阻塞示例
void asyncExample() {
  print('开始');
  Future.delayed(Duration(seconds: 2), () {
    print('异步操作完成');
  });
  print('结束'); // 立即打印，不会等待2秒
}
```

#### [知识点名称] Dart 的单线程模型与事件循环

##### 概念
- **是什么**：Dart 代码运行在单个执行线程（称为 Isolate）中，依靠事件循环（Event Loop）来处理异步任务。事件循环持续运行，从两个队列（微任务队列和事件队列）中取出任务执行。
- **为什么需要**：单线程模型避免了多线程编程中的锁、数据竞争等问题，同时借助事件循环和非阻塞 I/O，依然能高效处理并发。这使得 Dart 既简单又高性能，特别适合 UI 开发。
- **解决什么问题**：解决了单线程如何并发处理多个任务的问题，保证 UI 线程的流畅运行，同时简化了异步编程模型。

##### 原理
- **底层实现**：Dart 运行时维护两个队列：
  - **微任务队列**（Microtask Queue）：优先级高，用于处理短时间内需要完成的小任务，如 Future 的某些回调、流的内部分发等。
  - **事件队列**（Event Queue）：优先级低，包含外部事件（I/O、计时器、用户输入等）和普通 Future 任务。
- **工作流程**：
  1. 事件循环首先检查微任务队列，如果不为空，则依次执行所有微任务，直到队列清空。
  2. 然后从事件队列中取出第一个事件执行。
  3. 执行完一个事件后，再次检查微任务队列，重复上述过程。
  4. 如果两个队列都为空，事件循环将等待，直到新事件到达。
- **关键技术点**：
  - **微任务**：通过 `scheduleMicrotask` 或 `Future.microtask` 添加。
  - **事件**：通过 `Future` 构造函数、`Timer`、`Stream` 等添加。
  - 微任务优先于事件，因此滥用微任务可能导致事件（如 UI 渲染）被延迟。

##### 使用方法
- **基本用法**：通常不需要直接操作事件循环，而是通过 Future、async/await 等高层 API 触发任务。
- **常见场景**：
  - 使用 `Future` 将任务放入事件队列。
  - 使用 `scheduleMicrotask` 执行紧急的短任务（如需要立即处理的数据更新，但要避免长时间运行）。
- **最佳实践**：
  - 默认使用 `Future`（事件队列）来处理异步任务，除非有明确理由使用微任务。
  - 避免在微任务中执行耗时操作，以免阻塞事件队列。

##### 注意事项
- **常见错误**：
  - 混淆微任务和事件队列的优先级，导致执行顺序错误。
  - 在微任务中递归添加微任务，造成无限循环，饿死事件队列。
- **性能问题**：微任务过多会导致事件队列得不到及时处理，引起 UI 卡顿。
- **兼容性**：所有 Dart 环境行为一致。

##### 代码示例
```dart
void main() {
  print('main start');

  scheduleMicrotask(() => print('微任务1'));

  Future(() => print('事件1'));

  Future.microtask(() => print('微任务2'));

  Future.delayed(Duration.zero, () => print('延时事件'));

  print('main end');
}
// 输出顺序：
// main start
// main end
// 微任务1
// 微任务2
// 事件1
// 延时事件
```

#### [知识点名称] Future 与 async/await

##### 概念
- **是什么**：
  - **Future**：表示一个异步操作的结果，可能是一个值或一个错误。它可以处于未完成或已完成状态。
  - **async/await**：是 Dart 提供的语法糖，让异步代码看起来像同步代码，提高可读性。
- **为什么需要**：直接使用回调（`then`）容易导致回调地狱，难以维护。`async/await` 使得异步流程控制更接近自然语言，便于理解和调试。
- **解决什么问题**：简化异步代码的编写，避免嵌套回调，同时保持非阻塞特性。

##### 原理
- **底层实现**：
  - `Future` 内部维护了一个回调列表，当异步操作完成时，会依次调用这些回调。
  - `async` 函数会自动返回一个 Future，函数体在遇到 `await` 时会暂停执行，并将控制权交还给事件循环。当被 await 的 Future 完成后，事件循环会恢复该函数的执行。
- **工作流程**：
  1. 调用一个 `async` 函数，立即返回一个 Future。
  2. 函数体开始同步执行，直到遇到第一个 `await` 表达式。
  3. 该 `await` 表达式的 Future 被调度，当前函数挂起，事件循环可以处理其他任务。
  4. 当 Future 完成后，事件循环将函数剩余部分作为一个任务放入事件队列。
  5. 事件循环取出该任务继续执行，直到遇到下一个 `await` 或函数结束。
- **关键技术点**：
  - `Future` 的 `then`、`catchError`、`whenComplete` 方法用于链式调用。
  - `async` 函数返回的 Future 的值就是函数 return 的值。
  - `await` 只能在 `async` 函数中使用。

##### 使用方法
- **基本用法**：
  ```dart
  // 使用 Future 和 then
  fetchUser().then((user) {
    print(user);
  }).catchError((error) {
    print(error);
  });

  // 使用 async/await
  Future<void> printUser() async {
    try {
      var user = await fetchUser();
      print(user);
    } catch (e) {
      print(e);
    }
  }
  ```
- **常见场景**：网络请求、文件读写、延时操作、任何返回 Future 的 API。
- **最佳实践**：
  - 优先使用 `async/await` 而不是原始 `then`。
  - 处理错误时，优先使用 `try/catch` 与 `await` 配合，而不是 `catchError`。
  - 对于无依赖的多个 Future，可以使用 `Future.wait` 并发执行。
  - 避免在 `async` 函数中执行耗时同步操作，应将其放入 `Isolate`。

##### 注意事项
- **常见错误**：
  - 忘记在调用 `async` 函数时使用 `await` 或 `then`，导致无法获取结果。
  - 在 `async` 函数中返回 `Future` 而没有 `await`，可能导致类型错误（例如返回 `Future<Future<T>>`）。
  - 未正确处理错误，导致异常被吞没。
- **性能问题**：`await` 会挂起函数，但开销很小；过度使用 `Future` 链可能造成性能开销，但通常可忽略。
- **兼容性**：所有 Dart 环境均支持。

##### 代码示例
```dart
Future<String> fetchData() async {
  // 模拟网络请求
  await Future.delayed(Duration(seconds: 1));
  return '数据';
}

void main() async {
  print('开始');
  var data = await fetchData(); // 挂起1秒，但不阻塞事件循环
  print(data); // 1秒后打印
  print('结束');
}
// 输出顺序：
// 开始
// (1秒后) 数据
// 结束
```

#### [知识点名称] Stream

##### 概念
- **是什么**：`Stream` 是一系列异步事件的序列，可以发送多个值（成功事件）或错误，最后可能关闭。你可以监听流来响应每个事件。
- **为什么需要**：某些场景下，数据不是一次性返回的，而是随时间多次产生，例如用户输入、文件读取的字节块、WebSocket 消息等。`Stream` 为这种异步数据流提供了统一处理模型。
- **解决什么问题**：提供一种订阅机制来处理多个异步数据片段，支持转换、组合、过滤等操作，类似集合的迭代器但用于异步。

##### 原理
- **底层实现**：`Stream` 有两种类型：
  - **单订阅流**（Single-subscription）：只能被一个监听者订阅，通常用于一次性数据流，如文件读取。监听开始后，数据才会产生。
  - **广播流**（Broadcast）：允许多个监听者同时订阅，即使没有监听者，数据也会产生并丢弃。
- **工作流程**：
  1. 创建一个 `Stream`（如通过 `StreamController`）。
  2. 通过 `listen` 方法注册监听器，当有数据事件、错误事件或完成事件时，相应回调被调用。
  3. 使用 `await for` 循环可以同步迭代流中的事件。
  4. 流可以使用各种转换方法，如 `map`、`where`、`expand` 等，这些方法返回新的流。
- **关键技术点**：
  - `StreamController` 用于手动创建和控制流。
  - `StreamSubscription` 对象用于管理监听，可以暂停、恢复或取消。
  - 流的转换是惰性的，只有监听时才会执行。

##### 使用方法
- **基本用法**：
  ```dart
  // 创建流
  final controller = StreamController<int>();
  // 添加数据
  controller.add(1);
  controller.add(2);
  controller.close();

  // 监听流
  controller.stream.listen(
    (data) => print('收到: $data'),
    onError: (err) => print('错误: $err'),
    onDone: () => print('流结束'),
  );

  // 使用 await for
  await for (var value in controller.stream) {
    print(value);
  }
  ```
- **常见场景**：
  - 实时数据（如传感器读数、WebSocket 消息）。
  - 文件分块读取。
  - 用户界面事件（如点击、文本变化）。
- **最佳实践**：
  - 对于需要多个监听者的流，使用广播流（`StreamController.broadcast()`）。
  - 使用 `async*` 和 `yield` 生成流（生成器函数）。
  - 及时取消订阅避免内存泄漏（调用 `cancel()`）。

##### 注意事项
- **常见错误**：
  - 对单订阅流多次监听，会引发错误。
  - 忘记处理错误，导致流意外关闭。
  - 在监听之前添加数据到单订阅流，数据会丢失（因为流还没开始生产）。
- **性能问题**：流转换操作可能创建新流，但通常开销可控；避免在回调中执行耗时操作。
- **兼容性**：所有 Dart 环境均支持。

##### 代码示例
```dart
// 使用 async* 生成一个每秒发出数字的流
Stream<int> countStream(int max) async* {
  for (int i = 1; i <= max; i++) {
    await Future.delayed(Duration(seconds: 1));
    yield i; // 发射一个值
  }
}

void main() async {
  await for (var num in countStream(3)) {
    print(num); // 每秒打印 1, 2, 3
  }
}
```

#### [知识点名称] Isolate 与并发

##### 概念
- **是什么**：`Isolate` 是 Dart 中实现并发的基本单位，每个 Isolate 拥有独立的内存堆和事件循环，它们之间不共享内存，通过消息传递通信。
- **为什么需要**：虽然事件循环可以高效处理 I/O 密集型任务，但对于计算密集型任务（如 JSON 解析、图像处理），如果放在主 Isolate 中运行，会阻塞事件循环，导致 UI 卡顿。Isolate 可以将这类任务放到后台独立线程执行，避免阻塞。
- **解决什么问题**：实现真正的并行计算，充分利用多核 CPU，同时保持数据安全（无共享内存，无需锁）。

##### 原理
- **底层实现**：每个 Isolate 由操作系统线程或进程支撑（具体取决于平台），但 Dart 代码在 Isolate 内依然是单线程执行。Isolate 之间通过端口（SendPort/ReceivePort）传递消息，消息会进行序列化和反序列化，因此不能直接传递对象引用。
- **工作流程**：
  1. 主 Isolate 通过 `Isolate.spawn` 或 `Isolate.run` 创建新 Isolate。
  2. 新 Isolate 拥有自己的内存和事件循环，执行指定的函数。
  3. 两个 Isolate 通过 `SendPort` 发送消息，对方通过 `ReceivePort` 接收。
  4. 任务完成后，后台 Isolate 将结果通过消息发回主 Isolate。
  5. 后台 Isolate 可以关闭。
- **关键技术点**：
  - `Isolate.spawn`：需要静态函数作为入口。
  - `Isolate.run`：简化版，将函数在后台运行并返回 Future（Dart 2.19 引入）。
  - `ReceivePort` 用于接收消息。
  - 消息必须可序列化（基本类型、列表、映射等），不能包含函数或不可序列化对象。

##### 使用方法
- **基本用法**（使用 `Isolate.run`）：
  ```dart
  import 'dart:isolate';

  Future<void> main() async {
    // 在后台 Isolate 执行耗时计算
    int result = await Isolate.run(() => fibonacci(40));
    print(result);
  }

  int fibonacci(int n) {
    if (n <= 1) return n;
    return fibonacci(n - 1) + fibonacci(n - 2);
  }
  ```
- **Flutter 中的简化用法**（使用 `compute` 函数）：
  ```dart
  import 'package:flutter/foundation.dart';

  Future<void> main() async {
    // compute 是 Flutter 提供的便捷函数，内部使用 Isolate
    final jsonData = await compute(parseJson, hugeJsonString);
    print('解析完成');
  }

  Map<String, dynamic> parseJson(String jsonString) {
    return jsonDecode(jsonString) as Map<String, dynamic>;
  }
  ```
- **常见场景**：
  - 解析大型 JSON。
  - 图像处理/滤镜。
  - 加密/解密大数据。
  - 任何 CPU 密集型任务。
- **最佳实践**：
  - 尽量使用 `Isolate.run` 或 `compute`，它们封装了创建和通信细节。
  - 传递的数据量不宜过大，否则序列化开销可能抵消收益。
  - 任务完成后及时退出 Isolate，避免资源泄漏。

##### 注意事项
- **常见错误**：
  - 试图在 Isolate 之间共享可变对象（实际上通过消息传递的是副本）。
  - 忘记处理后台 Isolate 的错误，导致 Future 永不完成。
  - 过度创建 Isolate 导致内存开销过大。
- **性能问题**：Isolate 的创建和销毁有开销，适合长时间运行的任务；短小任务可能不如直接在事件循环中执行划算。
- **兼容性**：所有 Dart 环境支持 Isolate，但在 Web 平台（Dart2js）上，Isolate 行为不同（实际使用 Web Workers），`Isolate.spawn` 可能受限。Flutter 移动/桌面平台支持良好。

##### 代码示例
```dart
import 'dart:isolate';

void heavyComputation(SendPort sendPort) {
  // 在后台 Isolate 中执行
  int result = 0;
  for (int i = 0; i < 1000000000; i++) {
    result += i;
  }
  sendPort.send(result); // 将结果发回主 Isolate
}

void main() async {
  ReceivePort receivePort = ReceivePort();
  // 创建新 Isolate，传入入口函数和发送端口
  await Isolate.spawn(heavyComputation, receivePort.sendPort);
  // 监听结果
  int result = await receivePort.first;
  print('计算结果: $result');
  receivePort.close();
}
```