# Dart 异常处理完整学习笔记

## Exception vs Error

### 概念
- **是什么**：Dart中将所有可抛出对象分为两大类——`Exception`和`Error`，它们都是`Object`的子类型，但代表不同性质的故障。
  - **Exception（异常）**：表示程序运行时发生的、可预见的意外情况，例如网络超时、文件不存在、数据格式错误等。这些是应用程序应该能够处理并从中恢复的情况。
  - **Error（错误）**：表示程序内部的严重问题，通常是程序员的错误或系统资源耗尽，例如空指针调用、类型转换失败、断言失败等。这些是**不应该被捕获**的，因为它们指示程序状态已损坏。
- **为什么需要**：通过明确的分类，Dart希望开发者能针对不同类型的故障采取不同的处理策略——`Exception`应该被捕获并处理，`Error`则应让程序终止以暴露编程错误。
- **解决什么问题**：区分“可恢复的业务异常”和“不可恢复的程序错误”，避免将本应修复的代码错误当作业务异常捕获，也避免将业务异常遗漏导致程序崩溃。

### 原理
- **底层实现**：`Exception`和`Error`都是接口（或抽象类），内置实现如`FormatException`继承自`Exception`，`ArgumentError`继承自`Error`。Dart虚拟机在抛出对象时不会强制检查类型，但语言规范建议遵循这一分类。
- **工作流程**：
  1. 当使用`throw`抛出对象时，该对象会沿着调用栈向上传播。
  2. 传播过程中，如果遇到匹配的`catch`块，则进入该块处理；否则继续向上。
  3. 如果到达顶层仍未捕获，程序终止（对于`Error`）或当前isolate被挂起（对于未捕获的`Exception`）。
- **关键技术点**：与Java不同，**Dart的所有异常都是非检查异常（unchecked exceptions）**——方法不会声明它们可能抛出哪些异常，调用者也不强制要求捕获任何异常。

### 使用方法
- **基本用法**：直接使用内置的异常或错误类，或自定义子类。
  ```dart
  void checkAge(int age) {
    if (age < 0) throw ArgumentError('年龄不能为负数'); // Error
    if (age > 150) throw Exception('年龄超出合理范围');   // Exception
  }
  ```
- **常见场景**：
  - 业务逻辑验证失败 → 抛出`Exception`（如`LoginException`）。
  - API调用参数错误 → 抛出`ArgumentError`（属于`Error`）。
  - 外部依赖不可用 → 抛出`TimeoutException`（属于`Exception`）。
- **最佳实践**：
  - 永远不要捕获`Error`，除非你有明确理由（如日志记录后重新抛出）。
  - 自定义异常应继承`Exception`，自定义错误应继承`Error`，遵循语言设计哲学。

### 注意事项
- **常见错误**：混淆两者，将程序错误（如空指针）用`try-catch`捕获，导致隐藏了本该修复的bug。
- **性能问题**：抛出异常本身有一定开销，但通常不影响整体性能；过度使用异常作为控制流应避免。
- **兼容性**：所有Dart实现（VM、JavaScript编译）都遵循这一分类。

### 代码示例
```dart
void main() {
  try {
    processData('');
  } on ArgumentError catch (e) {
    print('程序错误：参数非法，请修复代码 - $e');
    // 不应该在这里恢复，应记录并终止或重新抛出
    rethrow;
  } on Exception catch (e) {
    print('业务异常：可恢复 - $e');
    // 执行恢复逻辑，如重试、返回默认值
  }
}

void processData(String input) {
  if (input.isEmpty) throw ArgumentError('input不能为空'); // Error
  if (!isValidFormat(input)) throw FormatException('格式错误'); // Exception
}
```

---

## try-catch-finally

### 概念
- **是什么**：Dart提供的用于捕获和处理异常的语法结构。
  - `try`：包裹可能抛出异常的代码块。
  - `catch`：捕获异常并执行处理逻辑。
  - `finally`：无论是否发生异常都会执行的代码块，通常用于释放资源。
- **为什么需要**：防止异常导致程序崩溃，并提供机会进行错误恢复、日志记录或资源清理。
- **解决什么问题**：结构化异常处理，使正常流程与错误处理分离，提高代码可读性。

### 原理
- **底层实现**：Dart VM在遇到`throw`时会创建异常对象，并展开调用栈寻找匹配的`catch`块。`finally`块通过编译器生成的代码保证在任何退出路径（正常返回、异常抛出）前执行。
- **工作流程**：
  1. 执行`try`块内的代码。
  2. 如果发生异常，跳转到第一个匹配的`catch`块（按顺序匹配`on`类型）。
  3. 执行`catch`块中的处理代码。
  4. 无论是否发生异常，最后执行`finally`块（如果有）。
  5. 如果`catch`块中重新抛出异常，`finally`仍会在重新抛出前执行。
- **关键技术点**：`catch`可以带一个或两个参数——异常对象和堆栈信息（`StackTrace`对象）。

### 使用方法
- **基本用法**：
  ```dart
  try {
    // 可能出错的代码
  } catch (e) {
    // 处理任何异常
  } finally {
    // 清理工作
  }
  ```
- **常见场景**：
  - 文件操作：打开文件后，无论读写是否成功，都应在`finally`中关闭文件。
  - 网络请求：捕获超时异常，进行重试。
  - 数据库事务：在异常时回滚，在`finally`中释放连接。
- **最佳实践**：
  - 尽可能精确捕获异常类型，避免过度宽泛的`catch`（如`catch (e)`）掩盖未知错误。
  - 在`catch`中只处理当前层能恢复的错误，否则应重新抛出。
  - `finally`中不应抛出异常，否则会覆盖原有异常（如果`finally`抛出异常，原异常会丢失）。

### 注意事项
- **常见错误**：在`catch`后忘记`rethrow`导致异常被吞没；在`finally`中执行可能抛出异常的代码，破坏清理逻辑。
- **性能问题**：进入`try`块几乎没有开销，但抛出异常并展开堆栈有一定成本，因此异常应只用于异常情况，而非正常控制流。
- **兼容性**：所有Dart环境都支持。

### 代码示例
```dart
import 'dart:io';

void main() {
  File? file;
  try {
    file = File('data.txt');
    String content = file.readAsStringSync();
    print(content);
  } on FileSystemException catch (e) {
    print('文件系统错误: $e');
  } catch (e, s) {
    print('未知错误: $e');
    print('堆栈: $s');
  } finally {
    file?.close(); // 确保文件关闭
    print('清理完成');
  }
}
```

---

## on关键字指定异常类型

### 概念
- **是什么**：`on`是`try-catch`语法的一部分，用于指定要捕获的异常类型，只有匹配该类型的异常才会被该`catch`块处理。
- **为什么需要**：不同类型的异常需要不同的处理策略，例如网络超时可能需要重试，而数据格式错误可能需要提示用户。
- **解决什么问题**：实现异常的分类处理，使代码能针对特定异常执行定制逻辑。

### 原理
- **底层实现**：Dart的异常类型匹配基于运行时类型检查（`on Type`等同于`e is Type`）。当异常被抛出时，运行时按顺序检查每个`catch`块的`on`类型，第一个匹配的块被执行。
- **工作流程**：
  1. 异常对象沿栈向上传播。
  2. 遇到`try`块后，依次检查其后的`catch`块。
  3. 如果`catch`块带有`on`类型，则判断异常对象是否是该类型或其子类。
  4. 匹配成功则进入该块；否则继续检查下一个`catch`块。
- **关键技术点**：`on`可以与`catch`联合使用（`on SomeException catch (e)`），也可以单独使用（`on SomeException`）如果不需要异常对象。

### 使用方法
- **基本用法**：
  ```dart
  try {
    // ...
  } on TimeoutException {
    // 处理超时，不需要异常对象
  } on FormatException catch (e) {
    // 处理格式异常，需要对象
    print('格式错误: $e');
  } on Exception catch (e) {
    // 捕获其他所有Exception
  } catch (e) {
    // 捕获所有非Exception对象（如Error）
  }
  ```
- **常见场景**：
  - 网络请求：区分超时、连接失败、服务器错误等。
  - 解析数据：区分JSON格式错误、字段缺失等。
  - 自定义异常：精确捕获业务异常（如`LoginException`）并执行特定UI反馈。
- **最佳实践**：
  - 将最具体的异常类型放在前面，通用类型放在后面（如先`on FormatException`，再`on Exception`）。
  - 如果只关心类型而不需要异常对象，使用`on Type`，避免不必要的变量声明。
  - 不要使用`on`捕获`Error`类型，除非确实需要（通常应让程序崩溃以暴露bug）。

### 注意事项
- **常见错误**：类型顺序颠倒导致通用类型先捕获，使具体类型处理块永远无法执行。
- **性能问题**：类型检查开销极小，可忽略。
- **兼容性**：所有Dart环境均支持。

### 代码示例
```dart
void parseAndProcess(String jsonString) {
  try {
    var data = jsonDecode(jsonString);
    // 处理数据...
  } on FormatException catch (e) {
    print('JSON格式错误: $e');
    // 提示用户输入有误
  } on ArgumentError catch (e) {
    print('程序错误: 传入的字符串不是JSON格式? 这不应发生');
    rethrow; // 让开发环境捕获
  } on Exception catch (e) {
    print('其他异常: $e');
  }
}
```

---

## rethrow

### 概念
- **是什么**：`rethrow`关键字用于在`catch`块中重新抛出当前捕获的异常，同时**保留原始的堆栈信息**。
- **为什么需要**：有时在当前层级只能部分处理异常（如记录日志），但无法完全恢复，需要将异常继续向上传播，由上层决定最终处理。
- **解决什么问题**：在不丢失原始异常上下文的前提下，实现异常的局部处理和传播。

### 原理
- **底层实现**：`rethrow`并非简单的`throw e`，它会保留异常最初的抛出位置和堆栈轨迹。在VM层面，`rethrow`使用特殊的指令，使得堆栈信息不会因为重新抛出而被重置。
- **工作流程**：
  1. 进入`catch`块，执行部分处理逻辑。
  2. 调用`rethrow`，将当前异常对象重新抛出。
  3. 异常继续沿调用栈向上传播，携带原始的堆栈信息。
- **关键技术点**：`rethrow`只能在`catch`块内部使用，且无需指定异常对象（它自动引用当前捕获的异常）。

### 使用方法
- **基本用法**：
  ```dart
  void func() {
    try {
      doSomething();
    } catch (e) {
      print('记录日志: $e');
      rethrow; // 继续抛出，让调用者处理
    }
  }
  ```
- **常见场景**：
  - AOP风格的日志记录：在方法入口统一记录异常，但不处理，让业务层处理。
  - 资源清理：捕获异常进行部分清理后，重新抛出让上层知道失败。
- **最佳实践**：
  - 除非你确定要吞掉异常，否则在无法完全恢复时总是使用`rethrow`。
  - 不要使用`throw e`代替`rethrow`，除非你故意要重置堆栈（很少需要）。

### 注意事项
- **常见错误**：在`catch`块中使用`throw e`导致堆栈信息丢失，使得调试困难。
- **性能问题**：`rethrow`几乎无额外开销。
- **兼容性**：Dart 1.0起支持。

### 代码示例
```dart
void fetchData() {
  try {
    http.get('...');
  } on TimeoutException catch (e) {
    // 记录超时，但依然告知调用者失败
    print('超时发生在fetchData');
    rethrow;
  }
}

void main() {
  try {
    fetchData();
  } catch (e) {
    print('最终处理: $e'); // 堆栈指向原始的http.get调用，而非rethrow行
  }
}
```

---

## 自定义异常

### 概念
- **是什么**：开发者根据业务需求创建的、继承自`Exception`（或实现`Exception`接口）的类，用于表达特定的业务错误。
- **为什么需要**：内置异常无法准确描述业务场景（如“余额不足”、“用户不存在”），自定义异常可以携带丰富的业务上下文信息（如错误码、字段名）。
- **解决什么问题**：提高代码可读性和可维护性，使异常处理逻辑能与业务紧密结合。

### 原理
- **底层实现**：自定义异常类通常实现`Exception`接口，可以添加任意字段和方法。当抛出时，它就是一个普通对象，遵循相同的异常传播机制。
- **工作流程**：与任何异常相同，但可以被`on`精确捕获。
- **关键技术点**：通常覆盖`toString()`方法以提供友好的错误信息，方便日志记录。

### 使用方法
- **基本用法**：
  ```dart
  class InsufficientFundsException implements Exception {
    final double balance;
    final double amount;
    InsufficientFundsException(this.balance, this.amount);
    
    @override
    String toString() => '余额不足：需 $amount，现有 $balance';
  }

  void withdraw(double amount) {
    if (amount > balance) {
      throw InsufficientFundsException(balance, amount);
    }
    balance -= amount;
  }
  ```
- **常见场景**：
  - 业务逻辑层：验证失败抛出业务异常（如`OrderCancellationNotAllowedException`）。
  - 数据访问层：封装数据库错误为特定异常（如`DuplicateKeyException`）。
- **最佳实践**：
  - 命名清晰，以`Exception`或`Error`结尾，表明其用途。
  - 包含足够诊断信息（错误码、上下文数据）。
  - 优先实现`Exception`而非`Error`，除非是程序错误。

### 注意事项
- **常见错误**：自定义异常继承自`Object`而不是`Exception`，导致无法被`on Exception`捕获（但可以被`catch`捕获）。建议显式实现`Exception`接口。
- **性能问题**：自定义异常本身无额外开销，但过多字段可能增加对象大小。
- **兼容性**：完全兼容所有Dart环境。

### 代码示例
```dart
// 定义带错误码的业务异常
class ApiException implements Exception {
  final int statusCode;
  final String message;
  ApiException(this.statusCode, this.message);
  
  @override
  String toString() => 'ApiException $statusCode: $message';
}

// 使用
Future<void> fetchUser() async {
  try {
    var response = await http.get('/user');
    if (response.statusCode != 200) {
      throw ApiException(response.statusCode, '获取用户失败');
    }
    // ...
  } on ApiException catch (e) {
    if (e.statusCode == 401) {
      // 跳转到登录页
    } else {
      rethrow;
    }
  }
}
```

---

## assert断言

### 概念
- **是什么**：`assert`是Dart提供的一种开发期间的工具，用于检查不应该发生的条件——如果布尔条件为`false`，则中断正常执行并抛出`AssertionError`。
- **为什么需要**：在开发阶段尽早发现程序逻辑错误，帮助开发者快速定位问题。
- **解决什么问题**：提供一种声明式的方式验证不变式、前置条件和后置条件，且在生产环境零成本（断言被忽略）。

### 原理
- **底层实现**：`assert`语句在开发模式下被编译为检查代码；在生产模式下，`assert`语句被完全忽略，其参数甚至不会被求值。
- **工作流程**：
  1. 代码执行到`assert(condition, message)`。
  2. 如果当前环境启用了断言（如Flutter调试模式、带`--enable-asserts`标志的`dart run`），则计算`condition`。
  3. 如果`condition`为`false`，抛出`AssertionError`（可选携带消息）。
  4. 如果未启用断言，`assert`不产生任何代码，继续执行。
- **关键技术点**：断言启用与否取决于运行环境——Flutter在调试模式默认启用；`webdev serve`默认启用；`dart run`和`dart compile js`需要通过`--enable-asserts`标志启用。

### 使用方法
- **基本用法**：
  ```dart
  // 确保变量非空
  assert(text != null);
  
  // 确保值小于100
  assert(number < 100);
  
  // 确保这是https URL
  assert(urlString.startsWith('https'));
  
  // 附带消息
  assert(
    urlString.startsWith('https'),
    'URL ($urlString) 应该以 "https" 开头',
  );
  ```
- **常见场景**：
  - 函数前置条件检查：验证传入参数的有效性。
  - 类不变式检查：验证对象状态的一致性。
  - 测试和调试：验证预期行为。
- **最佳实践**：
  - 仅在检查**绝不应该发生**的条件时使用`assert`——不是用于验证用户输入，而是验证程序内部逻辑。
  - 对于必须在生产环境执行的校验（如防止`null`导致崩溃），应主动抛出`Error`（如`ArgumentError`），而不是依赖`assert`，因为断言在生产环境无效。

### 注意事项
- **常见错误**：在生产环境中依赖`assert`进行输入验证，导致安全漏洞或程序崩溃。
- **性能问题**：生产环境无开销，因为断言被完全忽略。
- **兼容性**：所有Dart环境都支持，但启用与否取决于运行时标志。

### 代码示例
```dart
void setProfile({required String name, int? age}) {
  // 开发阶段确保名称非空
  assert(name.isNotEmpty, '名称不能为空');
  
  // 但生产环境也需要保护——使用显式检查
  if (name.isEmpty) {
    throw ArgumentError('名称不能为空');
  }
  
  if (age != null) {
    assert(age >= 0 && age <= 150, '年龄必须在0-150之间');
    if (age < 0 || age > 150) {
      throw ArgumentError('年龄必须在0-150之间');
    }
  }
  // 设置profile...
}
```

---

## Zone：全局异常捕获

### 概念
- **是什么**：Zone是Dart提供的一种执行环境沙箱，可以捕获、拦截或修改代码行为——包括所有未处理的同步和异步异常。
- **为什么需要**：`try-catch`无法捕获异步回调中发生的异常（如`Future`、定时器中的错误），Zone提供了一种全局捕获所有未处理异常的机制。
- **解决什么问题**：实现应用级别的统一异常处理，防止因未捕获的异步异常导致应用崩溃，并集中收集错误日志。

### 原理
- **底层实现**：Zone表示一个代码执行的动态范围，创建新Zone时（如`runZoned`或`runZonedGuarded`），所有在该Zone内注册的异步回调（`then`、`Timer`、`microtask`等）都会在该Zone中执行。
- **工作流程**：
  1. 使用`runZonedGuarded()`创建带有错误处理器的Zone。
  2. 在Zone内执行的代码（包括所有异步回调）如果抛出未捕获异常，都会进入该错误处理器。
  3. 错误处理器接收异常对象和堆栈信息。
  4. Zone会继续执行其他任务，不会因为一个未捕获异常而终止整个应用。
- **关键技术点**：
  - **错误边界**：错误不能跨越error-zone的边界。如果在一个error-zone内发生未捕获异常，它会被该zone捕获，不会传播到父zone。
  - **微任务与事件队列**：Dart单线程模型包含微任务队列（microtask queue）和事件队列（event queue），Zone能捕获两个队列中的所有异常。
  - **zoneValues**：可以在Zone中存储私有数据，通过`Zone.current[key]`访问。

### 使用方法
- **基本用法**：
  ```dart
  runZonedGuarded(() {
    // 在这里执行的代码，包括异步回调
    Timer.run(() { 
      throw '这个错误会被zone捕获'; 
    });
    
    Future.error('这个也会被捕获');
    
    // 同步异常同样会被捕获
    throw Exception('同步异常');
  }, (error, stackTrace) {
    // 所有未捕获异常都会进入这里
    print('捕获到错误: $error');
    print('堆栈: $stackTrace');
    // 上报到错误监控平台
  });
  ```
- **常见场景**：
  - Flutter应用全局错误捕获，防止崩溃并收集日志。
  - 服务器应用中隔离请求，每个请求运行在独立Zone，一个请求的异常不影响其他请求。
  - 在测试中捕获异步代码的未处理错误。
- **最佳实践**：
  - 在应用入口（`main`函数）使用`runZonedGuarded`包裹整个应用，实现全局错误捕获。
  - 结合`FlutterError.onError`捕获Flutter框架层面的错误，实现完整覆盖。
  - 使用`zoneSpecification`拦截`print`等行为，收集所有日志。

### 注意事项
- **常见错误**：认为Zone能捕获所有异常——但Zone只捕获**未处理**的异常，如果代码中有`try-catch`捕获了异常，Zone不会收到该异常。
- **性能问题**：创建Zone有轻微开销，但通常可忽略；过多嵌套Zone可能影响可读性。
- **兼容性**：所有Dart环境（VM、Flutter、Web）都支持Zone。

### 代码示例
```dart
import 'dart:async';

void main() {
  // 全局错误捕获
  runZonedGuarded(() {
    // 启动应用
    runApp(MyApp());
    
    // 模拟异步错误
    Future.delayed(Duration(seconds: 1), () {
      throw Exception('异步错误演示');
    });
    
    // 定时器中的错误
    Timer.periodic(Duration(seconds: 2), (timer) {
      if (timer.tick == 3) {
        throw '定时器错误';
      }
    });
  }, (error, stackTrace) {
    // 统一错误处理
    print('🚨 捕获到未处理异常: $error');
    print('📚 堆栈: $stackTrace');
    // 上报到Sentry等监控平台
  });
  
  print('应用继续运行，不会崩溃');
}

// Flutter完整示例
void setupFlutterErrorHandling() {
  FlutterError.onError = (FlutterErrorDetails details) {
    // 处理Flutter框架捕获的错误
    reportError(details);
  };
  
  runZonedGuarded(
    () => runApp(MyApp()),
    (Object error, StackTrace stack) {
      // 处理所有其他未捕获错误
      reportError(FlutterErrorDetails(
        exception: error,
        stack: stack,
        library: 'global-zone',
      ));
    },
    zoneSpecification: ZoneSpecification(
      print: (Zone self, ZoneDelegate parent, Zone zone, String line) {
        // 拦截所有print输出，用于日志收集
        collectLog(line);
        parent.print(zone, line); // 仍然正常输出
      },
    ),
  );
}
```

## Result模式（函数式错误处理）

### 概念
- **是什么**：`Result`模式是一种替代传统异常抛出的函数式编程模式，通过将返回值包装为成功（`Ok`）或失败（`Error`）类型，强制调用者显式处理错误。
- **为什么需要**：Dart的异常是“非检查异常”（unchecked exceptions），方法签名不声明可能抛出的异常，调用者容易忘记用`try-catch`包裹，导致应用崩溃。
- **解决什么问题**：通过类型系统强制调用者处理所有可能的失败情况，避免因未捕获异常导致的崩溃，并使错误处理流程更显式、更可控。

### 原理
- **底层实现**：`Result`通常定义为`sealed class`，只有两个子类——`Ok<T>`（携带成功值）和`Error<T>`（携带异常对象）。
- **工作流程**：
  1. 函数返回`Result<T>`类型，而不是直接返回`T`或抛出异常。
  2. 函数内部使用`try-catch`捕获所有异常，并包装为`Result.error`返回。
  3. 成功时返回`Result.ok(value)`。
  4. 调用者必须使用`switch`或类型检查解包`Result`，才能获取内部值或错误。
- **关键技术点**：
  - **sealed class**：确保`Result`只能是`Ok`或`Error`，编译器能检查`switch`是否覆盖所有情况。
  - **泛型**：`Result<T>`可以包装任何类型的成功值。
  - **类型安全**：调用者无法绕过错误检查直接获取值。

### 使用方法
- **基本用法**：
  ```dart
  // 定义Result类
  sealed class Result<T> {
    const Result();
    
    factory Result.ok(T value) => Ok(value);
    factory Result.error(Exception error) => Error(error);
  }
  
  final class Ok<T> extends Result<T> {
    final T value;
    const Ok(this.value);
  }
  
  final class Error<T> extends Result<T> {
    final Exception error;
    const Error(this.error);
  }
  
  // 使用Result的函数
  Future<Result<UserProfile>> getUserProfile() async {
    try {
      final response = await http.get(Uri.parse('/user'));
      if (response.statusCode == 200) {
        return Result.ok(UserProfile.fromJson(response.body));
      } else {
        return Result.error(HttpException('Invalid response'));
      }
    } on Exception catch (e) {
      return Result.error(e);
    }
  }
  
  // 调用者必须解包
  void loadUser() async {
    final result = await getUserProfile();
    switch (result) {
      case Ok<UserProfile>():
        print('成功: ${result.value.name}');
      case Error<UserProfile>():
        print('失败: ${result.error}');
    }
  }
  ```
- **常见场景**：
  - Repository层：封装数据源（网络、数据库）的错误。
  - Service层：返回业务操作结果，区分成功和失败。
  - UseCase/Interactor层：组合多个操作，处理复杂的错误恢复逻辑。
- **最佳实践**：
  - 在架构边界使用Result（如Repository接口），使错误处理显式化。
  - 结合`sealed class`和`switch`，确保所有情况都被处理。
  - 对于复杂恢复逻辑，Result比嵌套try-catch更清晰。

### 注意事项
- **常见错误**：在Result中包装`Error`类型（应只包装`Exception`，让`Error`继续传播）。
- **性能问题**：创建Result对象有微小开销，但相比异常抛出和堆栈展开，通常更高效。
- **兼容性**：需要Dart 3.0及以上版本支持`sealed class`。

### 代码示例：复杂错误恢复
```dart
// 使用Result改进控制流
class UserProfileRepository {
  Future<Result<UserProfile>> getUserProfile() async {
    // 先尝试从网络获取
    final apiResult = await _apiClientService.getUserProfile();
    if (apiResult is Ok<UserProfile>) {
      return apiResult; // 网络成功，直接返回
    }
    
    // 网络失败，尝试从数据库获取临时用户
    final dbResult = await _databaseService.createTemporaryUser();
    if (dbResult is Ok<UserProfile>) {
      return dbResult; // 数据库成功，返回临时用户
    }
    
    // 都失败了，返回错误
    return Result.error(Exception('Failed to get user profile'));
  }
}

// ViewModel中使用
class UserProfileViewModel {
  UserProfile? userProfile;
  String? errorMessage;
  
  Future<void> load() async {
    final result = await repository.getUserProfile();
    switch (result) {
      case Ok<UserProfile>():
        userProfile = result.value;
        errorMessage = null;
      case Error<UserProfile>():
        userProfile = null;
        errorMessage = '加载失败: ${result.error}';
        // 可以选择重试或显示默认值
    }
    notifyListeners();
  }
}
```