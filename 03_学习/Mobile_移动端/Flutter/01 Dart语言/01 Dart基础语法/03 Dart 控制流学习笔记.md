## 1. if 语句与 if-case

### 概念
- **是什么**  
  `if` 语句是一种条件分支结构，根据布尔表达式的结果决定执行哪一段代码。`if-case` 是 Dart 3.0 引入的增强，允许在 `if` 后使用模式匹配来解构并检查值。
- **为什么需要**  
  程序经常需要根据不同条件执行不同逻辑，`if` 提供了最基本的条件控制。`if-case` 则简化了类型检查和解构的代码，使逻辑更清晰。
- **解决什么问题**  
  - 根据布尔条件选择执行路径。  
  - 结合模式匹配，避免显式类型转换和嵌套判断，提高代码可读性。

### 原理
- **底层实现**  
  `if` 语句编译为分支跳转指令，条件表达式必须返回 `bool` 类型。`if-case` 在编译时生成模式匹配代码，若匹配成功则将解构的变量引入作用域并执行分支。
- **工作流程**  
  1. 计算 `if` 后括号内的表达式。  
  2. 若结果为 `true`，执行 `if` 分支；否则检查 `else if` 或进入 `else` 分支。  
  3. 对于 `if-case`，先尝试将值与模式匹配，若成功则执行分支，否则继续后续分支。
- **关键技术点**  
  - 条件必须是布尔表达式（不能是其他类型）。  
  - 模式匹配支持解构、类型检查等。

### 使用方法
- **基本用法**
  ```dart
  if (isRaining()) {
    bringRainCoat();
  } else if (isSnowing()) {
    wearJacket();
  } else {
    putTopDown();
  }
  ```
- **if-case 用法**
  ```dart
  if (pair case [int x, int y]) {
    print('坐标: $x, $y');
  } else {
    throw FormatException('无效坐标');
  }
  ```
- **常见场景**  
  - 简单的条件判断。  
  - 结合模式匹配处理特定类型的数据（如列表、记录）。
- **最佳实践**  
  - 保持条件简洁，避免过长的布尔表达式。  
  - 若需要多个模式匹配，考虑使用 `switch` 而非多个 `else if`。

### 注意事项
- **常见错误**  
  - 条件中误用赋值运算符 `=` 而非比较 `==`。  
  - 忘记处理所有可能分支导致逻辑遗漏。
- **性能问题**  
  无显著性能问题，但过多嵌套 `if` 可能影响可读性。
- **兼容性**  
  `if-case` 需要 Dart 3.0 或更高版本。

### 代码示例
```dart
void main() {
  // 基本 if
  int score = 85;
  if (score >= 90) {
    print('优秀');
  } else if (score >= 60) {
    print('及格');
  } else {
    print('不及格');
  }

  // if-case
  Object obj = (1, 2);
  if (obj case (int a, int b)) {
    print('记录: ($a, $b)');
  }
}
```

---

## 2. switch 语句与 switch 表达式

### 概念
- **是什么**  
  `switch` 语句允许一个值与多个模式（case）进行匹配，执行匹配到的分支。`switch` 表达式是 Dart 3.0 引入的，它直接返回一个值，更加简洁。
- **为什么需要**  
  当有多个分支条件时，`if-else` 链显得冗长且容易出错。`switch` 提供更清晰的多路分支控制，且编译器可进行穷尽性检查。
- **解决什么问题**  
  - 替代多个 `else if`，提高代码可读性。  
  - 结合模式匹配和解构，处理复杂数据结构。  
  - 使用穷尽性检查确保所有情况都被覆盖（尤其对枚举和密封类）。

### 原理
- **底层实现**  
  `switch` 语句编译为跳转表或二分查找等高效跳转机制（取决于情况）。`switch` 表达式则作为一个表达式求值，其结果为匹配分支的表达式值。
- **工作流程**  
  1. 计算待匹配的值。  
  2. 依次与各个 case 的模式匹配（可带守卫 `when`）。  
  3. 若匹配成功，执行对应分支（语句或表达式）。  
  4. 在 `switch` 语句中，非空 case 执行后自动跳出（无 fallthrough），除非使用 `continue` 标签。  
  5. 在 `switch` 表达式中，必须有一个分支匹配，否则编译错误（若未穷尽）。
- **关键技术点**  
  - 模式匹配（逻辑或、关系、类型等）。  
  - 守卫子句 `when`。  
  - 穷尽性检查（exhaustiveness checking）。  
  - 标签与 `continue` 实现 fallthrough。

### 使用方法
- **switch 语句**
  ```dart
  var command = 'OPEN';
  switch (command) {
    case 'CLOSED':
      executeClosed();
    case 'PENDING':
      executePending();
    case 'APPROVED':
    case 'DENIED':
      executeApprovedOrDenied(); // 共享体（空 case fallthrough）
    case 'OPEN':
      executeOpen();
      continue alsoOpen; // 显式 fallthrough
    alsoOpen:
    case 'REOPEN':
      executeAlsoOpen();
    default:
      executeUnknown();
  }
  ```
- **switch 表达式**
  ```dart
  token = switch (charCode) {
    slash || star || plus || minus => operator(charCode),
    comma || semicolon => punctuation(charCode),
    >= digit0 && <= digit9 => number(),
    _ => throw FormatException('Invalid'),
  };
  ```
- **常见场景**  
  - 状态机、命令解析。  
  - 基于枚举类型的分支。  
  - 对密封类（sealed class）子类型进行穷尽处理。
- **最佳实践**  
  - 对于简单的值映射，优先使用 `switch` 表达式。  
  - 使用 `_` 作为默认分支，避免遗漏。  
  - 结合密封类实现类似代数数据类型的模式匹配。

### 注意事项
- **常见错误**  
  - 忘记处理所有可能的值导致非穷尽 `switch`（编译错误或运行时异常）。  
  - 在 `switch` 表达式中误用 `:` 而不是 `=>`。  
  - 在 `switch` 语句中忘记 `break` 导致意外 fallthrough（但 Dart 非空 case 自动 break，需注意空 case）。
- **性能问题**  
  通常高效，但大量复杂模式可能影响编译优化。
- **兼容性**  
  `switch` 表达式、模式、守卫需要 Dart 3.0。

### 代码示例
```dart
enum Color { red, green, blue }

String describeColor(Color c) {
  return switch (c) {
    Color.red => '红色',
    Color.green => '绿色',
    Color.blue => '蓝色',
  }; // 穷尽，无需 default
}

void main() {
  print(describeColor(Color.red));
}
```

---

## 3. 循环：for、for-in、forEach、while、do-while

### 概念
- **是什么**  
  循环结构允许重复执行一段代码，直到特定条件改变。Dart 提供多种循环：`for`（传统计数循环）、`for-in`（遍历可迭代对象）、`forEach`（方法）、`while`（先判断后执行）、`do-while`（先执行后判断）。
- **为什么需要**  
  处理重复任务，如遍历集合、执行固定次数的操作。
- **解决什么问题**  
  - 根据索引遍历。  
  - 无需索引遍历集合。  
  - 条件未知但需要反复执行直到满足条件。

### 原理
- **底层实现**  
  循环编译为跳转指令，`for` 和 `while` 类似，`do-while` 保证至少执行一次。`for-in` 使用迭代器（`Iterator`）遍历。
- **工作流程**  
  - `for`：初始化 -> 条件判断 -> 执行体 -> 迭代语句 -> 重复。  
  - `for-in`：获取迭代器，循环调用 `moveNext()`，取值执行体。  
  - `while`：先判断条件，为 true 则执行体，再判断。  
  - `do-while`：先执行体，再判断条件，为 true 则继续。
- **关键技术点**  
  - 闭包捕获索引：Dart 的 `for` 循环中，每次迭代创建的闭包捕获的是当前索引的副本，避免了 JavaScript 的常见陷阱。  
  - `forEach` 是 Iterable 的扩展方法，用于副作用。

### 使用方法
- **基本用法**
  ```dart
  // for
  for (var i = 0; i < 5; i++) {
    print(i);
  }

  // for-in
  var list = [1, 2, 3];
  for (var item in list) {
    print(item);
  }

  // forEach
  list.forEach(print);

  // while
  while (isNotDone()) {
    doSomething();
  }

  // do-while
  do {
    printLine();
  } while (!atEndOfPage());
  ```
- **常见场景**  
  - 遍历数组、列表、集合。  
  - 重复操作直到条件满足（如读取文件直到末尾）。  
  - 使用索引修改集合。
- **最佳实践**  
  - 优先使用 `for-in` 遍历集合，除非需要索引。  
  - 需要索引时用传统 `for` 循环。  
  - 避免在循环中修改正在遍历的集合（可能导致异常）。

### 注意事项
- **常见错误**  
  - 无限循环：忘记更新循环变量或条件永远为真。  
  - 在 `for-in` 中修改集合（如添加或删除元素）可能引发 `ConcurrentModificationError`。  
  - 误用 `forEach` 进行非副作用操作（`forEach` 返回 void，不能用于变换）。
- **性能问题**  
  通常无显著差异，但大量循环内创建闭包可能增加内存开销。
- **兼容性**  
  所有版本均支持。

### 代码示例
```dart
void main() {
  // 闭包捕获索引演示
  var callbacks = [];
  for (var i = 0; i < 2; i++) {
    callbacks.add(() => print(i));
  }
  for (var cb in callbacks) {
    cb(); // 输出 0, 1 （与 JavaScript 不同）
  }

  // 模式匹配 in for-in
  var candidates = [Candidate('Alice', 5), Candidate('Bob', 3)];
  for (var Candidate(:name, :yearsExperience) in candidates) {
    print('$name 经验 $yearsExperience 年');
  }
}

class Candidate {
  final String name;
  final int yearsExperience;
  Candidate(this.name, this.yearsExperience);
}
```

---

## 4. break、continue 与标签

### 概念
- **是什么**  
  `break` 用于退出当前循环或 `switch` 语句；`continue` 用于跳过当前迭代剩余部分，进入下一次迭代。标签（label）可为语句命名，使 `break` 或 `continue` 可以指定目标。
- **为什么需要**  
  提供更精细的循环控制，例如提前终止循环、跳过某些迭代，或在嵌套循环中跳出外层循环。
- **解决什么问题**  
  - 提前退出循环（如找到目标后停止）。  
  - 跳过不符合条件的迭代。  
  - 从多层嵌套中直接跳出外层，避免使用标志变量。

### 原理
- **底层实现**  
  `break` 和 `continue` 对应汇编中的跳转指令。标签在编译时被标记为地址，`break label` 跳转到标签后的第一条指令。
- **工作流程**  
  - `break`：立即终止当前最内层循环或 `switch`。  
  - `continue`：跳过当前迭代中后续代码，回到循环条件判断（或迭代语句）。  
  - 带标签的 `break`/`continue`：跳转到指定标签的循环或语句之后/下一次迭代。
- **关键技术点**  
  标签作用域：标签只能用于直接包含它的语句。

### 使用方法
- **基本用法**
  ```dart
  // break 退出循环
  while (true) {
    if (shutDownRequested()) break;
    processRequests();
  }

  // continue 跳过本次迭代
  for (int i = 0; i < 10; i++) {
    if (i % 2 == 0) continue;
    print(i); // 打印奇数
  }
  ```
- **带标签的 break/continue**
  ```dart
  outerLoop:
  for (var i = 1; i <= 3; i++) {
    for (var j = 1; j <= 3; j++) {
      if (i == 2 && j == 2) {
        break outerLoop; // 跳出外层循环
      }
      print('i=$i, j=$j');
    }
  }
  ```
- **常见场景**  
  - 搜索算法中提前退出。  
  - 过滤循环中的元素。  
  - 处理多层嵌套循环时跳出外层。
- **最佳实践**  
  - 谨慎使用标签，避免过度复杂化。  
  - 优先考虑将循环逻辑提取为函数，用 `return` 替代复杂 `break`。

### 注意事项
- **常见错误**  
  - 忘记标签作用于哪个语句，导致意外跳转。  
  - 在 `switch` 外使用 `break` 或 `continue` 导致编译错误。  
  - 无限循环中未正确使用 `break`。
- **性能问题**  
  无显著影响。
- **兼容性**  
  全版本支持。

### 代码示例
```dart
void main() {
  // 使用 continue 跳过特定值
  for (int i = 0; i < 5; i++) {
    if (i == 3) continue;
    print('i = $i'); // 0,1,2,4
  }

  // 使用标签 break 从嵌套循环中跳出
  outer:
  for (int i = 0; i < 3; i++) {
    for (int j = 0; j < 3; j++) {
      if (i == 1 && j == 1) break outer;
      print('($i, $j)');
    }
  }
  print('循环结束');
}
```

---

## 5. 异常处理：throw、catch、finally、rethrow

### 概念
- **是什么**  
  异常是程序运行时发生的错误或意外情况。Dart 提供 `throw` 抛出异常，`try-catch` 捕获并处理异常，`finally` 执行清理代码，`rethrow` 部分处理后重新抛出。
- **为什么需要**  
  使程序能从错误中恢复，或至少优雅地终止，并提供错误信息。异常分离了错误处理代码和正常逻辑。
- **解决什么问题**  
  - 报告不可恢复的错误。  
  - 捕获并处理特定类型的错误。  
  - 确保资源释放（如关闭文件）即使发生异常。

### 原理
- **底层实现**  
  异常处理基于堆栈展开（stack unwinding），当异常抛出时，运行时寻找匹配的 `catch` 子句，并执行 `finally` 块。
- **工作流程**  
  1. 使用 `throw` 抛出任何非空对象（通常为 `Exception` 或 `Error` 子类）。  
  2. 在 `try` 块中执行可能抛出异常的代码。  
  3. 若抛出异常，按顺序匹配 `catch` 子句（`on` 指定类型，`catch` 捕获对象）。  
  4. 若匹配，执行处理代码；可选择性 `rethrow` 继续传播。  
  5. 无论是否捕获，`finally` 块最终都会执行。
- **关键技术点**  
  - `on` 用于指定异常类型。  
  - `catch` 可带两个参数：异常对象和堆栈跟踪。  
  - `rethrow` 保留原始堆栈信息。

### 使用方法
- **基本用法**
  ```dart
  try {
    breedMoreLlamas();
  } on OutOfLlamasException {
    buyMoreLlamas();
  } on Exception catch (e) {
    print('异常: $e');
  } catch (e, s) {
    print('未知错误: $e');
    print('堆栈: $s');
  } finally {
    cleanLlamaStalls();
  }
  ```
- **rethrow 示例**
  ```dart
  void misbehave() {
    try {
      dynamic foo = true;
      print(foo++); // 运行时错误
    } catch (e) {
      print('部分处理: $e');
      rethrow; // 重新抛出，让上层处理
    }
  }
  ```
- **常见场景**  
  - 文件读写、网络请求等可能失败的操作。  
  - 输入验证失败时抛出格式异常。  
  - 资源清理（无论成功与否）。
- **最佳实践**  
  - 只捕获你能处理的异常，否则让上层处理。  
  - 使用具体的异常类型，而非捕获所有异常。  
  - 使用 `rethrow` 保留堆栈信息，而不是重新 `throw` 原异常。

### 注意事项
- **常见错误**  
  - 捕获异常后忘记处理，导致错误被隐藏。  
  - 过度使用异常控制正常流程（异常开销较大）。  
  - 在 `finally` 中抛出异常会覆盖之前的异常。
- **性能问题**  
  抛出异常比正常返回慢，不应用于常规控制流。
- **兼容性**  
  全版本支持。

### 代码示例
```dart
void main() {
  try {
    int result = divide(10, 0);
    print(result);
  } on IntegerDivisionByZeroException {
    print('不能除以零');
  } finally {
    print('计算完成');
  }
}

int divide(int a, int b) {
  if (b == 0) {
    throw IntegerDivisionByZeroException();
  }
  return a ~/ b;
}
```

---

## 6. assert 断言

### 概念
- **是什么**  
  `assert` 是一个调试工具，用于在开发期间检查条件是否为真。如果条件为假，程序会抛出 `AssertionError` 并终止（仅在调试或启用断言的环境）。
- **为什么需要**  
  帮助开发者尽早发现逻辑错误，验证不变量，确保程序状态符合预期。
- **解决什么问题**  
  - 验证函数参数合法性。  
  - 检查程序中间状态。  
  - 提供一种比注释更可靠的文档方式。

### 原理
- **底层实现**  
  `assert` 在编译为生产代码时通常被移除（除非显式启用）。在开发模式或带 `--enable-asserts` 运行时，`assert` 会检查条件，若为 false 则抛出 `AssertionError`。
- **工作流程**  
  1. 执行 `assert(condition, optionalMessage)`。  
  2. 如果断言被禁用（生产环境），忽略条件，程序继续。  
  3. 如果断言启用，计算条件，若为 false，构造 `AssertionError` 并抛出。
- **关键技术点**  
  断言可带可选消息；条件可以是任何布尔表达式，但在生产环境可能不会被执行（避免副作用）。

### 使用方法
- **基本用法**
  ```dart
  assert(text != null); // 无消息
  assert(number < 100, '数值必须小于100');
  assert(urlString.startsWith('https'), 'URL 必须是 HTTPS');
  ```
- **常见场景**  
  - 检查函数前置条件（参数非空、范围等）。  
  - 验证循环不变量。  
  - 确保某个分支不会到达（如 `assert(false, '不该执行到这里')`）。
- **最佳实践**  
  - 使用断言检查内部错误，而不是用户输入验证（用户输入应使用异常）。  
  - 断言条件不应有副作用，因为生产环境可能不会执行。  
  - 提供清晰的消息，便于调试。

### 注意事项
- **常见错误**  
  - 在断言条件中放入有副作用的代码（如变量修改），导致生产环境行为不同。  
  - 依赖断言进行正常流程控制（断言可能被禁用）。
- **性能问题**  
  断言在开发模式下有轻微开销，生产环境无影响。
- **兼容性**  
  全版本支持，但启用方式依赖工具（Flutter 调试模式默认启用，`dart run` 需 `--enable-asserts`）。

### 代码示例
```dart
int fib(int n) {
  assert(n >= 0, 'n 必须为非负数');
  if (n <= 1) return n;
  return fib(n - 1) + fib(n - 2);
}

void main() {
  print(fib(10)); // 正常
  print(fib(-1)); // 在启用断言时抛出 AssertionError
}
```

---

## 7. 模式匹配高级用法（补充）

### 7.1 守卫子句 `when`
- **是什么**  
  在 `switch` case 或 `if-case` 中，可以在模式后添加 `when` 子句，附加一个布尔条件，只有同时满足模式和 `when` 条件时才算匹配成功。
- **为什么需要**  
  当模式本身不足以精确匹配（例如需要检查模式中变量的关系或外部条件）时，`when` 提供了细粒度控制。
- **示例**
  ```dart
  void describePoint((int, int) point) {
    switch (point) {
      case (0, 0):
        print('原点');
      case (var x, var y) when x == y:
        print('在对角线上: ($x, $y)');
      case (var x, var y) when x.abs() == y.abs():
        print('在 y = ±x 线上');
      default:
        print('普通点: $point');
    }
  }
  ```
- **注意事项**  
  `when` 子句中的表达式可以访问模式中声明的变量，但不能修改它们。

### 7.2 逻辑或模式 `||`
- **是什么**  
  在 `switch` 的 case 中，可以使用 `||` 将多个模式组合，只要其中一个匹配即可。
- **为什么需要**  
  多个模式共享同一分支时，避免重复代码。
- **示例**
  ```dart
  String classifyNumber(int? n) {
    return switch (n) {
      0 => '零',
      1 || 2 || 3 => '小',
      4 || 5 || 6 => '中',
      >=7 && <=9 => '大',
      _ => '未知',
    };
  }
  ```
- **注意事项**  
  逻辑或模式中的每个模式必须独立且不能相互干扰，变量只能在所有分支中都存在且类型一致时才能绑定。

### 7.3 嵌套模式与解构
- **是什么**  
  模式可以嵌套，例如在列表中包含子列表、记录等，进一步解构内部元素。
- **示例**
  ```dart
  if (nestedObj case [String name, (int x, int y)]) {
    print('$name 位于 ($x, $y)');
  }

  // 与 for-in 结合
  var points = [Point(1, 2), Point(3, 4)];
  for (var Point(x: x, y: y) in points) {
    print('($x, $y)');
  }
  ```

---

## 8. 循环嵌套与练习建议

### 8.1 循环嵌套的陷阱
- **变量共享问题**  
  在 Dart 的传统 `for` 循环中，每次迭代都会创建一个新的循环变量绑定，因此闭包捕获的是当前迭代的值（与 JavaScript 不同）。但在嵌套循环中，如果使用相同的变量名，仍可能混淆。
- **性能考虑**  
  深度嵌套循环可能导致时间复杂度升高（如 O(n²)），应评估是否可优化。

### 8.2 练习示例：打印乘法表
```dart
void main() {
  for (int i = 1; i <= 9; i++) {
    String row = '';
    for (int j = 1; j <= i; j++) {
      row += '$j×$i=${i * j}\t';
    }
    print(row);
  }
}
```

### 8.3 使用标签控制多重循环
```dart
// 查找第一个和为 0 的数对
outer:
for (var a in listA) {
  for (var b in listB) {
    if (a + b == 0) {
      print('找到: $a + $b = 0');
      break outer;
    }
  }
}
```

---

## 9. 异常处理补充

### 9.1 自定义异常类
- **最佳实践**  
  实现 `Exception` 接口或继承 `Exception` 类（或 `Error`），以提供更具体的错误信息。
  ```dart
  class ValidationException implements Exception {
    final String message;
    ValidationException(this.message);
    @override
    String toString() => '验证失败: $message';
  }

  void validateAge(int age) {
    if (age < 0) throw ValidationException('年龄不能为负数');
  }
  ```

### 9.2 `finally` 执行顺序
- **关键点**  
  `finally` 总是在 `try` 块退出后执行，即使 `catch` 中 `rethrow` 或 `return`，`finally` 依然执行。若 `finally` 中 `return` 或 `throw`，会覆盖之前的异常或返回值。
  ```dart
  int test() {
    try {
      throw '错误';
    } catch (e) {
      print('捕获');
      return 1;    // 返回值会被 finally 覆盖？
    } finally {
      print('清理');
      // 如果这里 return 2，则函数返回 2
    }
  }
  ```

### 9.3 链式异常与堆栈信息
- **使用 `rethrow`**  
  保留原始堆栈信息，便于调试。
  ```dart
  void outer() {
    try {
      inner();
    } catch (e, s) {
      print('外层捕获: $e');
      print('堆栈: $s');
    }
  }

  void inner() {
    try {
      throw '内部错误';
    } catch (e) {
      print('内层部分处理');
      rethrow; // 重新抛出，保留原始堆栈
    }
  }
  ```

---

## 10. 断言（Assert）深入

### 10.1 断言在生产环境的禁用
- **默认行为**  
  在 Dart 的生产模式（如 `dart run --release` 或 Flutter 的 release build）下，断言被完全忽略，条件表达式甚至不会被执行。因此**绝不能**在断言中放入具有副作用的代码，否则生产环境行为与开发环境不一致。

### 10.2 启用断言的方式
- **命令行**：`dart run --enable-asserts <file.dart>`
- **Flutter 调试模式**：默认启用
- **单元测试**：通常启用断言，便于测试内部状态。

### 10.3 断言与单元测试的区别
- 断言用于检查程序内部不变量，失败代表程序有 bug。
- 单元测试使用 `expect` 等函数验证外部行为，失败代表测试不通过。

---

## 11. 综合示例：结合多种控制流

以下示例展示了一个简单的文本解析器，使用了 `if-case`、`switch` 表达式、循环、异常处理和断言：
```dart
import 'dart:io';

void main() {
  var lines = File('data.txt').readAsLinesSync();
  for (var line in lines) {
    try {
      processLine(line);
    } on FormatException catch (e) {
      stderr.writeln('格式错误: $e');
    } catch (e) {
      stderr.writeln('未知错误: $e');
      rethrow;
    } finally {
      print('--- 处理完成 ---');
    }
  }
}

void processLine(String line) {
  // 使用 if-case 解构
  if (line case [var first, var second] when first == second) {
    print('重复字符: $first');
  } else {
    // 使用 switch 表达式进行分类
    var result = switch (line) {
      '' => '空行',
      String s when s.length < 5 => '短行',
      String s when s.contains(RegExp(r'\d+')) => '包含数字',
      _ => '普通行',
    };
    print('结果: $result');
  }

  // 断言：行不能包含制表符
  assert(!line.contains('\t'), '行中包含制表符: $line');
}
```

