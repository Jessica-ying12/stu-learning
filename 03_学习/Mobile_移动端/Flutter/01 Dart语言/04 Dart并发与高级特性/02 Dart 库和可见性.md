## 知识点一：库的定义与代码组织（`library`, `part`）

### 概念
- **是什么**：在Dart中，**每一个Dart文件（`.dart`）本身就是一个库**，即使没有显式声明 。库不仅提供了一组API，还是一个独立的隐私单元 。
- **为什么需要**：随着项目规模扩大，将代码分散到多个文件中可以提高可读性、可维护性，并促进代码复用。
- **解决什么问题**：
    - **模块化**：按功能划分代码。
    - **封装**：定义公共API与内部实现的边界。
    - **拆分大文件**：允许将一个逻辑复杂的大库拆分成多个物理文件进行管理 。

### 原理
- **底层实现**：Dart编译器将每个库视为一个独立的命名空间。库内通过`part`指令关联的所有文件共享同一个命名空间和隐私边界。
- **工作流程**：
    1.  **主文件**使用`library`指令（可选）声明库名，并使用`part`指令包含其他文件。
    2.  **部分文件**使用`part of`指令指明其归属。
    3.  编译时，这些文件被合并为一个逻辑单元进行处理。
- **关键技术点**：`part`机制允许拆分出的文件访问主文件中的所有成员（包括私有成员），因为它们最终属于同一个库 。

### 使用方法
- **基本用法**：
    1.  **声明库**（可选，通常用于添加文档或元数据）：
        ```dart
        /// 数学运算库
        library math_operations;
        ```
    2.  **使用 `part` 拆分**：
        主文件 `math_operations.dart`：
        ```dart
        part 'src/add.dart';
        part 'src/subtract.dart';

        int multiply(int a, int b) => a * b;
        ```
        部分文件 `src/add.dart`：
        ```dart
        part of '../math_operations.dart';

        int add(int a, int b) => a + b; // 可以访问主文件中的私有成员
        ```
- **常见场景**：
    - 将极其庞大且内聚的类或功能集拆分到不同文件，但它们仍需共享私有状态。
    - 用于代码生成场景，生成的文件作为主库的一部分 。
- **最佳实践**：
    - **谨慎使用**：官方和社区普遍建议**尽量避免使用 `part`**，因为它破坏了文件的独立性，使代码依赖关系变得隐晦，难以理解和维护 。优先考虑使用`import`/`export`来组织多文件库。

### 注意事项
- **常见错误**：在部分文件中忘记添加`part of`指令。
- **性能问题**：无直接影响。
- **兼容性**：在所有Dart平台上都受支持。

## 知识点二：导入其他库（`import`, `as`, `show`, `hide`）

### 概念
- **是什么**：`import`指令用于将一个库的命名空间引入到当前库的作用域中，以便使用其公开的API 。
- **为什么需要**：复用标准库、第三方包或项目其他部分的代码。
- **解决什么问题**：
    - 代码复用。
    - 通过`as`、`show`、`hide`精细控制导入的内容，解决命名冲突，减少命名空间污染 。

### 原理
- **工作流程**：
    1.  解析`import`指令后的URI（统一资源标识符）。
    2.  根据`show`/`hide`过滤要导入的成员。
    3.  若指定了`as`前缀，则将导入的成员放入指定前缀的命名空间下，避免冲突 。

### 使用方法
- **基本用法与URI方案**：
    1.  **导入内置库**：使用 `dart:` 方案。
        ```dart
        import 'dart:math';
        import 'dart:async';
        ```
    2.  **导入第三方包**：使用 `package:` 方案 。
        ```dart
        import 'package:http/http.dart' as http;
        ```
    3.  **导入本地文件**：使用相对路径 。
        ```dart
        import '../models/user.dart';
        import 'src/utils.dart';
        ```

- **解决命名冲突 (`as`)**：
    ```dart
    import 'package:lib1/lib1.dart';
    import 'package:lib2/lib2.dart' as lib2; // 为 lib2 指定前缀

    void main() {
      Element element1 = Element(); // 来自 lib1
      lib2.Element element2 = lib2.Element(); // 来自 lib2
    }
    ```

- **选择性导入 (`show`/`hide`)** ：
    ```dart
    // 只导入 foo 和 bar
    import 'package:lib1/lib1.dart' show foo, bar;

    // 导入除 foo 之外的所有内容
    import 'package:lib2/lib2.dart' hide foo;
    ```

- **延迟加载 (`deferred as`)** ：
    - **场景**：减少APP的启动时间，加载不常用的功能。
    - **用法**：
        ```dart
        import 'package:greetings/hello.dart' deferred as hello;

        Future<void> greet() async {
          await hello.loadLibrary(); // 加载库，返回 Future
          hello.printGreeting();
        }
        ```
    - **重要限制**：
        - 延迟加载库的常量在加载完成前不可用。
        - 不能在当前文件使用延迟库中定义的类型。
        - `loadLibrary()` 可以被多次调用，但库只会加载一次 。
        - **目前主要用于Web平台** 。

### 注意事项
- **常见错误**：
    - 混用相对路径和`package:`路径导入**同一个物理文件**，Dart会将其视为两个不同的库，导致类型不匹配错误。
    - 在使用延迟加载库的成员前，忘记调用`loadLibrary()`。
- **兼容性**：`deferred as` 在Dart VM上的行为与Web有所不同，官方警告不要依赖VM的当前行为 。

## 知识点三：重新导出库（`export`）

### 概念
- **是什么**：`export`指令允许一个库将其导入的其他库的API作为自己的API对外公开 。
- **为什么需要**：当创建一个由多个文件组成的大型库时，希望为用户提供一个**单一、统一的入口文件**，而不必让他们分别导入每个内部文件。
- **解决什么问题**：简化库的使用接口，隐藏内部实现结构，即实现“外观模式”。

### 原理
- **工作流程**：当一个库使用`export`导出另一个库时，Dart编译器会将导出库的公开成员合并到当前库的命名空间中。用户只需导入当前库，即可使用所有被导出的API。

### 使用方法
- **基本用法**：
    库的主入口文件 `my_library.dart`：
    ```dart
    library my_library;

    // 公开内部模块的 API
    export 'src/models.dart' show User, Product; // 选择性导出
    export 'src/api.dart';                       // 全部导出

    // 也可以有自己的代码
    String getVersion() => '1.0.0';
    ```
    用户使用：
    ```dart
    import 'package:my_library/my_library.dart'; // 一个import就够了

    void main() {
      var user = User(); // 来自被导出的 models.dart
      getVersion();      // 来自 my_library.dart 自身
    }
    ```
- **常见场景**：创建**Barrel File（桶文件）**，这是组织和封装包的推荐方式 。
- **最佳实践**：
    - **永远不要导出 `lib/src` 目录下的文件**。`lib/src` 是存放私有实现的地方，应被视为内部细节。包的公开API应通过`lib/`下的主文件（通常是`lib/<package_name>.dart`）进行导出 。
    - 使用`show`/`hide`精确控制导出的API，避免将内部辅助函数或类意外暴露给用户。

## 知识点四：内置核心库

### 概念
- **是什么**：Dart SDK自带了一套丰富的核心库，为日常编程任务提供基础API 。
- **为什么需要**：避免重复造轮子，提供经过优化和测试的跨平台基础功能。

### 原理
- **底层实现**：这些库部分用Dart实现，部分由虚拟机或JavaScript运行时提供底层支持，确保了在不同平台上的高效运行。

### 核心库详解 
| 库 | 描述 | 使用场景 | 平台 |
| :--- | :--- | :--- | :--- |
| **dart:core** | **自动导入**到每个Dart程序。包含`String`、`num`、`bool`、`List`、`Map`等核心类型。 | 任何程序的基础。 | 全平台 |
| **dart:async** | 提供`Future`、`Stream`等用于异步编程的核心类。 | 处理异步操作，如IO、定时器、事件。 | 全平台 |
| **dart:math** | 提供数学常数（如`pi`）、函数（如`sin`、`sqrt`）和**随机数生成器** `Random`。 | 科学计算、游戏、生成随机数据。 | 全平台 |
| **dart:convert** | 提供JSON、UTF-8等数据格式的编码器和解码器。 | 处理网络请求响应、数据存储。 | 全平台 |
| **dart:collection** | 补充`dart:core`的集合类，如`Queue`、`LinkedList`、`HashMap`。 | 需要使用特定数据结构优化算法时。 | 全平台 |
| **dart:io** | 为基于Dart VM的应用提供文件、Socket、HTTP服务器等I/O功能。 | **命令行工具、服务器端应用、Flutter应用（需注意）**。 | 原生平台 (VM) |
| **dart:isolate** | 提供基于Isolate的并发编程能力。 | 执行计算密集型任务，避免阻塞主Isolate。 | 原生平台 (VM) |
| **dart:js_interop** | 用于与JavaScript和浏览器API进行互操作的类型安全库。 | **Web平台**开发，取代旧版`dart:html`和`package:js`。 | Web平台 |

### 注意事项
- **平台兼容性**：并非所有库都适用于所有平台。例如，`dart:io`不能在Web平台使用；`dart:html`等旧版Web库已被`package:web`和`dart:js_interop`取代 。
- **导入**：除了`dart:core`，使用其他核心库都需要显式导入 。

## 知识点五：第三方包管理（`pub`、`pubspec.yaml`）

### 概念
- **是什么**：Pub是Dart和Flutter的官方包管理器。`pubspec.yaml`是项目的配置文件，用于声明项目的元数据、依赖项和版本约束 。
- **为什么需要**：管理和复用社区开发的代码库，解决依赖地狱，保证构建的可重现性。

### 原理
- **工作流程** ：
    1.  开发者在`pubspec.yaml`中声明依赖。
    2.  运行 `dart pub get`。
    3.  Pub解析依赖图，根据版本约束下载最佳匹配的包到本地缓存。
    4.  生成 `pubspec.lock` 文件（锁定具体版本）和 `.dart_tool/package_config.json` 文件。
    5.  编译时，Dart工具根据 `package_config.json` 定位包代码。

### 使用方法
- **`pubspec.yaml` 核心字段** ：
    ```yaml
    name: my_app                 # 必需：包名
    description: A new project.  # 可选
    version: 1.0.0               # 可选，应用需要，库必需
    environment:                 # 必需：SDK 版本约束
      sdk: '^3.0.0'

    dependencies:                # 生产环境依赖
      http: ^0.13.5              # 使用插值语法
      path: 1.8.3                # 使用精确版本

    dev_dependencies:            # 开发环境依赖
      test: ^1.24.0
      lints: ^2.1.0
    ```

- **版本管理** ：
    - **`^1.2.3`**：插值语法，表示兼容 `1.2.3` 的任何版本（即 `>=1.2.3 <2.0.0`）。**推荐使用**。
    - **`>=1.2.3 <2.0.0`**：范围语法。
    - **`1.2.3`**：精确版本。

- **`pubspec.lock` 文件**：
    - 记录了所有直接和传递依赖的**具体版本号**。
    - **必须提交到版本控制系统**（如Git），以确保所有开发者和部署环境使用完全相同的依赖版本，保证构建一致性 。

- **依赖本地或Git仓库** ：
    ```yaml
    dependencies:
      my_local_package:
        path: ../my_local_package  # 依赖本地包
      my_git_package:
        git:
          url: https://github.com/xx/xx.git
          ref: main                # 指定分支、标签或commit
    ```

### 注意事项
- **核心规则**：**永远不要导入其他包 `lib/src` 目录下的文件**。这被视为私有API，包作者可以随时修改而不通知，会导致你的应用在更新依赖后崩溃 。

## 知识点六：可见性与封装实践（`_`私有成员）

### 概念
- **是什么**：在Dart中，以下划线 `_` 开头的标识符（如变量、方法、类）被视为**库私有**的，它们仅在当前库内可见 。
- **为什么需要**：封装是面向对象的核心原则，通过隐藏内部实现细节，可以降低模块间的耦合，提高代码的安全性和可维护性 。
- **解决什么问题**：强制定义API边界，防止外部代码意外依赖或篡改内部状态。

### 原理
- **底层实现**：这是由Dart编译器执行的**静态检查**。当在一个库中尝试访问另一个库的 `_` 私有成员时，编译器会报错。
- **关键技术点**：
    - **可见性的单位是“库”，而不是“类”** 。同一个库内的不同类（或顶层函数）之间可以互相访问私有成员。
    - 通过 `part` 关联的所有文件属于同一个库，因此它们之间也可以互相访问私有成员。

### 使用方法
- **基本用法** ：
    ```dart
    // person.dart (这是一个库)
    class Person {
      String _name;    // 私有字段
      int _age;

      Person(this._name, this._age);

      // 通过公有getter提供只读访问
      String get name => _name;

      // 通过公有setter提供受控写入
      set age(int value) {
        if (value >= 0) _age = value;
      }

      void _incrementAge() => _age++; // 私有方法

      void haveBirthday() {
        _incrementAge(); // 同一个库内可以调用私有方法
      }
    }

    // main.dart (不同的库)
    import 'person.dart';

    void main() {
      var p = Person('Alice', 30);
      print(p.name);     // OK: 通过getter访问
      p.age = 31;        // OK: 通过setter修改
      // print(p._name); // 错误: _name是私有的
      // p._incrementAge(); // 错误: _incrementAge是私有的
    }
    ```

- **私有类**：
    ```dart
    class PublicClass {
      final _InternalHelper _helper = _InternalHelper(); // 可以
    }

    class _InternalHelper { // 私有类，仅在此文件内可见
      // ...
    }
    ```

### 注意事项
- **常见错误**：误以为 `_` 私有是“类私有”的，在一个库的不同文件中尝试访问时发现可以访问，感到困惑。需要记住其规则是基于库的 。

## 补充知识点：创建统一的库入口（Barrel File）

虽然表格中未直接列出，但这是使用`export`的一个极其重要的实践。

### 概念
- **是什么**：在 `lib` 目录下创建一个主文件（通常命名为 `<package_name>.dart`），其唯一目的就是通过 `export` 指令将 `lib/src` 目录下所有公开的API重新导出 。
- **为什么需要**：这是Dart包的标准组织形式。它完美地封装了包的内部实现（放在`src`里），同时为用户提供了一个清晰、单一的导入入口 。
- **解决什么问题**：解决了包内代码组织与对外暴露API之间的平衡问题。

### 最佳实践结构
```
my_package/
  lib/
    my_package.dart      <-- Barrel File (主入口)
    src/
      models.dart        <-- 内部实现文件1
      api.dart           <-- 内部实现文件2
      utils.dart         <-- 内部实现文件3
  pubspec.yaml
```

`my_package.dart` 的内容：
```dart
/// 这是 my_package 的公开API入口。
library my_package;

// 导出所有公开的API
export 'src/models.dart';
export 'src/api.dart';
// 不导出 utils.dart 中的某些私有辅助功能，或者如果 utils 都是公开的，也可以导出
export 'src/utils.dart' show HelperFunctionA; // 只导出部分
```
用户只需 `import 'package:my_package/my_package.dart';`，无需关心包内的文件结构 。