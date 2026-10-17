## 一、Stream 概念：异步数据事件序列

### 1.1 概念

**是什么**  
Stream（流）是 `dart:async` 库中表示一系列**异步数据事件**的抽象 。它是一个异步数据序列，数据会随着时间的推移逐个产生，消费者可以监听这个序列并对每个数据事件做出响应 。可以把 Stream 想象成一根管道：数据像水流一样，依次进入管道，你可以随时监听管道里流过的数据 。

**为什么需要**  
在异步编程中，经常需要处理多个先后到达的数据，例如：用户输入、定时器事件、文件读取块、传感器数据、WebSocket 消息等 。Future 只能表示单个异步结果，而 Stream 专门用于处理**多个异步事件**。

**解决什么问题**  
- 解耦数据生产者和消费者：生产者只管往流里放数据，消费者只管监听流 。  
- 提供统一的错误处理和完成通知机制 。  
- 允许对数据流进行组合、转换和过滤，形成可复用的异步管道 。

### 1.2 原理

**底层实现**  
Stream 基于**发布-订阅模式**实现。内部维护一个订阅者列表（对于广播流）或单个订阅者（对于单订阅流），当有数据事件时，通知所有（或唯一）订阅者 。

**工作流程**  
1. **创建 Stream**：通过 `StreamController`、`async*` 函数或已有流转换。  
2. **消费者监听**：调用 `listen()` 注册回调（数据、错误、完成）。  
3. **生产者产生事件**：调用 `add()`、`addError()` 或通过 `yield` 产生数据。  
4. **事件分发**：Stream 内部将事件传递给订阅者的对应回调。  
5. **流结束**：所有事件发送完毕时，调用 `close()` 或函数结束，触发 `onDone`。  
6. **取消订阅**：订阅者可以调用 `cancel()` 停止接收事件，释放资源 。

**关键技术点**  
- **事件循环**：Stream 事件通过 Dart 事件循环调度，保证异步非阻塞 。  
- **背压支持**：可以通过 `pause()` / `resume()` 控制订阅者的接收速度 。  
- **惰性求值**：大多数 Stream 只有在被监听时才开始产生事件 。

### 1.3 使用方法

**基本用法**  
```dart
// 创建流的方式
Stream.fromIterable([1, 2, 3]);                 // 从可迭代对象创建
Stream.periodic(Duration(seconds: 1), (i) => i); // 定时产生事件
Stream.value(42);                                // 只产生一个值
Stream.error('出错了');                           // 只产生一个错误
Stream.empty();                                  // 空流
Stream.fromFuture(Future.value(42));              // 从 Future 创建
```

**监听流**  
```dart
stream.listen(
  (data) => print('数据: $data'),   // 数据事件处理
  onError: (err) => print('错误: $err'), // 错误处理
  onDone: () => print('完成'),        // 流关闭处理
  cancelOnError: false,               // 出错时是否自动取消
);
```

**await for 循环（推荐方式）**   
```dart
Future<int> sumStream(Stream<int> stream) async {
  var sum = 0;
  await for (final value in stream) {
    sum += value;
  }
  return sum;
}
```

### 1.4 注意事项

**常见错误**  
- 在 `await for` 循环中，错误会抛出，需要用 `try-catch` 捕获 。  
- 忘记取消订阅可能导致内存泄漏 。  
- 对单订阅流多次监听会抛出 `StateError` 。

**性能问题**  
- Stream 操作符会创建新流，大量链式操作可能增加开销，但通常可接受。  
- 避免在事件处理中执行耗时同步操作，会阻塞事件循环。

**兼容性**  
所有 Dart 环境（VM、Flutter、Web）均支持 Stream 。

---

## 二、Stream 类型：单订阅流 vs 广播流

### 2.1 概念

**是什么**  
Dart 的 Stream 分为两种核心类型 ：  
- **单订阅流 (Single-Subscription Stream)**：整个生命周期只允许有一个订阅者。  
- **广播流 (Broadcast Stream)**：允许多个订阅者同时监听，也可以在没有订阅者时产生事件。

**为什么需要**  
单订阅流适用于“一次性”数据源，比如文件读取，你只希望从头到尾顺序消费一次 ；广播流适用于多个地方需要同时接收相同事件的场景，比如 UI 事件、传感器信号、全局状态变化 。

**解决什么问题**  
- 单订阅流保证数据顺序被单一消费者完整消费，避免竞争。  
- 广播流允许多个独立监听者，互不干扰。

### 2.2 原理

**底层实现**  
单订阅流内部只保存一个订阅者引用，当尝试第二次监听时直接抛出错误。广播流内部维护一个订阅者列表，事件会分发给列表中的所有订阅者 。

**工作流程**  
- **单订阅流**：只有在第一次调用 `listen()` 后，流才开始产生事件。如果订阅者取消，流结束 。  
- **广播流**：可以随时调用 `listen()`，新订阅者只会收到之后产生的事件。即使没有订阅者，生产者也可以产生事件（但这些事件会被丢弃）。

**关键技术点**  
- 单订阅流通常由 `StreamController()`（无参数）创建。  
- 广播流由 `StreamController.broadcast()` 创建 。  
- 可以通过 `asBroadcastStream()` 将单订阅流转换为广播流 。

### 2.3 使用方法

**基本用法**  
```dart
// 创建单订阅流
final singleController = StreamController<int>();
final singleStream = singleController.stream;

// 创建广播流
final broadcastController = StreamController<int>.broadcast();
final broadcastStream = broadcastController.stream;
```

**常见场景**  
- **单订阅流**：文件读取、网络请求响应、数据库查询结果 。  
- **广播流**：按钮点击事件、WebSocket 消息、状态管理（如 BLoC）、传感器数据 。

**最佳实践**  
- 除非明确需要多个监听者，否则默认使用单订阅流，因为它行为更简单。  
- 若要将单订阅流共享给多个监听者，使用 `asBroadcastStream()` 并注意它会缓存历史事件（可能导致内存问题）。

### 2.4 注意事项

**常见错误**  
- 对单订阅流多次监听：`StateError: Stream has already been listened to.`   
- 在广播流中假设新订阅者能收到之前的事件（它们收不到）。  
- 忘记广播流的事件在没有订阅者时会被丢弃，可能导致逻辑丢失。

**性能问题**  
- 广播流分发事件给多个订阅者，性能与订阅者数量线性相关。  
- `asBroadcastStream()` 需要缓存事件直到第一个订阅者出现，可能占用内存。

**兼容性**  
所有 Dart 平台一致 。

---

## 三、StreamController：创建和管理流

### 3.1 概念

**是什么**  
`StreamController` 是手动创建和控制 Stream 的类。它同时提供了向流添加事件的方法（通过 `sink` 或直接调用 `add`）和供外界监听的 `stream` 属性 。

**为什么需要**  
当我们需要精细控制事件的产生时机、监听行为（如暂停、取消）时，就需要使用 StreamController。它是连接数据生产者和消费者的桥梁 。

**解决什么问题**  
- 手动向流中添加数据、错误，并关闭流。  
- 监听订阅者的行为（`onListen`、`onPause`、`onResume`、`onCancel`），实现资源管理 。  
- 创建同步或异步控制器。

### 3.2 原理

**底层实现**  
StreamController 内部维护一个 `_StreamImpl` 对象，它实现了 Stream 接口。控制器持有该流的引用，并提供内部方法发送事件。当调用 `add` 时，控制器通过内部管道将事件传递给流的订阅者 。

**工作流程**  
1. 创建 `StreamController` 实例。  
2. 通过 `controller.stream` 暴露流给消费者。  
3. 消费者调用 `listen()` 注册回调。  
4. 控制器通过 `add`、`addError`、`close` 产生事件。  
5. 如果设置了 `onListen` 等回调，会在对应时机触发。  
6. 当控制器关闭后，不能再添加事件。

**关键技术点**  
- **同步 vs 异步**：`sync: true` 时，事件会立即同步发送给订阅者（需确保订阅者已存在）；`sync: false`（默认）时，事件通过微任务异步发送，更安全 。  
- **回调钩子**：`onListen`、`onPause`、`onResume`、`onCancel` 用于管理资源（如开启/关闭定时器）。  
- **sink 属性**：一个只包含 `add`、`addError`、`close` 的接口，用于将数据生产逻辑与流控制分离 。

### 3.3 使用方法

**基本用法**  
```dart
final controller = StreamController<int>();
final stream = controller.stream;

final subscription = stream.listen((data) {
  print('收到: $data');
});

controller.add(1);
controller.add(2);
controller.close();
```

**常见场景**  
- 包装现有异步 API 为 Stream。  
- 实现自定义事件总线。  
- 与 `async*` 函数配合处理复杂逻辑。

**最佳实践**  
- **务必调用 `close()` 释放资源**，避免内存泄漏 。  
- 使用 `onCancel` 回调取消订阅或清理资源。  
- 优先使用异步控制器（默认），除非确定需要同步行为且理解风险。

### 3.4 注意事项

**常见错误**  
- 在控制器关闭后调用 `add` 会抛出异常。  
- 忘记取消订阅可能导致 `StreamController` 无法被垃圾回收。  
- 在 `onListen` 中产生事件时，若订阅者尚未准备好，可能会丢失事件（对于同步控制器尤其危险）。

**性能问题**  
- 同步控制器减少了事件循环调度开销，但必须确保事件处理不会导致栈溢出或逻辑错误。  
- 大量使用 StreamController 可能产生较多对象，注意重用。

**兼容性**  
所有 Dart 环境 。

---

## 四、Stream 订阅：listen、onData、onError、onDone

### 4.1 概念

**是什么**  
Stream 订阅是通过调用 `stream.listen()` 获得的 `StreamSubscription` 对象，它代表了消费者与流之间的连接 。通过这个对象，可以控制接收事件的方式（暂停、恢复、取消）以及获取订阅状态。

**为什么需要**  
监听流只是第一步，实际应用中可能需要动态控制事件流：比如用户离开页面时暂停数据接收、或者主动取消订阅避免内存泄漏 。

**解决什么问题**  
- 提供暂停/恢复机制，实现流控。  
- 允许取消订阅，释放资源。  
- 能够获取订阅状态（如是否暂停）。

### 4.2 原理

**底层实现**  
`StreamSubscription` 是 Stream 内部维护的一个句柄，它持有消费者的回调函数以及暂停计数等状态。当流产生事件时，会检查订阅者是否暂停，若暂停则将事件缓存（或丢弃，取决于流类型），否则立即调用回调 。

**工作流程**  
1. 调用 `listen()` 返回 `StreamSubscription`。  
2. 订阅者可以通过 `pause()` 通知流暂停发送事件（流可能缓存后续事件）。  
3. 调用 `resume()` 恢复事件发送。  
4. 调用 `cancel()` 终止订阅，流会清理相关资源，并可能触发 `onCancel`。  
5. 当流关闭或出错时，订阅也会自动结束。

**关键技术点**  
- `pause([Future? resumeSignal])`：可传入一个 Future，当 Future 完成时自动恢复 。  
- `isPaused` 属性：检查当前是否暂停。  
- `cancel()` 返回 `Future`，可在真正取消后执行清理。

### 4.3 使用方法

**基本用法**  
```dart
Stream<int> stream = Stream.periodic(Duration(seconds: 1), (i) => i);

StreamSubscription<int> subscription = stream.listen(
  (data) => print('收到: $data'),
  onError: (err) => print('错误: $err'),
  onDone: () => print('完成'),
  cancelOnError: false,
);

// 暂停
subscription.pause();

// 恢复
subscription.resume();

// 取消
subscription.cancel();
```

**常见场景**  
- 页面销毁时取消订阅 。  
- 根据用户操作暂停/恢复实时数据更新。  
- 限流：利用 `pause` 结合 Future 实现节流。

**最佳实践**  
- 总是保存 `StreamSubscription` 并在不再需要时调用 `cancel()` 。  
- 使用 `cancelOnError: true` 可以在遇到第一个错误时自动停止监听。  
- 利用 `pause` 的 `resumeSignal` 参数简化恢复逻辑。

### 4.4 注意事项

**常见错误**  
- 忘记取消订阅导致流持续运行，内存泄漏 。  
- 在暂停期间产生的事件如果流是单订阅且未缓存，可能会丢失。  
- 多次取消或取消后调用 pause/resume 会抛出异常。

**性能问题**  
- 暂停期间，单订阅流通常会缓存事件（直到恢复），可能导致内存占用增加。  
- 频繁暂停/恢复可能带来性能开销。

**兼容性**  
所有 Dart 环境。

---

## 五、转换 Stream：map、where、distinct、take 等操作符

### 5.1 概念

**是什么**  
Stream 提供了一系列**操作符**（如 `map`、`where`、`take` 等），它们接受一个 Stream 并返回一个新的 Stream，对原始数据流进行变换、过滤、截取等操作 。

**为什么需要**  
数据流在传递过程中常常需要处理：转换格式、过滤无用数据、限制数量、合并多个流等。操作符让我们可以用声明式的方式组合数据流，避免手动编写复杂的监听逻辑 。

**解决什么问题**  
- 数据格式转换：`map`。  
- 数据过滤：`where`。  
- 截取部分数据：`take`、`skip`。  
- 去重：`distinct`。  
- 异步映射：`asyncMap`。

### 5.2 原理

**底层实现**  
每个操作符都是一个返回新 Stream 的方法，内部通常使用 `StreamTransformer` 实现。新 Stream 监听原始流，对每个事件应用转换逻辑，然后将结果添加到自己的输出中 。

**工作流程**  
以 `map` 为例 ：  
1. 调用 `sourceStream.map(f)` 返回一个新流 `mappedStream`。  
2. 当有人监听 `mappedStream` 时，它内部会监听 `sourceStream`。  
3. 每当 `sourceStream` 发出数据 `d`，`map` 内部调用 `f(d)` 得到新值 `r`，然后将 `r` 作为事件添加到 `mappedStream` 的监听者。  
4. 错误和完成事件同样被转发。

**关键技术点**  
- 操作符是**惰性**的，只有在新流被监听时才会监听源流 。  
- 操作符可以链式调用，每次返回新流。  
- 一些操作符（如 `distinct`）需要维护状态（前一个值）。

### 5.3 使用方法

**基本用法**  
```dart
Stream.fromIterable([1, 2, 3, 4, 5])
    .where((n) => n.isEven)        // 过滤出偶数: 2,4
    .map((n) => '数字: $n')         // 转为字符串
    .take(2)                        // 只取前两个: '数字: 2', '数字: 4'
    .distinct()                     // 去重（连续相同值）
    .listen(print);
```

**完整操作符列表** 

| 分类 | 操作符 | 描述 |
|------|--------|------|
| 映射 | `map` | 每个元素转换 |
| | `asyncMap` | 异步映射（返回 Future） |
| | `expand` | 一个元素展开为多个 |
| 过滤 | `where` | 条件过滤 |
| | `distinct` | 去重（连续相同） |
| 截取 | `take` | 取前 N 个 |
| | `takeWhile` | 条件满足时取 |
| | `skip` | 跳过前 N 个 |
| | `skipWhile` | 条件满足时跳过 |
| 工具 | `handleError` | 错误处理 |
| | `timeout` | 超时控制 |

**常见场景**  
- 数据清洗：`where` + `map`。  
- 限制频率：`take`、`skip`。  
- 去重连续值：`distinct`。  
- 异步调用：`asyncMap` 用于依赖网络请求的转换。  
- 展开集合：`expand`。

### 5.4 注意事项

**常见错误**  
- 在 `map` 中返回 Future，导致流类型变为 `Stream<Future<T>>`，而不是期望的 `Stream<T>`。此时应使用 `asyncMap` 。  
- 对广播流使用 `distinct` 等有状态操作符时，每个新监听者会重新应用操作符，状态独立，可能产生不同结果。  
- 操作符顺序会影响结果，需仔细设计。

**性能问题**  
- 每个操作符都会创建新流和内部监听，链过长可能增加少量开销，但通常可忽略。  
- 有状态操作符（如 `distinct`）会占用内存存储状态。

**兼容性**  
所有操作符在 Dart SDK 中提供，一致 。

---

## 六、StreamTransformer：自定义流转换

### 6.1 概念

**是什么**  
`StreamTransformer<T, R>` 是一个抽象类，允许开发者创建可复用的自定义流转换逻辑。它将一个输入流转换为输出流，并且可以处理数据、错误和完成事件 。

**为什么需要**  
当内置操作符不足以满足需求时（例如需要组合多个操作符并复用，或需要复杂的内部状态），就需要自定义 StreamTransformer 。

**解决什么问题**  
- 封装复杂的转换逻辑为可复用的单元。  
- 创建新的流操作符（如 `debounce`、`throttle`）。  
- 在转换过程中维护状态（如缓冲区、计数器）。

### 6.2 原理

**底层实现**  
`StreamTransformer` 定义了一个 `bind(Stream<T> stream)` 方法，返回一个 `Stream<R>`。内部通常会监听输入流，并通过一个 `EventSink` 向输出流发送事件 。

**工作流程**  
1. 调用 `stream.transform(transformer)` 时，会调用 `transformer.bind(stream)`。  
2. `bind` 方法创建一个新的输出流（通常通过 `StreamController` 或 `async*` 函数）。  
3. 在新流被监听时，开始监听输入流，并将处理后的数据/错误/完成事件通过输出流的 `EventSink` 发送。  
4. 转换器可以在事件处理过程中维护状态。

**关键技术点**  
- 可以使用 `StreamTransformer.fromHandlers` 快速创建转换器，只需提供 `handleData`、`handleError`、`handleDone` 。  
- 可以使用 `StreamTransformer.fromBind` 直接提供一个 `bind` 函数。  
- 转换器应该是“纯”的，即不依赖外部可变状态（除非明确需要）。

### 6.3 使用方法

**基本用法（fromHandlers）**  
```dart
// 创建一个每两个元素一组的批处理转换器
StreamTransformer<int, List<int>> batchTransformer = 
    StreamTransformer.fromHandlers(
  handleData: (int data, EventSink<List<int>> sink) {
    // 实际实现需要维护缓冲区，这里简化展示
    // 假设每两个一组
    List<int> buffer = [];
    buffer.add(data);
    if (buffer.length == 2) {
      sink.add(List.from(buffer));
      buffer.clear();
    }
  },
  handleDone: (sink) {
    // 结束时若有剩余，也发出
    if (buffer.isNotEmpty) sink.add(buffer);
    sink.close();
  },
);

// 使用
Stream.fromIterable([1, 2, 3, 4])
    .transform(batchTransformer)
    .listen(print); // 输出: [1,2], [3,4]
```

**基本用法（fromBind）**  
```dart
// 创建"去重后取前3个"的复合转换器
StreamTransformer<int, int> takeDistinct3 = StreamTransformer.fromBind(
  (Stream<int> stream) {
    return stream.distinct().take(3);
  }
);
```

**常见场景**  
- 创建自定义操作符（如 `debounce`、`buffer`）。  
- 复用复杂的过滤/转换逻辑。  
- 实现与外部资源交互的转换（如 WebSocket 消息编解码）。

**最佳实践**  
- 优先使用 `StreamTransformer.fromHandlers` 简化开发。  
- 如果转换器需要外部资源，确保在 `handleDone` 或通过 `onCancel` 释放资源。  
- 测试转换器时，应覆盖数据、错误、完成三种情况。

### 6.4 注意事项

**常见错误**  
- 忘记在 `handleDone` 中调用 `sink.close()`，导致输出流永不完成。  
- 状态未正确重置，导致多次绑定时状态污染。  
- 错误处理不当，导致异常未传递给输出流。

**性能问题**  
- 每个转换器内部都会创建新的流和监听，大量转换器嵌套会增加开销。  
- 如果转换器维护大量状态，注意内存占用。

**兼容性**  
所有 Dart 环境 。

---

## 七、async*、yield、yield*：生成 Stream 的函数

### 7.1 概念

**是什么**  
`async*` 是 Dart 中用于定义**异步生成器函数**的关键字，这类函数返回一个 `Stream`。函数体内可以使用 `yield` 产生一个值到流中，也可以使用 `yield*` 将另一个流的所有值转发到当前流 。

**为什么需要**  
相比于使用 `StreamController` 手动添加事件，`async*` 函数提供了一种更简洁、更直观的方式来生成异步数据序列，特别适合基于循环或递归生成数据 。

**解决什么问题**  
- 简化流的生产逻辑，用同步风格的代码编写异步生成器。  
- 自动处理流的暂停和恢复（当消费者暂停时，`yield` 会等待）。  
- 方便地组合多个流（`yield*`）。

### 7.2 原理

**底层实现**  
`async*` 函数在编译时会被转换为一个状态机，类似于 `async` 函数但支持多次 `yield`。当函数被调用时，它立即返回一个 `Stream` 对象。当该流被监听时，函数体开始执行，每次遇到 `yield` 就会将值作为事件发送，并暂停执行直到消费者请求下一个值（处理了背压）。

**工作流程**  
1. 调用 `async*` 函数，返回一个 `Stream`。  
2. 当有人监听该流时，函数体开始执行。  
3. 执行到 `yield e` 时，将 `e` 作为数据事件发送给监听者，然后暂停，直到监听者准备接收下一个事件（例如在 `await for` 中迭代或 `listen` 回调完成）。  
4. 如果监听者暂停（`pause()`），生成器也会暂停。  
5. 函数结束或遇到 `return` 时，流关闭。  
6. 如果函数内抛出未捕获异常，流会发出错误事件并关闭。

**关键技术点**  
- **背压支持**：`async*` 天然支持背压，消费者慢时生成器会自动等待。  
- `yield*` 用于委托给另一个流，等价于 `await for (var v in otherStream) yield v;` 。  
- 生成器函数内可以使用 `await`，允许在产生数据之间执行异步操作。

### 7.3 使用方法

**基本用法**  
```dart
Stream<int> countDown(int from) async* {
  for (int i = from; i > 0; i--) {
    yield i;                     // 产生一个值
    await Future.delayed(Duration(seconds: 1));
  }
}

void main() async {
  await for (int value in countDown(3)) {
    print(value); // 每秒打印: 3, 2, 1
  }
}
```

**yield* 示例**  
```dart
Stream<String> words1() async* {
  yield 'Hello';
  yield 'from';
}

Stream<String> words2() async* {
  yield 'Dart';
  yield 'Stream';
}

Stream<String> combined() async* {
  yield* words1();  // 委托给 words1
  yield '---';
  yield* words2();  // 委托给 words2
}
// 输出: Hello, from, ---, Dart, Stream
```

**常见场景**  
- 生成周期性数据（如定时器）。  
- 分页加载数据（每加载一页 `yield` 多个值）。  
- 递归遍历文件系统并逐个 `yield` 文件路径。  
- 组合多个异步数据源。

**最佳实践**  
- 在生成器内处理异常，使用 `try-catch` 捕获错误并可通过 `yield` 发送错误事件或 `Stream.error()` 。  
- 使用 `yield*` 简化流的合并。  
- 对于无限流，确保消费者能控制终止（如使用 `take` 操作符）。

### 7.4 注意事项

**常见错误**  
- 在 `async*` 函数中使用 `return value;` 是不允许的，只能使用 `return;` 结束。  
- 忘记 `yield` 值会导致函数执行但无事件发出。  
- 在生成器内使用 `await` 时，要理解暂停行为：`yield` 后函数暂停，但 `await` 也会暂停，但两者不同。

**性能问题**  
- 每次 `yield` 都会产生一次事件调度，频繁 `yield` 可能影响性能，但通常可接受。  
- 生成器内避免长时间同步计算，否则会阻塞事件循环。

**兼容性**  
所有 Dart 环境 。

---

## 八、高级 Stream 操作符：stream_transform 库

### 8.1 概念

**是什么**  
`stream_transform` 是 Dart 官方维护的一个 Stream 操作符扩展库，提供了原生 SDK 中缺失的高级操作符，如防抖（debounce）、节流（throttle）、流合并（merge）等 。

**为什么需要**  
虽然原生操作符可以处理常见转换，但在处理复杂交互时（如搜索框防抖、按钮防连点、请求竞态处理），原生操作符不足以优雅地解决问题 。

**解决什么问题**  
- **debounce（防抖）**：当流暂停一段时间后才发射最新数据。  
- **throttle（节流）**：在规定时间内只发射第一个/最后一个数据。  
- **switchMap（切换映射）**：接收到新事件时取消上一个事件的异步操作。  
- **merge（合并）**：将多个流合并为一个。  
- **tap（窥探）**：不改变流数据，但执行额外操作（如日志）。

### 8.2 使用方法

**安装**  
```yaml
dependencies:
  stream_transform: ^2.1.0
```

**防抖（Debounce）- 搜索框优化**   
```dart
import 'package:stream_transform/stream_transform.dart';

class SearchViewModel {
  final _inputController = StreamController<String>();
  
  SearchViewModel() {
    _inputController.stream
        .debounce(const Duration(milliseconds: 500))
        .listen((query) {
          // 用户停止输入 500ms 后才执行搜索
          performSearch(query);
        });
  }
  
  void onSearchTextChanged(String text) {
    _inputController.add(text);
  }
}
```

**节流（Throttle）- 按钮防连点**   
```dart
final _clickController = StreamController<void>();

_clickController.stream
    .throttle(const Duration(seconds: 1))
    .listen((_) {
      // 每秒最多执行一次
      submitForm();
    });

void onButtonClick() => _clickController.add(null);
```

**switchMap - 处理请求竞态**   
```dart
// 当用户快速切换分类时，只处理最后一次请求
categorySelectStream
    .switchMap((category) => fetchProducts(category))
    .listen((products) {
      // 只更新最后一次请求的结果
      updateUI(products);
    });
```

**merge - 合并多个流**   
```dart
final streamA = Stream.periodic(Duration(seconds: 2), (i) => 'A$i');
final streamB = Stream.periodic(Duration(seconds: 3), (i) => 'B$i');

streamA.merge(streamB).listen(print);
// 输出: A0, B0, A1, B1, A2, ...
```

**性能建议**  
在低端设备上处理高频事件（如触摸滑动）时，使用 `throttle` 是降低 UI 线程负载的有效手段，防止过快的 `setState()` 导致掉帧 。

---

## 九、资源管理与最佳实践

### 9.1 内存泄漏防范 

**必须关闭 StreamController**  
在 `StatefulWidget` 中使用 `StreamController` 时，必须在 `dispose()` 方法中调用 `_controller.close()`。如果不关闭，这根“水管”将永远留在内存中，造成严重的内存泄漏。

```dart
class MyWidget extends StatefulWidget {
  @override
  _MyWidgetState createState() => _MyWidgetState();
}

class _MyWidgetState extends State<MyWidget> {
  late final StreamController<int> _controller;
  StreamSubscription? _subscription;

  @override
  void initState() {
    super.initState();
    _controller = StreamController<int>();
    _subscription = _controller.stream.listen((data) {
      // 处理数据
    });
  }

  @override
  void dispose() {
    _subscription?.cancel();  // 取消订阅
    _controller.close();       // 关闭控制器
    super.dispose();
  }
}
```

### 9.2 创建流的方式选择

| 场景 | 推荐方式 |
|------|----------|
| 基于现有流转换 | 使用内置操作符  |
| 需要手动控制事件添加 | `StreamController`  |
| 从多个异步源生成数据 | `async*` 函数  |
| 需要高级操作（防抖、节流） | `stream_transform` 包  |
| 复杂、可复用的转换逻辑 | `StreamTransformer`  |
| 需要多监听者且缓存最新值 | `BehaviorSubject`（rxdart） |

### 9.3 错误处理最佳实践 

```dart
Future<int> sumStream(Stream<int> stream) async {
  var sum = 0;
  try {
    await for (final value in stream) {
      sum += value;
    }
  } catch (e) {
    print('捕获错误: $e');
    return -1;  // 或重新抛出
  }
  return sum;
}
```

### 9.4 重要规则总结 

1. **惰性初始化**：Stream 应该在有订阅者时才开始产生事件（`onListen` 回调是实现的关键）。  
2. **尊重暂停**：当订阅者暂停时，应该停止产生事件或缓存事件。  
3. **及时清理**：在 `onCancel` 回调中释放资源（关闭文件、取消定时器等）。  
4. **广播流谨慎**：广播流如果没有订阅者，事件会丢失。  
5. **单订阅流**：只能监听一次，用于文件 I/O 等连续数据块 。
```