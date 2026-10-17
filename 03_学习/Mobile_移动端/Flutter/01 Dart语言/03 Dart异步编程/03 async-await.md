## Dart 异步编程完整学习笔记（权威版）

本笔记基于 Dart 官方文档（dart.dev）及权威教材整理，涵盖 `Future`、`Stream`、`async`/`await`、`async*`/`await for` 等核心异步编程知识点，每个部分均按照 **概念 → 原理 → 使用方法 → 注意事项 → 代码示例** 的模板详细展开。

---

### 一、Future：异步操作的结果占位符

#### 概念
- **是什么**：`Future<T>` 是一个表示**将来可能会完成并产生一个 T 类型值**（或错误）的对象。它是 Dart 异步编程的基础单元，类似于 JavaScript 中的 Promise。
- **为什么需要**：执行耗时操作（如网络请求、文件读写）时，不希望阻塞当前线程，`Future` 允许我们注册回调，在操作完成后得到通知。
- **解决什么问题**：将异步操作的等待与结果处理解耦，避免线程阻塞，提升应用响应性。

#### 原理
- 当调用一个返回 `Future` 的函数时，该函数立即返回一个未完成的 `Future` 实例，并将实际任务提交给事件循环（Event Loop）。
- 任务完成后，结果（或错误）被放入事件队列，事件循环在适当的时候取出并调用对应的回调（如 `then`、`catchError`）。
- `Future` 内部维护一个状态：未完成（uncompleted）或已完成（completed，可能是 value 或 error）。

#### 使用方法
- **基本创建**：
  ```dart
  Future<String> fetchData() {
    return Future.delayed(Duration(seconds: 2), () => 'data');
  }
  ```
- **链式调用**：
  ```dart
  fetchData().then((value) {
    print(value);
  }).catchError((error) {
    print('Error: $error');
  }).whenComplete(() {
    print('完成');
  });
  ```
- **常用构造函数**：
  - `Future.value(value)`：立即完成的 Future。
  - `Future.error(error)`：立即失败的 Future。
  - `Future.delayed(duration, computation)`：延迟后执行。
  - `Future.sync(computation)`：立即同步执行计算，但返回 Future。

#### 注意事项
- `then` 和 `catchError` 返回新的 `Future`，可链式调用。
- 未捕获的错误会被传递到全局错误处理器，可能导致应用崩溃。
- 不要在 `then` 中执行耗时同步操作，否则会阻塞事件循环。

#### 代码示例
```dart
void main() {
  print('开始');
  Future.delayed(Duration(seconds: 1), () => 'Hello')
      .then((value) => print(value))
      .catchError((err) => print('出错：$err'))
      .whenComplete(() => print('完成'));
  print('结束');
}
// 输出：开始 → 结束 → Hello → 完成
```

---

### 二、async/await：简化 Future 的语法糖

#### 概念
- **是什么**：`async` 和 `await` 是 Dart 语言提供的关键字，用于以**同步风格编写异步代码**。`async` 标记函数为异步，`await` 等待 Future 完成。
- **为什么需要**：避免回调嵌套（“回调地狱”），提高代码可读性和可维护性。
- **解决什么问题**：让异步代码看起来像同步代码，同时保持非阻塞特性。

#### 原理
- `async` 函数被编译器转换为返回 `Future` 的状态机。
- 每个 `await` 表达式都会暂停当前函数的执行，将后续代码注册为 Future 完成时的回调，控制权交还给事件循环。
- Future 完成后，事件循环触发回调，函数从暂停点恢复执行。

#### 使用方法
- **基本用法**：
  ```dart
  Future<String> fetchOrder() async {
    var order = await getOrderFromNetwork(); // 暂停等待
    return order; // 自动包装为 Future<String>
  }
  ```
- **多个 await**：顺序执行。
- **返回值**：`async` 函数必须返回 `Future`，即使看起来返回普通值，也会自动包装。

#### 注意事项
- `await` 只能在 `async` 函数内使用。
- 如果一个函数内没有 `await`，声明为 `async` 会带来不必要的开销，应避免。
- 多个独立的异步任务不要用 `await` 顺序执行，应并发执行（见后文 `Future.wait`）。

#### 代码示例
```dart
Future<String> fetchUser() async {
  await Future.delayed(Duration(seconds: 1));
  return 'Alice';
}

Future<String> fetchOrder(String user) async {
  await Future.delayed(Duration(seconds: 1));
  return '$user\'s order';
}

void main() async {
  print('开始');
  var user = await fetchUser();
  var order = await fetchOrder(user);
  print(order); // Alice's order
}
```

---

### 三、异步错误处理：try-catch 与 catchError

#### 概念
- **是什么**：处理异步操作中可能发生的异常，保证程序健壮性。
- **为什么需要**：异步操作的失败（如网络超时、数据解析错误）若不处理，会导致 Future 以错误状态完成，可能被全局未处理异常捕获，甚至终止程序。
- **解决什么问题**：提供与同步代码一致的错误处理方式，简化异步异常捕获。

#### 原理
- `try-catch` 在 `async` 函数中捕获 `await` 表达式抛出的错误，相当于 `Future` 的 `catchError`。
- 未使用 `await` 时，需用 `catchError` 捕获。

#### 使用方法
- **try-catch 与 async/await**：
  ```dart
  Future<void> main() async {
    try {
      var data = await fetchData();
      print(data);
    } catch (e) {
      print('出错：$e');
    } finally {
      print('清理资源');
    }
  }
  ```
- **catchError 链式调用**：
  ```dart
  fetchData()
      .then((data) => print(data))
      .catchError((e) => print('出错：$e'))
      .whenComplete(() => print('完成'));
  ```

#### 注意事项
- `catchError` 可以捕获前面所有 `then` 中抛出的错误，但需注意错误类型匹配（可传入 `test` 参数过滤特定类型）。
- 在 `then` 中返回一个 Future 时，其错误也会被后续的 `catchError` 捕获。
- `try-catch` 只能捕获 `await` 表达式的错误，不能捕获 `Future` 创建时的同步错误（如直接 `throw`），需在 Future 内部处理。

#### 代码示例
```dart
Future<String> riskyOperation() async {
  if (DateTime.now().second.isEven) {
    throw Exception('偶数秒出错');
  }
  return '成功';
}

void main() async {
  try {
    var result = await riskyOperation();
    print(result);
  } on Exception catch (e) {
    print('捕获异常：$e');
  } catch (e) {
    print('其他错误：$e');
  }
}
```

### 四、并发异步操作：Future.wait、Future.any 等

#### 概念
- **是什么**：同时执行多个独立的异步任务，并在所有（或某个）任务完成后得到通知。
- **为什么需要**：避免串行执行浪费等待时间，提高吞吐量。
- **解决什么问题**：并行请求资源，聚合结果。

#### 原理
- `Future.wait` 接收一个 `Iterable<Future>`，返回一个 `Future<List>`，当所有 Future 完成时，返回的结果列表顺序与输入顺序一致。若任意 Future 失败，返回的 Future 立即以该错误完成。
- `Future.any` 返回第一个完成的 Future 的结果。
- `Future.doWhile` 等用于循环异步操作。

#### 使用方法
- **Future.wait**：
  ```dart
  Future<void> fetchAll() async {
    var futures = [fetchUser(), fetchPosts(), fetchComments()];
    var results = await Future.wait(futures);
    print('用户：${results[0]}');
    print('帖子：${results[1]}');
  }
  ```
- **Future.any**：
  ```dart
  var first = await Future.any([slowServer(), fastServer()]);
  ```
- **Future.forEach**：对集合中的每个元素执行异步操作，可控制并发度（通过手动实现）。

#### 注意事项
- `Future.wait` 中只要有一个 Future 失败，整个 wait 就失败。若需部分成功，可考虑 `Future.wait` 的 `eagerError: false` 参数（Dart 2.15+），或使用 `Future.wait` 结合 `catchError` 处理单个 Future。
- 大量并发可能耗尽系统资源，需适当限制。

#### 代码示例
```dart
Future<String> task(int id, Duration delay) async {
  await Future.delayed(delay);
  return 'Task $id done';
}

void main() async {
  var tasks = [
    task(1, Duration(seconds: 2)),
    task(2, Duration(seconds: 1)),
    task(3, Duration(seconds: 3)),
  ];
  var results = await Future.wait(tasks);
  print(results); // [Task 1 done, Task 2 done, Task 3 done]
}
```

### 五、Stream：异步数据序列

#### 概念
- **是什么**：`Stream<T>` 是一个异步数据源的抽象，可以连续产生多个值，如文件行、按钮点击、WebSocket 消息等。
- **为什么需要**：处理随时间到达的一系列数据，而非单一结果。
- **解决什么问题**：响应式编程，监听数据流的变化。

#### 原理
- Stream 通过事件循环分发数据。它有两种类型：
  - **单订阅（Single-subscription）**：只能有一个监听者，适合一次性数据流（如文件读取）。
  - **广播（Broadcast）**：允许多个监听者，适合持续事件（如用户输入）。
- 数据从 Stream 的源头通过 `add` 或 `yield` 发出，经过可能存在的转换，最终被监听者接收。

#### 使用方法
- **创建 Stream**：
  - 使用 `Stream.fromIterable`、`Stream.periodic` 等工厂构造。
  - 使用 `async*` 生成器。
  - 使用 `StreamController` 手动控制。
- **监听 Stream**：
  - `listen` 方法：提供 onData、onError、onDone 回调。
  - `await for` 循环（需在 async 函数内）。
- **转换 Stream**：
  - `map`、`where`、`expand`、`take` 等，返回新 Stream。

#### 注意事项
- 单订阅 Stream 只能被监听一次，重复监听会抛出异常。
- 必须处理 onError 和 onDone，否则可能导致资源泄漏或未捕获异常。
- 使用 `listen` 返回的 `StreamSubscription` 可暂停、恢复、取消订阅。

#### 代码示例
```dart
void main() {
  var stream = Stream.periodic(Duration(seconds: 1), (i) => i).take(5);
  stream.listen(
    (data) => print('数据：$data'),
    onError: (err) => print('错误：$err'),
    onDone: () => print('流结束'),
  );
}
```

### 六、生成 Stream：async* 与 yield

#### 概念
- **是什么**：`async*` 标记一个函数为**异步生成器**，它返回一个 `Stream`，并使用 `yield` 发射值。
- **为什么需要**：以简洁的方式创建自定义 Stream，避免手动编写 StreamController 的样板代码。
- **解决什么问题**：方便地生成连续异步数据，如计时器、分页加载数据。

#### 原理
- `async*` 函数内部遇到 `yield` 时，会将值添加到 Stream 中，但函数不会结束。函数可以包含循环，通过 `await` 等待异步操作，然后继续 `yield`。
- 当函数执行完毕时，Stream 自动关闭。

#### 使用方法
- **基本语法**：
  ```dart
  Stream<int> countStream(int max) async* {
    for (int i = 1; i <= max; i++) {
      await Future.delayed(Duration(seconds: 1));
      yield i; // 发射 i 到 Stream
    }
  }
  ```
- **yield***：可发射另一个 Stream 的所有值。
  ```dart
  Stream<int> combined() async* {
    yield* countStream(3);
    yield* countStream(2);
  }
  ```

#### 注意事项
- `async*` 函数必须返回 `Stream`。
- 内部可以使用 `await` 等待 Future，但注意不要在 `yield` 后忘记 `await` 导致时序错误。
- 不要在 `async*` 函数内使用 `return` 返回值，它只用于结束 Stream。

#### 代码示例
```dart
Stream<String> getNews() async* {
  var page = 1;
  while (page <= 3) {
    await Future.delayed(Duration(seconds: 1));
    yield '新闻第 $page 页';
    page++;
  }
}

void main() async {
  await for (var news in getNews()) {
    print(news);
  }
}
```

### 七、消费 Stream：await for 与 listen

#### 概念
- **是什么**：两种监听 Stream 数据的方式：`await for` 循环和 `listen` 方法。
- **为什么需要**：根据场景选择更合适的消费方式。
- **解决什么问题**：遍历异步数据序列。

#### 原理
- `await for` 每次迭代会暂停直到 Stream 发出下一个值，类似同步的 `for-in` 但用于异步。
- `listen` 方法注册回调，当数据到达时立即执行，更灵活（可暂停、取消）。

#### 使用方法
- **await for**（适合需要逐个处理且逻辑较复杂的情况）：
  ```dart
  Future<void> printNumbers() async {
    await for (var num in countStream(5)) {
      print(num);
    }
  }
  ```
- **listen**（适合需要精细控制或只需监听一次的情况）：
  ```dart
  void listenNumbers() {
    var subscription = countStream(5).listen(
      (num) => print(num),
      onDone: () => print('完成'),
    );
    // 稍后可以 subscription.pause() 或 cancel()
  }
  ```

#### 注意事项
- `await for` 必须在 `async` 函数内使用。
- `await for` 会一直消耗 Stream 直到关闭，无法中途暂停，除非使用 `break`。
- `listen` 返回的 `StreamSubscription` 必须妥善管理，避免内存泄漏。

#### 代码示例
```dart
Stream<int> odds() async* {
  for (int i = 1; i <= 5; i += 2) {
    await Future.delayed(Duration(milliseconds: 500));
    yield i;
  }
}

void main() async {
  // 使用 await for
  await for (var odd in odds()) {
    print('奇数：$odd');
  }

  // 使用 listen
  odds().listen((odd) => print('监听：$odd'));
}
```

### 八、StreamController：手动控制 Stream

#### 概念
- **是什么**：`StreamController<T>` 是一个类，允许你手动创建 Stream，并通过它添加数据、错误，以及关闭 Stream。
- **为什么需要**：当数据来源无法用 `async*` 方便表达时（如事件监听、旧式回调），需要手动控制 Stream。
- **解决什么问题**：将任何数据源适配为 Stream。

#### 原理
- StreamController 内部管理一个 Stream 和对应的 Sink（用于添加数据）。它提供 `stream` 属性供外部监听，提供 `sink`（或 `add`、`addError`、`close`）供内部写入。

#### 使用方法
- **创建并监听**：
  ```dart
  final controller = StreamController<String>();
  controller.stream.listen((data) => print(data));
  controller.add('Hello');
  controller.add('World');
  controller.close();
  ```
- **广播控制器**：`StreamController.broadcast()` 创建广播 Stream，允许多个监听。
- **同步 vs 异步**：默认控制器是异步的（数据放入事件队列），也可创建同步控制器（直接触发监听，但需谨慎使用）。

#### 注意事项
- 使用完必须调用 `close()` 释放资源，否则可能导致内存泄漏。
- 如果控制器被垃圾回收但未关闭，其内部资源可能泄漏。
- 广播控制器可以多次监听，但必须在添加数据之前就注册监听，否则可能错过数据。

#### 代码示例
```dart
import 'dart:async';

class Button {
  final _onClick = StreamController<String>.broadcast();
  Stream<String> get onClick => _onClick.stream;

  void click(String user) {
    _onClick.add('$user clicked button');
  }

  void dispose() {
    _onClick.close();
  }
}

void main() {
  var button = Button();
  button.onClick.listen((msg) => print('Listener 1: $msg'));
  button.onClick.listen((msg) => print('Listener 2: $msg'));

  button.click('Alice');
  button.click('Bob');
  button.dispose();
}
```

### 九、转换 Stream：transform、map 等

#### 概念
- **是什么**：Stream 提供一系列变换方法，如 `map`、`where`、`expand`、`take`，以及更强大的 `transform`。
- **为什么需要**：对数据流进行过滤、映射、聚合等操作，形成新的流。
- **解决什么问题**：实现声明式的数据处理管道。

#### 原理
- 大多数变换方法（如 `map`）内部创建新的 Stream，当原 Stream 有数据时，应用变换函数后将结果添加到新 Stream。
- `transform` 使用 `StreamTransformer`，可以更灵活地处理复杂转换（如缓冲、编解码）。

#### 使用方法
- **简单变换**：
  ```dart
  Stream.fromIterable([1, 2, 3])
      .map((i) => i * 2)
      .where((i) => i > 3)
      .listen(print); // 4, 6
  ```
- **transform 示例**：
  ```dart
  var lineStream = utf8.decoder
      .bind(fileStream)
      .transform(LineSplitter());
  ```
- **自定义 StreamTransformer**：
  ```dart
  class DuplicateTransformer<S> extends StreamTransformerBase<S, S> {
    @override
    Stream<S> bind(Stream<S> stream) async* {
      await for (var value in stream) {
        yield value;
        yield value; // 重复一次
      }
    }
  }
  ```

#### 注意事项
- 变换是惰性的，只有在监听结果 Stream 时才会触发。
- 变换可能改变 Stream 的类型。
- `transform` 是处理 Stream 的高级接口，可组合复杂操作。

#### 代码示例
```dart
void main() async {
  var stream = Stream.periodic(Duration(seconds: 1), (i) => i).take(5);
  var evenStream = stream.where((i) => i.isEven).map((i) => '偶数 $i');
  await for (var s in evenStream) {
    print(s);
  }
}
```

### 十、广播 Stream（Broadcast Stream）

#### 概念
- **是什么**：允许同时有多个监听者的 Stream。与之相对，单订阅 Stream 只允许一个监听。
- **为什么需要**：适用于事件源，如用户点击、传感器数据等，需要被多个消费者监听。
- **解决什么问题**：支持一对多的数据分发。

#### 原理
- 广播 Stream 在添加数据时，会同时将数据分发给所有当前活跃的监听者。
- 如果添加数据时没有监听者，该数据可能会被丢弃（取决于实现，但通常广播 Stream 不保留历史数据）。

#### 使用方法
- **创建广播 Stream**：
  - `StreamController.broadcast()` 创建的控制器。
  - 使用 `Stream` 的 `asBroadcastStream()` 将单订阅转为广播（需谨慎，会缓存数据给新监听者？实际上 `asBroadcastStream` 的行为是：如果原 Stream 已开始，新监听者会从当前时间点开始接收数据，但不会收到历史数据）。
  - 某些工厂如 `Stream.periodic` 不是广播流，需手动转换。
- **监听**：任意多个。

#### 注意事项
- 广播 Stream 不能使用 `await for` 同时多次监听？实际上可以，但需注意每个 `await for` 会独立消费流，可能相互干扰。通常 `await for` 用于单订阅流，广播流推荐用 `listen`。
- 错误处理：广播 Stream 中，如果一个监听者出错，不影响其他监听者。
- 性能：大量监听者可能影响性能。

#### 代码示例
```dart
void main() {
  var controller = StreamController<int>.broadcast();
  controller.stream.listen((v) => print('A: $v'));
  controller.stream.listen((v) => print('B: $v'));

  controller.add(1);
  controller.add(2);
  controller.close();
}
// 输出顺序可能 A1, B1, A2, B2 或交错，取决于事件循环。
```

### 十一、异步编程最佳实践与注意事项

#### 概念
- **是什么**：编写高效、健壮、可维护异步代码的指导原则。
- **为什么需要**：避免常见陷阱，提升代码质量。
- **解决什么问题**：确保异步程序稳定运行，易于调试和扩展。

#### 最佳实践
1. **优先使用 async/await**：取代显式 `then`，代码更清晰。
2. **并发执行独立任务**：使用 `Future.wait` 或 `Stream` 合并。
3. **及时取消订阅**：对于不需要的 Stream 订阅，调用 `cancel()` 防止内存泄漏。
4. **处理所有错误**：无论 `try-catch` 还是 `catchError`，确保异步链中的错误都被捕获。
5. **避免在 async 函数中执行耗时同步计算**：长时间循环会阻塞事件循环，可考虑使用 `Isolate`。
6. **使用 `Future.value` 和 `Future.error` 创建已完成的 Future**，提高性能。
7. **在销毁对象前关闭 StreamController**：在 `dispose` 或 `close` 方法中调用 `close()`。
8. **选择合适的 Stream 类型**：单订阅用于一次性数据流，广播用于多监听事件。
9. **使用 `async*` 生成 Stream 时注意资源释放**：确保生成器能正常结束。
10. **测试异步代码**：使用 `expectLater` 与 `completion`、`throwsA` 等匹配器。

#### 常见错误
- **忘记 await**：导致拿到 Future 而非值。
- **在非 async 函数中使用 await**：编译错误。
- **未处理的 Future 错误**：可能导致“Unhandled exception”崩溃。
- **多个 await 导致串行**：错失并发机会。
- **过早取消订阅**：导致数据丢失。
- **在广播流上使用 await for**：可能造成逻辑混乱（多个 `await for` 会独立消费流，可能导致数据被重复处理或丢失，取决于流的性质）。

#### 性能问题
- 大量短时 Future 可能造成事件循环过载，可考虑合并或缓冲。
- 不必要的 `async` 函数会增加微任务开销。
- Stream 转换链过长可能影响性能，但通常可接受。

#### 代码示例（综合实践）
```dart
// 一个模拟的新闻客户端，并发获取用户信息和新闻列表，然后处理并显示。
Future<void> fetchNews() async {
  try {
    // 并发执行两个任务
    var results = await Future.wait([
      fetchUserProfile(),
      fetchHeadlines(),
    ]);

    var user = results[0] as User;
    var headlines = results[1] as List<Article>;

    // 使用 Stream 转换处理新闻流
    var stream = Stream.fromIterable(headlines);
    var filtered = stream.where((a) => a.category == user.preference);
    await for (var article in filtered) {
      displayArticle(article);
    }
  } catch (e) {
    showError('加载失败：$e');
  } finally {
    hideLoadingIndicator();
  }
}
```