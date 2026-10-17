## 知识点一：注释

### 概念
- **是什么**：注释是代码中的说明性文本，用于向开发者解释代码的意图、逻辑或注意事项。Dart 编译器会完全忽略注释，不会对程序行为产生任何影响。
- **为什么需要**：提高代码可读性和可维护性，帮助其他开发者（或未来的自己）理解代码的功能和设计决策。
- **解决什么问题**：解决代码"为什么这么做"的问题，补充代码本身无法表达的设计思想、使用限制或待办事项。

### 原理
- **底层实现**：Dart 的词法分析器在扫描源代码时，会将注释视为空白符，不生成任何抽象语法树节点，也不会在编译后的二进制或字节码中留下痕迹。
- **工作流程**：
    1. 开发者编写注释（`//`、`/* */` 或 `///`）。
    2. Dart 解析器在解析时跳过这些注释内容，不影响语法分析。
    3. 对于文档注释（`///` 或 `/**`），`dart doc` 工具会提取这些内容并生成 HTML 文档。
- **关键技术点**：
    - 单行注释：`//` 直到行尾
    - 多行注释：`/* */`，支持嵌套
    - 文档注释：`///`（推荐）或 `/**`，支持 Markdown 语法和 `[标识符]` 链接

### 使用方法
- **基本用法**：
  ```dart
  // 单行注释
  /* 多行
     注释 */
  /// 文档注释，用于生成 API 文档
  void foo() { ... }
  ```
- **常见场景**：
    - 解释复杂算法或业务逻辑
    - 标记待办事项（TODO）或待修复问题
    - 记录方法参数、返回值、异常
    - 生成 API 文档（`dart doc`）
- **最佳实践**：
    - 遵循 [Effective Dart: Documentation](https://dart.dev/guides/language/effective-dart/documentation) 指南
    - 文档注释以简洁的摘要开头，并单独成段
    - 使用 `[ClassName]` 或 `[methodName]` 自动链接到相关 API
    - 对于布尔属性，以"Whether"开头描述

### 注意事项
- **常见错误**：
    - 将注释用于记录版本历史（应该用版本控制系统）
    - 注释与代码实际行为不一致，导致误导
- **性能问题**：注释不影响运行时性能，但过多的文档注释会增加源代码体积（影响传输），通常可忽略
- **兼容性**：所有 Dart 版本均支持

### 代码示例
```dart
/// 表示一种驯养的南美骆驼科动物（美洲驼）。
///
/// 安第斯文化自古以来就将美洲驼作为肉用和驮运动物。
/// 与其他动物一样，美洲驼需要进食，所以别忘了 [feed] 它们一些 [Food]。
class Llama {
  String? name;

  /// 用 [food] 喂你的美洲驼。
  ///
  /// 一只典型的美洲驼每周吃一捆干草。
  void feed(Food food) {
    // 实际喂食逻辑
  }

  // TODO: 添加美洲驼的睡眠行为（2024-01-15）
  void exercise() {
    // ...
  }
}
```


---

## 知识点二：内置注解

### 概念
- **是什么**：内置注解是 Dart 语言预定义的一组注解，用于向编译器或开发工具提供关于代码的额外信息。它们以 `@` 开头。
- **为什么需要**：帮助编译器进行静态检查、标记 API 状态（如弃用）、向工具传递优化提示。
- **解决什么问题**：
    - `@override`：确保子类方法正确重写父类方法，防止拼写错误
    - `@Deprecated`：提示开发者某个 API 已过时，建议迁移
    - `@pragma`：向编译器传递特定平台或优化指令
    - 来自 `meta` 包的注解：提供更丰富的代码约束和意图表达

### 原理
- **底层实现**：内置注解本质上是 Dart SDK 库中定义的类，它们被编译器特殊识别或通过静态分析处理。
- **工作流程**：
    1. 开发者将注解附加到代码元素（类、方法、变量等）
    2. Dart 分析器在静态分析阶段读取这些注解，并根据其语义执行相应检查（如 `@override` 验证）
    3. 对于 `@Deprecated`，分析器会在使用被注解元素时发出警告
    4. 编译器在生成代码时也可能利用 `@pragma` 进行优化
- **关键技术点**：
    - `@override`：仅用于实例方法，不适用于静态方法或顶层函数
    - `@Deprecated`：接受一个字符串参数，解释弃用原因和替代方案
    - `@protected`：成员只能在类及其子类中访问
    - `@mustCallSuper`：重写方法时必须调用父类实现
    - `@immutable`：类的所有字段必须是 final

### 使用方法
- **基本用法**：
  ```dart
  // 1. 四个通用内置注解
  class Parent {
    void greet() => print('Hello');
  }

  class Child extends Parent {
    @override
    void greet() => print('Hi'); // 正确重写
  }

  @Deprecated('Use newMethod instead')
  void oldMethod() {}

  @pragma('vm:entry-point')
  void keepMe() {}
  ```

  ```dart
  // 2. meta 包提供的常用注解（需导入 package:meta/meta.dart）
  import 'package:meta/meta.dart';

  class BaseClass {
    @protected
    void protectedMethod() {}  // 只能被子类访问

    @mustCallSuper
    void performTask() {
      print('Base task');
    }
  }

  class SubClass extends BaseClass {
    @override
    void performTask() {
      super.performTask();  // 必须调用，否则分析器警告
      print('Sub task');
    }
  }

  @immutable
  class ImmutableClass {
    final int value;  // 所有字段必须是 final
    const ImmutableClass(this.value);
  }

  class MyClass {
    @visibleForTesting
    final String debugName = 'test';  // 仅用于测试
  }
  ```
  

- **常见场景**：
    - **`@override`**：任何重写父类方法的地方都应使用
    - **`@Deprecated`**：当 API 需要移除或替换时，先弃用几个版本
    - **`@protected`**：框架设计中需要限制访问权限的成员
    - **`@mustCallSuper`**：`initState`、`dispose` 等生命周期方法
    - **`@immutable`**：Flutter Widget 类设计
    - **`@visibleForTesting`**：为测试暴露内部状态
- **最佳实践**：
    - 始终使用 `@override`，即使 Dart 不强制
    - 弃用 API 时优先使用 `@Deprecated('explanation')`，而不是 `@deprecated`
    - 使用 `meta` 包注解增强代码意图表达
    - `@pragma` 的使用应参考目标平台的文档，避免滥用

### 注意事项
- **常见错误**：
    - 忘记在子类方法上加 `@override`，导致意外地定义了一个新方法而非重写
    - 使用 `@deprecated`（无消息）而不是 `@Deprecated`（有消息）
    - 在非 `package:meta/meta.dart` 导入的情况下使用 `meta` 包注解
- **性能问题**：内置注解仅影响编译时分析，不影响运行时性能
- **兼容性**：
    - 四个通用内置注解在所有 Dart 环境中可用
    - `meta` 包需要额外添加依赖，适用于 Dart 和 Flutter 项目
    - `@required` 已被 Dart 2.12+ 的 `required` 关键字取代

### 代码示例
```dart
import 'package:meta/meta.dart';

// @override 示例
abstract class Animal {
  void speak();
}

class Dog extends Animal {
  @override
  void speak() => print('Woof');
}

// @Deprecated 示例
class OldAPI {
  @Deprecated('Use newFeature() instead')
  void oldFeature() {}
}

// @protected 示例
class Base {
  @protected
  int internalValue = 42;
}

class Derived extends Base {
  void access() {
    print(internalValue); // 可以访问
  }
}

// @mustCallSuper 示例
class Lifecycle {
  @mustCallSuper
  void init() {
    print('Base init');
  }
}

class Component extends Lifecycle {
  @override
  void init() {
    super.init(); // 必须调用
    print('Component init');
  }
}

// @immutable 示例
@immutable
class Point {
  final int x;
  final int y;
  const Point(this.x, this.y);
}

void main() {
  var api = OldAPI();
  api.oldFeature(); // 分析器显示弃用警告
}
```


---

## 知识点三：自定义注解

### 概念
- **是什么**：自定义注解是开发者自己定义的注解类，用于为代码附加自定义元数据。例如 `@Todo('Dash', 'fix this')`。
- **为什么需要**：内置注解无法覆盖所有项目特定需求，自定义注解允许开发者根据业务逻辑定义自己的标记，便于后续工具处理。
- **解决什么问题**：实现领域特定的代码标注，如标记待办项、指定 JSON 序列化规则、声明依赖注入组件等。

### 原理
- **底层实现**：自定义注解本质上是一个普通的 Dart 类，但要求该类必须有一个**常量构造函数（const constructor）**，使得注解可以编译时常量形式存在。
- **工作流程**：
    1. 定义类并使用 `const` 构造函数
    2. 在目标代码元素上使用 `@MyAnnotation(...)`
    3. 在编译时，注解作为元数据附加到元素的 AST 节点上
    4. 通过代码生成器（如 `source_gen`）或运行时反射（`dart:mirrors`）访问这些元数据并执行相应逻辑
- **关键技术点**：
    - 注解类必须提供 `const` 构造函数，以便在编译时求值
    - 注解参数必须是编译时常量（如数字、字符串、布尔值、常量列表等）
    - 多个注解可以附加到同一元素上
    - 注解可以出现在库、类、typedef、类型参数、构造函数、工厂、函数、字段、参数、变量声明之前，以及 import 或 export 指令之前

### 使用方法
- **基本用法**：
  1. 定义注解类：
     ```dart
     class Todo {
       final String who;
       final String what;

       const Todo(this.who, this.what);
     }
     ```
     
  
  2. 使用注解：
     ```dart
     import 'todo.dart';

     @Todo('Dash', 'Implement this function')
     void doSomething() {
       print('Do something');
     }
     ```
     

- **常见场景**：
    - **标记待办或代码审查点**：如 `@Todo`
    - **代码生成触发器**：`json_serializable` 的 `@JsonSerializable()`
    - **依赖注入标识**：`injectable` 的 `@injectable`
    - **路由定义**：`auto_route` 的 `@MaterialAutoRouter`
    - **数据验证**：自定义验证规则注解
- **最佳实践**：
    - 注解类命名应清晰，通常以大写字母开头，使用名词
    - 为注解类添加文档注释，说明其用途和参数含义
    - 保持注解参数简单，避免复杂对象
    - 考虑将注解定义在独立的文件中，便于统一管理

### 注意事项
- **常见错误**：
    - 注解类的构造函数不是 `const`，导致无法作为注解使用
    - 试图在注解中使用非常量值（如 `DateTime.now()`）
    - 忽略注解的包导入路径
- **性能问题**：注解本身对运行时无影响，但如果通过反射访问，会有性能开销；编译时处理（代码生成）则无运行时损耗
- **兼容性**：自定义注解在 Dart 2.x 中完全支持。但依赖反射时需注意 `dart:mirrors` 在 Flutter 中不可用

### 代码示例
```dart
// 1. 定义多个自定义注解
class Immutable {
  final String reason;
  const Immutable(this.reason);
}

class Required {
  const Required();
}

class MinLength {
  final int length;
  const MinLength(this.length);
}

// 2. 使用注解
@Immutable('This class should not be modified')
class User {
  final String name;
  final int age;

  const User(this.name, this.age);
}

class LoginForm {
  @Required()
  String? username;

  @Required()
  @MinLength(6)
  String? password;
}

// 3. 注解也可以用于库级别
@Todo('Team', 'Add more documentation')
library my_library;

// 4. 注解用于导入/导出指令
@Deprecated('Use math.dart instead')
import 'dart:math' hide sqrt;
```


---

## 知识点四：注解的处理机制

### 概念
- **是什么**：注解处理是指读取代码中的注解并基于它们执行某些逻辑（如生成代码、运行时行为改变）的过程。Dart 中主要有两种方式：**编译时处理（代码生成）** 和 **运行时处理（反射）**。
- **为什么需要**：注解本身只是标记，只有通过处理才能发挥实际作用，例如自动生成序列化代码、实现依赖注入等。
- **解决什么问题**：将元编程从手写样板代码中解放出来，通过声明式标记自动生成重复性代码，提高开发效率和代码质量。

### 原理
- **底层实现**：
    - **编译时处理**：利用 `build_runner` 和 `source_gen` 框架，在编译前扫描源代码文件，识别注解，然后生成新的 `.dart` 文件，这些文件随后参与编译。其核心是基于 `Builder` 的构建系统，能够增量构建。
    - **运行时处理**：使用 `dart:mirrors` 库在运行时动态获取类、方法、字段的元数据（包括注解）。反射 API 允许遍历对象图并检查注解信息。
- **工作流程**：
    - **编译时处理**：
        1. 定义注解和对应的 `Generator`（实现 `GeneratorForAnnotation`）
        2. 配置 `build.yaml` 声明生成器
        3. 运行 `dart run build_runner build`
        4. 构建系统解析源文件，为带有注解的元素调用生成器
        5. 生成器输出字符串内容，写入新文件
    - **运行时处理**：
        1. 导入 `dart:mirrors`
        2. 获取目标类的 `ClassMirror`
        3. 遍历元数据列表，检查是否有特定注解
        4. 根据注解信息执行动态行为
- **关键技术点**：
    - `source_gen` 的 `GeneratorForAnnotation<T>` 简化了针对特定注解的生成器编写
    - `build_runner` 提供增量构建、缓存和并发支持
    - 反射中的 `MirrorSystem`、`ClassMirror`、`InstanceMirror` 等 API
    - `ConstantReader` 用于安全读取注解参数值

### 使用方法
- **基本用法（编译时处理）**：
  1. **添加依赖**（`pubspec.yaml`）：
     ```yaml
     dev_dependencies:
       build_runner: ^2.4.0
       source_gen: ^1.5.0
     ```
  
  2. **定义注解**（`lib/annotations.dart`）：
     ```dart
     class ToString {
       const ToString();
     }
     ```
  
  3. **编写生成器**（`lib/src/toString_generator.dart`）：
     ```dart
     import 'package:source_gen/source_gen.dart';
     import 'package:analyzer/dart/element/element.dart';
     import '../annotations.dart';

     class ToStringGenerator extends GeneratorForAnnotation<ToString> {
       @override
       String generateForAnnotatedElement(
           Element element, ConstantReader annotation, BuildStep buildStep) {
         if (element is! ClassElement) {
           throw InvalidGenerationSourceError('@ToString can only be applied to classes');
         }
         
         var className = element.name;
         var fields = element.fields
             .where((f) => !f.isStatic)
             .map((f) => f.name)
             .join(', ');
         
         return '''
         extension ${className}ToString on $className {
           String toString() => "$className{ $fields }";
         }
         ''';
       }
     }
     ```
  
  4. **配置 `build.yaml`**：
     ```yaml
     targets:
       $default:
         builders:
           my_package|toStringBuilder:
             enabled: true

     builders:
       toStringBuilder:
         import: "lib/src/toString_generator.dart"
         builder_factories: ["toStringBuilderFactory"]
         build_extensions: { ".dart": [".g.dart"] }
         auto_apply: dependents
         build_to: source
     ```
  
  5. **运行生成**：
     ```bash
     dart run build_runner build
     ```

- **基本用法（运行时处理）**：
  ```dart
  import 'dart:mirrors';

  class Todo {
    final String who;
    final String what;
    const Todo(this.who, this.what);
  }

  @Todo('Dash', 'check this')
  class MyClass {}

  void main() {
    ClassMirror cm = reflectClass(MyClass);
    for (var meta in cm.metadata) {
      if (meta.reflectee is Todo) {
        var todo = meta.reflectee as Todo;
        print('Todo: ${todo.what} (by ${todo.who})');
      }
    }
  }
  ```
  

- **常见场景**：
    - **编译时处理**：JSON 序列化 (`json_serializable`)、依赖注入 (`injectable`)、路由生成 (`auto_route`)、数据类 (`freezed`)、ORM 实体映射
    - **运行时处理**：极少，仅限服务器端或命令行工具，如一些简单的配置读取框架

- **最佳实践**：
    - 优先选择编译时处理，避免反射的性能开销和平台限制
    - 编写生成器时注意处理错误，提供清晰的日志
    - 使用 `ConstantReader` 安全地读取注解参数
    - 为生成器编写单元测试，确保输出代码正确
    - 生成的文件通常以 `.g.dart` 后缀命名，并添加注释表明是自动生成的

### 注意事项
- **常见错误**：
    - 忘记将生成器添加到 `build.yaml` 导致无输出
    - 在生成器中直接修改源文件（生成器只能输出新文件，不应修改源文件）
    - 在 Flutter 中使用 `dart:mirrors` 导致应用崩溃或体积增大
    - 生成器未正确处理边缘情况（如泛型、私有字段）
- **性能问题**：
    - 编译时处理会增加编译时间，但增量构建可以缓解
    - 反射性能较差，且无法被摇树优化，导致二进制体积膨胀
- **兼容性**：
    - `dart:mirrors` 在 Flutter 和 Dart Web 中不可用，仅支持 VM 环境
    - `build_runner` 广泛适用于 Dart 和 Flutter 项目
    - 代码生成产生的文件应与手写代码分开管理，避免手动修改

### 代码示例
**完整示例：编译时处理自定义 `@ToString` 注解**
```dart
// ---- 文件：lib/annotations.dart ----
class ToString {
  const ToString();
}

// ---- 文件：lib/models/person.dart ----
import '../annotations.dart';

part 'person.g.dart'; // 生成的代码

@ToString()
class Person {
  final String name;
  final int age;
  
  Person(this.name, this.age);
}

// ---- 运行 build_runner 后生成的 lib/models/person.g.dart ----
// GENERATED CODE - DO NOT MODIFY BY HAND

part of 'person.dart';

extension PersonToString on Person {
  String toString() => "Person{ name, age }";
}

// ---- 使用 ----
void main() {
  var p = Person('Alice', 30);
  print(p.toString()); // 输出：Person{ name, age }
}
```

**运行时处理示例（仅限 VM）**：
```dart
import 'dart:mirrors';

// 定义一个用于审计的注解
class Audited {
  const Audited();
}

// 定义一个用于性能计时的注解
class Timed {
  const Timed();
}

@Audited()
@Timed()
class BankTransaction {
  void transfer(double amount) {
    print('Transferring \$$amount');
  }
}

void main() {
  ClassMirror cm = reflectClass(BankTransaction);
  
  // 检查是否有 Audited 注解
  bool isAudited = cm.metadata.any((m) => m.reflectee is Audited);
  print('Is audited: $isAudited');
  
  // 检查是否有 Timed 注解
  bool isTimed = cm.metadata.any((m) => m.reflectee is Timed);
  print('Is timed: $isTimed');
  
  // 获取所有注解信息
  for (var meta in cm.metadata) {
    print('Annotation: ${meta.reflectee.runtimeType}');
  }
}
```


---

## 补充知识点：Dart 注解的高级应用

### @pragma 的深入理解
- **是什么**：`@pragma` 是一个特殊的注解，用于向编译器、运行时或其他工具传递指令。
- **常见用途**：
    - **VM 优化**：`@pragma('vm:entry-point')` 告诉 Dart VM 即使某个函数看起来未被使用，也要保留它（常用于 isolate 入口点）
    - **树摇优化**：`@pragma('vm:prefer-inline')` 建议内联函数
    - **平台特定**：在 Flutter 中用于与原生代码交互
- **示例**：
  ```dart
  // 保留此函数作为 isolate 入口点
  @pragma('vm:entry-point')
  void isolateMain(message) {
    print('Isolate started with message: $message');
  }
  
  // 建议编译器内联此函数
  @pragma('vm:prefer-inline')
  int add(int a, int b) => a + b;
  
  // Flutter FFI 中的使用
  @pragma('vm:entry-point')
  void myNativeFunction() { ... }
  ```
  

### 注解与库的可见性
注解可以应用于库级别的声明，影响整个库的行为：
```dart
@Deprecated('Use new_library.dart instead')
library old_library;

@Todo('Refactor', 'Split into smaller files')
library complex_library;
```


### 常用第三方注解库
| 库名称 | 提供的注解 | 用途 |
|--------|-----------|------|
| `package:meta` | `@required`、`@protected`、`@mustCallSuper`、`@immutable`、`@visibleForTesting`、`@factory`、`@nonVirtual` | 提供通用的代码约束注解 |
| `package:json_serializable` | `@JsonSerializable()`、`@JsonKey()` | JSON 序列化/反序列化 |
| `package:freezed_annotation` | `@freezed`、`@Freezed`、`@Default` | 不可变数据类生成 |
| `package:injectable` | `@injectable`、`@singleton`、`@factoryParam` | 依赖注入配置 |
| `package:riverpod_annotation` | `@riverpod` | Riverpod 状态管理代码生成 |

---

## 总结

| 概念 | 核心知识点 | 关键语法与工具 |
| :--- | :--- | :--- |
| **注释** | 为开发者提供代码说明，编译器忽略 | `//` (单行), `/* */` (多行), `///` (文档注释)  |
| **文档注释** | 用于生成 API 文档，`dart doc` 工具解析 | 使用 `///`，遵循 Effective Dart，用 `[]` 引用标识符  |
| **内置注解** | Dart 语言预定义的元数据 | `@override`, `@Deprecated`, `@deprecated`, `@pragma`  |
| **meta 包注解** | 增强代码约束和意图表达 | `@protected`, `@mustCallSuper`, `@immutable`, `@visibleForTesting` 等  |
| **自定义注解** | 允许开发者定义自己的元数据 | 定义一个带有 `const` 构造函数的类  |
| **注解处理** | 让注解发挥实际作用的方式 | **编译时处理** (`build_runner` + `source_gen`)；**运行时处理** (`dart:mirrors`，慎用) |
| **高级应用** | 平台特定优化和第三方库集成 | `@pragma` 优化指令 、JSON 序列化、依赖注入、状态管理等 |
