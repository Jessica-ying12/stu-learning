## 一、文本输入基础

### 1.1 TextField

#### 概念
- **是什么**：`TextField` 是 Flutter 中最基础的文本输入组件，遵循 Material Design 风格，允许用户通过键盘输入文本。
- **为什么需要**：几乎所有应用都需要用户输入文本（如登录、搜索、评论），`TextField` 提供了标准的输入框外观和交互。
- **解决什么问题**：它封装了与键盘交互、文本显示、光标管理、输入限制等复杂逻辑，让开发者只需简单配置即可获得功能完整的文本输入框。

#### 原理
- **底层实现**：`TextField` 是一个组合组件，内部由 `EditableText`（核心渲染层）、`_TextFieldSelectionGestureDetector`（手势处理）和多个 `RenderObject` 构成。它通过 `TextEditingController` 管理文本状态，通过 `FocusNode` 管理焦点。
- **工作流程**：
  1. 用户触摸输入框，`FocusNode` 请求焦点，键盘弹出。
  2. 用户键入字符，`EditableText` 接收系统键盘输入事件，更新 `TextEditingController` 中的文本和选区。
  3. `TextField` 监听 `TextEditingController` 的变化，通过 `setState` 刷新 UI 显示新文本。
  4. 开发者可以通过 `onChanged`、`onSubmitted` 等回调获取输入事件。
- **关键技术点**：
  - **无状态设计**：`TextField` 本身不存储文本，文本完全由外部传入的 `controller` 管理。
  - **组合优于继承**：通过 `decoration`、`inputFormatters` 等属性高度可定制。

#### 使用方法
- **基本用法**：
  ```dart
  TextField(
    onChanged: (text) => print(text),
    decoration: InputDecoration(hintText: '请输入内容'),
  );
  ```
- **常见场景**：
  - 单行文本输入（默认）：`maxLines: 1`
  - 多行文本输入：`maxLines: 3` 或 `maxLines: null`（无限行）
  - 密码输入：`obscureText: true`
  - 数字输入：`keyboardType: TextInputType.number`
  - 限制字符数：`maxLength: 20`
- **最佳实践**：
  - 当需要动态读取或设置输入值时，务必使用 `TextEditingController`。
  - 对于敏感信息（如密码），设置 `obscureText` 并考虑添加切换可见性的 `suffixIcon`。
  - 合理使用 `inputFormatters` 限制输入格式，如 `FilteringTextInputFormatter.digitsOnly`。

#### 注意事项
- **常见错误**：
  - 忘记释放 `TextEditingController` 导致内存泄漏。
  - 在无状态组件中使用 `TextField` 且未绑定 `controller`，导致无法获取输入值。
- **性能问题**：
  - 避免在 `onChanged` 中执行耗时操作，否则会阻塞 UI 渲染。
  - 对于频繁更新 UI 的场景，考虑使用 `TextEditingController` 的监听器替代 `onChanged` 以减少重建。
- **兼容性**：`TextField` 自动适应不同平台（iOS/Android）的键盘样式，但可通过 `keyboardAppearance` 微调。

#### 代码示例
```dart
class SimpleTextField extends StatefulWidget {
  @override
  _SimpleTextFieldState createState() => _SimpleTextFieldState();
}

class _SimpleTextFieldState extends State<SimpleTextField> {
  final TextEditingController _controller = TextEditingController();

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        TextField(
          controller: _controller,
          decoration: InputDecoration(
            labelText: '用户名',
            hintText: '请输入用户名',
            prefixIcon: Icon(Icons.person),
          ),
          onChanged: (value) => print('输入中：$value'),
          onSubmitted: (value) => print('提交：$value'),
        ),
        ElevatedButton(
          onPressed: () => print('当前输入：${_controller.text}'),
          child: Text('获取输入'),
        ),
      ],
    );
  }
}
```

---

### 1.2 TextFormField

#### 概念
- **是什么**：`TextFormField` 是对 `TextField` 的封装，专门用于与 `Form` 组件集成，提供表单验证、数据保存和重置功能。
- **为什么需要**：在复杂表单中，手动管理多个输入框的验证和状态非常繁琐，`TextFormField` 配合 `Form` 可以简化这一过程。
- **解决什么问题**：它让开发者能够通过 `validator` 声明式定义验证规则，并通过 `FormState` 统一触发验证和保存。

#### 原理
- **底层实现**：`TextFormField` 继承自 `FormField<String>`，内部包含一个 `TextField` 并委托其完成输入。它通过 `FormFieldState` 管理自身的验证状态（如错误信息），并与祖先 `Form` 通过 `InheritedWidget` 通信。
- **工作流程**：
  1. 用户输入文本，`TextField` 更新值并回调给 `FormField`。
  2. 当 `FormState.validate()` 被调用时，`FormField` 执行其 `validator` 函数，若返回非空字符串则更新错误状态并重新渲染。
  3. 当 `FormState.save()` 被调用时，`FormField` 执行 `onSaved` 回调，将当前值传递给父组件。
- **关键技术点**：
  - 利用 `FormField` 的泛型设计，支持多种类型（如 `String`、`int`）的验证。
  - 自动处理验证错误的显示样式（红色边框、错误文本）。

#### 使用方法
- **基本用法**：
  ```dart
  TextFormField(
    decoration: InputDecoration(labelText: '邮箱'),
    validator: (value) {
      if (value == null || value.isEmpty) return '邮箱不能为空';
      if (!value.contains('@')) return '请输入有效的邮箱';
      return null;
    },
    onSaved: (value) => _email = value,
  );
  ```
- **常见场景**：
  - 表单中需要验证的输入字段。
  - 配合 `autovalidateMode` 实现即时验证反馈。
- **最佳实践**：
  - 始终将 `TextFormField` 包裹在 `Form` 组件内。
  - 使用 `GlobalKey<FormState>` 统一管理表单。
  - 在 `validator` 中返回具体的错误信息，避免笼统提示。

#### 注意事项
- **常见错误**：
  - 没有提供 `GlobalKey`，导致无法调用 `FormState` 方法。
  - `validator` 返回 `null` 表示验证通过，错误地返回空字符串会导致错误显示。
- **性能问题**：`autovalidateMode: always` 可能引发频繁验证，推荐使用 `onUserInteraction`。
- **兼容性**：与 `TextField` 相同，所有属性均支持。

#### 代码示例
```dart
final _formKey = GlobalKey<FormState>();
String _username;

Form(
  key: _formKey,
  child: Column(
    children: [
      TextFormField(
        decoration: InputDecoration(labelText: '用户名'),
        validator: (value) => value!.isEmpty ? '用户名不能为空' : null,
        onSaved: (value) => _username = value!,
      ),
      ElevatedButton(
        onPressed: () {
          if (_formKey.currentState!.validate()) {
            _formKey.currentState!.save();
            print('提交用户名：$_username');
          }
        },
        child: Text('提交'),
      ),
    ],
  ),
);
```

---

## 二、输入框装饰与控制

### 2.1 InputDecoration

#### 概念
- **是什么**：`InputDecoration` 是一个配置类，用于定制 `TextField` 和 `TextFormField` 的视觉外观，如标签、提示、图标、边框和错误信息。
- **为什么需要**：良好的视觉设计能提升用户体验，`InputDecoration` 提供了丰富且一致的 Material Design 样式。
- **解决什么问题**：它将输入框的装饰属性（如边框样式、标签位置）集中管理，避免开发者手动布局，同时自动响应焦点、错误等状态变化。

#### 原理
- **底层实现**：`InputDecoration` 的数据会被传递给 `_InputDecorator` 组件，该组件根据当前状态（焦点、错误、禁用）组合多个子组件（如标签、提示、图标）并应用对应的样式。最终通过 `DecoratedBox` 等绘制边框。
- **工作流程**：
  1. 用户设置 `decoration` 属性。
  2. 输入框根据焦点、错误等状态，选择相应的 `border`、`labelStyle` 等。
  3. `_InputDecorator` 在布局时计算各个元素的位置（例如当获得焦点时，`labelText` 会浮动到上方）。
- **关键技术点**：
  - **状态派生**：自动根据 `errorText`、`enabled`、焦点等状态切换样式。
  - **浮动标签**：通过 `floatingLabelBehavior` 控制标签是否浮动。

#### 使用方法
- **基本用法**：
  ```dart
  TextField(
    decoration: InputDecoration(
      labelText: '姓名',
      hintText: '请输入真实姓名',
      prefixIcon: Icon(Icons.person),
      border: OutlineInputBorder(),
    ),
  );
  ```
- **常见场景**：
  - 设置不同状态下的边框：`enabledBorder`、`focusedBorder`、`errorBorder`。
  - 添加后缀图标（如清空按钮）：`suffixIcon: IconButton(...)`。
  - 自定义计数器：`counterText: '${value.length}/20'`。
- **最佳实践**：
  - 尽量使用 `labelText` 而非 `hintText` 提供字段说明，因为 `labelText` 在输入后仍可见。
  - 使用 `OutlineInputBorder` 获得现代感外观。
  - 通过 `errorStyle` 自定义错误信息样式，避免布局抖动。

#### 注意事项
- **常见错误**：
  - 同时设置 `labelText` 和 `hintText` 导致视觉重叠（`hintText` 在输入后消失，而 `labelText` 浮动）。
  - 未设置 `border` 时默认使用 `UnderlineInputBorder`，可能与预期不符。
- **性能问题**：无显著性能问题。
- **兼容性**：所有属性均适用于 Material Design，Cupertino 风格需使用 `CupertinoTextField` 的 `decoration`（不同类）。

#### 代码示例
```dart
TextField(
  decoration: InputDecoration(
    labelText: '密码',
    hintText: '至少8位',
    prefixIcon: Icon(Icons.lock),
    suffixIcon: Icon(Icons.visibility_off),
    border: OutlineInputBorder(
      borderRadius: BorderRadius.circular(8),
    ),
    focusedBorder: OutlineInputBorder(
      borderSide: BorderSide(color: Colors.blue, width: 2),
    ),
    errorBorder: OutlineInputBorder(
      borderSide: BorderSide(color: Colors.red),
    ),
    errorText: _errorMessage, // 动态设置错误
  ),
);
```

---

### 2.2 TextEditingController

#### 概念
- **是什么**：`TextEditingController` 是一个用于关联 `TextField` 并控制其文本内容和选区的类。
- **为什么需要**：`TextField` 本身无状态，必须通过控制器来获取、设置和监听文本，实现数据双向绑定。
- **解决什么问题**：它提供了一种统一的机制来管理文本输入状态，支持初始值设置、动态修改、监听变化以及控制光标选区。

#### 原理
- **底层实现**：`TextEditingController` 继承自 `ValueNotifier<TextEditingValue>`，内部维护一个 `TextEditingValue` 对象，包含 `text`（文本）和 `selection`（选区）。当值改变时，`ValueNotifier` 通知所有监听器（包括 `TextField`），触发 UI 更新。
- **工作流程**：
  1. 创建控制器并传入 `TextField`。
  2. 用户输入导致 `TextField` 调用 `controller.value = newValue`，更新内部值并通知监听器。
  3. 开发者通过 `controller.addListener` 监听变化，或通过 `controller.text` 读取最新值。
  4. 在 `State` 销毁时，调用 `controller.dispose()` 释放资源。
- **关键技术点**：
  - **值不可变性**：`TextEditingValue` 是不可变对象，每次修改都会创建新实例。
  - **监听链**：多个 `TextField` 可以共用同一个控制器（罕见但可能）。

#### 使用方法
- **基本用法**：
  ```dart
  final controller = TextEditingController(); // 无初始值
  final controllerWithInit = TextEditingController(text: '默认文本');
  ```
- **常见场景**：
  - **获取输入值**：`controller.text`
  - **设置新值**：`controller.text = '新文本'`（自动更新 UI）
  - **监听变化**：`controller.addListener(() => print(controller.text))`
  - **控制选区**：`controller.selection = TextSelection(baseOffset: 0, extentOffset: 3)`
- **最佳实践**：
  - 在 `StatefulWidget` 中创建控制器，并在 `dispose` 中释放。
  - 避免在 `build` 方法中创建控制器，以免每次重建丢失状态。
  - 使用 `controller.clear()` 清空文本。

#### 注意事项
- **常见错误**：
  - **内存泄漏**：未调用 `dispose()`。
  - **在 `build` 中创建控制器**：导致每次重建时控制器被重置，且旧控制器无法释放。
  - **直接修改 `controller.value` 时忘记更新选区**：例如设置文本后光标可能跑到末尾，可通过 `controller.value = controller.value.copyWith(text: newText, selection: TextSelection.collapsed(offset: newText.length))` 控制。
- **性能问题**：监听器过多可能影响性能，但通常可忽略。
- **兼容性**：适用于所有文本输入组件。

#### 代码示例
```dart
class ControllerDemo extends StatefulWidget {
  @override
  _ControllerDemoState createState() => _ControllerDemoState();
}

class _ControllerDemoState extends State<ControllerDemo> {
  final TextEditingController _controller = TextEditingController();

  @override
  void initState() {
    super.initState();
    _controller.addListener(_printLatestValue);
  }

  void _printLatestValue() {
    print('当前文本: ${_controller.text}');
  }

  @override
  void dispose() {
    _controller.removeListener(_printLatestValue);
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        TextField(controller: _controller),
        ElevatedButton(
          onPressed: () => _controller.text = '重置文本',
          child: Text('重置'),
        ),
      ],
    );
  }
}
```

---

## 三、表单管理与验证

### 3.1 Form 与 FormField

#### 概念
- **是什么**：`Form` 是一个容器组件，用于将多个 `FormField`（如 `TextFormField`、`DropdownButtonFormField`）组合在一起，并提供统一的状态管理。`FormField` 是抽象基类，定义了表单字段的通用接口。
- **为什么需要**：在复杂表单中，需要集中验证、保存和重置多个字段，手动管理每个字段的状态和错误信息非常低效。
- **解决什么问题**：`Form` 提供了一种声明式的方式来组织表单字段，并通过 `FormState` 提供全局操作（验证、保存、重置）。

#### 原理
- **底层实现**：`Form` 通过 `InheritedWidget` 向其子孙 `FormField` 共享一个 `_FormScope` 对象。每个 `FormField` 内部维护一个 `FormFieldState`，该状态会注册到 `Form` 的 `_FormState` 中。当调用 `FormState` 的方法时，它会遍历所有已注册的 `FormFieldState` 并执行相应操作。
- **工作流程**：
  1. 创建 `GlobalKey<FormState>` 并绑定到 `Form`。
  2. 子孙 `FormField` 在初始化时将自身状态注册到 `Form`。
  3. 调用 `_formKey.currentState.validate()` 时，`Form` 遍历所有 `FormFieldState` 并执行它们的 `validate` 方法，收集错误。
  4. 若全部验证通过，返回 `true`；否则返回 `false` 并显示错误。
  5. 调用 `save()` 时，触发每个 `FormField` 的 `onSaved` 回调。
- **关键技术点**：
  - **依赖 `GlobalKey`**：必须通过 `GlobalKey` 访问 `FormState`。
  - **动态注册**：字段可以动态添加/移除，`Form` 会自动更新状态列表。

#### 使用方法
- **基本用法**：
  ```dart
  final _formKey = GlobalKey<FormState>();

  Form(
    key: _formKey,
    child: Column(
      children: [
        TextFormField(/* ... */),
        TextFormField(/* ... */),
        ElevatedButton(
          onPressed: () {
            if (_formKey.currentState!.validate()) {
              _formKey.currentState!.save();
              // 处理数据
            }
          },
          child: Text('提交'),
        ),
      ],
    ),
  );
  ```
- **常见场景**：
  - 登录/注册表单。
  - 设置页面多字段编辑。
  - 需要统一提交的数据录入界面。
- **最佳实践**：
  - 使用 `GlobalKey` 时，确保 `currentState` 不为 `null`（通常在 `build` 完成后才可用，可在按钮点击时安全使用）。
  - 在 `Form` 外调用 `validate` 前，可先检查 `_formKey.currentState != null`。
  - 利用 `onSaved` 收集数据，而非手动从每个控制器读取。

#### 注意事项
- **常见错误**：
  - 未给 `Form` 设置 `key`，导致无法获取 `FormState`。
  - 在 `Form` 内部使用了非 `FormField` 的自定义组件，无法参与验证（需自己实现 `FormField` 或使用现有封装）。
- **性能问题**：当表单字段很多时，遍历验证对性能影响很小，无需优化。
- **兼容性**：`Form` 和 `FormField` 是通用组件，适用于任何平台。

#### 代码示例
```dart
class RegistrationForm extends StatefulWidget {
  @override
  _RegistrationFormState createState() => _RegistrationFormState();
}

class _RegistrationFormState extends State<RegistrationForm> {
  final _formKey = GlobalKey<FormState>();
  String _username, _email;

  @override
  Widget build(BuildContext context) {
    return Form(
      key: _formKey,
      child: Column(
        children: [
          TextFormField(
            decoration: InputDecoration(labelText: '用户名'),
            validator: (value) => value!.isEmpty ? '用户名不能为空' : null,
            onSaved: (value) => _username = value!,
          ),
          TextFormField(
            decoration: InputDecoration(labelText: '邮箱'),
            validator: (value) {
              if (value!.isEmpty) return '邮箱不能为空';
              if (!value.contains('@')) return '邮箱格式不正确';
              return null;
            },
            onSaved: (value) => _email = value!,
          ),
          ElevatedButton(
            onPressed: () {
              if (_formKey.currentState!.validate()) {
                _formKey.currentState!.save();
                print('注册信息：用户名=$_username, 邮箱=$_email');
              }
            },
            child: Text('注册'),
          ),
        ],
      ),
    );
  }
}
```

---

## 四、选择控件

### 4.1 Checkbox

#### 概念
- **是什么**：`Checkbox` 是 Material Design 风格的复选框控件，允许用户切换“选中”或“未选中”状态，或处于不确定状态。
- **为什么需要**：用于多选或同意条款等需要表示二元或三态选择的场景。
- **解决什么问题**：提供标准的复选框交互，并可与 `ListTile` 结合快速构建列表项。

#### 原理
- **底层实现**：`Checkbox` 是一个无状态组件，内部通过 `_CheckboxRenderObjectWidget` 渲染。它的状态（是否选中）完全由外部传入的 `value` 和 `onChanged` 回调控制。当用户点击时，调用 `onChanged` 并传入新值，由父组件更新状态。
- **工作流程**：
  1. 父组件构建时传入当前 `value`（如 `true`）和 `onChanged` 回调。
  2. 用户点击复选框，`Checkbox` 触发 `onChanged(!value)`。
  3. 父组件接收新值，调用 `setState` 更新 `value`，导致 `Checkbox` 使用新值重新渲染。
- **关键技术点**：
  - **tristate 模式**：当 `tristate` 为 `true` 时，`value` 可为 `null` 表示不确定状态，常用于表示子选项部分选中。

#### 使用方法
- **基本用法**：
  ```dart
  Checkbox(
    value: _isChecked,
    onChanged: (bool? newValue) {
      setState(() { _isChecked = newValue!; });
    },
  );
  ```
- **常见场景**：
  - 同意用户协议：`tristate: false`（默认）。
  - 树形节点选择：使用 `tristate: true` 和 `null` 表示部分选中。
  - 配合 `CheckboxListTile` 快速构建带标题的复选框。
- **最佳实践**：
  - 始终通过 `setState` 更新状态，确保 UI 同步。
  - 当复选框表示“全选”时，可结合 `tristate` 处理部分选中状态。

#### 注意事项
- **常见错误**：
  - 忘记在 `onChanged` 中更新状态，导致复选框无反应。
  - 在 `tristate: true` 时未正确处理 `null` 值。
- **性能问题**：无。
- **兼容性**：Material Design 组件，iOS 上也可使用，但建议使用 `CupertinoCheckbox` 保持平台一致性。

#### 代码示例
```dart
class CheckboxDemo extends StatefulWidget {
  @override
  _CheckboxDemoState createState() => _CheckboxDemoState();
}

class _CheckboxDemoState extends State<CheckboxDemo> {
  bool _isChecked = false;
  bool _isAgree = false;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        CheckboxListTile(
          title: Text('同意用户协议'),
          value: _isAgree,
          onChanged: (value) => setState(() => _isAgree = value!),
          secondary: Icon(Icons.assignment),
        ),
        Checkbox(
          value: _isChecked,
          onChanged: (value) => setState(() => _isChecked = value!),
        ),
      ],
    );
  }
}
```

---

### 4.2 Radio

#### 概念
- **是什么**：`Radio` 是 Material Design 风格的单选按钮控件，用于从一组互斥的选项中选择一个。
- **为什么需要**：在需要用户从多个选项中选其一的场景（如性别选择、支付方式）中，单选按钮是标准交互模式。
- **解决什么问题**：它强制用户只能选择一个选项，避免多选歧义。

#### 原理
- **底层实现**：`Radio` 也是无状态组件，通过 `value` 和 `groupValue` 决定是否选中。当 `value == groupValue` 时显示为选中状态。点击时调用 `onChanged` 传入自身的 `value`，由父组件更新 `groupValue`。
- **工作流程**：
  1. 父组件维护一个 `groupValue` 变量，并传递给所有 `Radio`。
  2. 每个 `Radio` 有唯一的 `value`。
  3. 用户点击某个 `Radio`，其 `onChanged` 被调用，传入该 `Radio` 的 `value`。
  4. 父组件将 `groupValue` 更新为新值，所有 `Radio` 重新评估选中状态。
- **关键技术点**：
  - **互斥性**：同一组 `Radio` 必须共享同一个 `groupValue` 变量。
  - **toggleable 属性**：允许用户取消选择（将 `groupValue` 置为 `null`）。

#### 使用方法
- **基本用法**：
  ```dart
  int _selectedRadio = 0;

  Column(
    children: [
      Radio(value: 0, groupValue: _selectedRadio, onChanged: (v) => setState(() => _selectedRadio = v!)),
      Radio(value: 1, groupValue: _selectedRadio, onChanged: (v) => setState(() => _selectedRadio = v!)),
    ],
  );
  ```
- **常见场景**：
  - 性别选择：`value` 使用枚举类型。
  - 支付方式选择：`value` 使用字符串标识。
  - 启用 `toggleable` 让用户可以不选任何项。
- **最佳实践**：
  - 使用枚举作为 `value` 类型，增强代码可读性。
  - 配合 `RadioListTile` 快速构建带标题的单选列表。

#### 注意事项
- **常见错误**：
  - 多个 `Radio` 使用不同的 `groupValue` 变量，导致互斥失效。
  - 未处理 `onChanged` 传入的 `null` 值（当 `toggleable` 为 `true` 时可能发生）。
- **性能问题**：无。
- **兼容性**：Material Design，iOS 上有 `CupertinoRadio`。

#### 代码示例
```dart
enum Gender { male, female }

class RadioDemo extends StatefulWidget {
  @override
  _RadioDemoState createState() => _RadioDemoState();
}

class _RadioDemoState extends State<RadioDemo> {
  Gender _selectedGender = Gender.male;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        RadioListTile<Gender>(
          title: Text('男'),
          value: Gender.male,
          groupValue: _selectedGender,
          onChanged: (value) => setState(() => _selectedGender = value!),
        ),
        RadioListTile<Gender>(
          title: Text('女'),
          value: Gender.female,
          groupValue: _selectedGender,
          onChanged: (value) => setState(() => _selectedGender = value!),
        ),
      ],
    );
  }
}
```

---

### 4.3 Switch

#### 概念
- **是什么**：`Switch` 是 Material Design 风格的开关控件，用于切换单个设置的“开/关”状态。
- **为什么需要**：相较于复选框，开关更直观地表示“启用/禁用”操作，常见于设置页面。
- **解决什么问题**：提供触感友好的开关交互，支持平台自适应（通过 `Switch.adaptive`）。

#### 原理
- **底层实现**：`Switch` 内部使用 `_SwitchRenderObjectWidget` 渲染，状态管理逻辑与 `Checkbox` 完全相同：通过 `value` 和 `onChanged` 实现双向绑定。
- **工作流程**：
  1. 父组件传入当前 `value` 和 `onChanged`。
  2. 用户滑动或点击开关，`onChanged` 被调用，传入新的布尔值。
  3. 父组件更新状态，触发 `Switch` 重新渲染。
- **关键技术点**：
  - **自适应**：`Switch.adaptive` 根据平台自动选择 `Switch`（Android）或 `CupertinoSwitch`（iOS）。

#### 使用方法
- **基本用法**：
  ```dart
  Switch(
    value: _isEnabled,
    onChanged: (bool newValue) {
      setState(() { _isEnabled = newValue; });
    },
  );
  ```
- **常见场景**：
  - 设置页面的选项开关（如 Wi-Fi、蓝牙）。
  - 暗黑模式切换。
  - 使用 `SwitchListTile` 快速构建带标题的开关项。
- **最佳实践**：
  - 当需要跨平台一致性时，使用 `Switch.adaptive`。
  - 开关的状态变更应直接反映功能变化，避免延迟。

#### 注意事项
- **常见错误**：
  - 未在 `onChanged` 中更新状态，导致开关无法滑动。
  - 将开关用于非布尔值场景。
- **性能问题**：无。
- **兼容性**：`Switch.adaptive` 在 iOS 上显示为 `CupertinoSwitch`，样式略有不同。

#### 代码示例
```dart
class SwitchDemo extends StatefulWidget {
  @override
  _SwitchDemoState createState() => _SwitchDemoState();
}

class _SwitchDemoState extends State<SwitchDemo> {
  bool _isDarkMode = false;

  @override
  Widget build(BuildContext context) {
    return SwitchListTile(
      title: Text('深色模式'),
      value: _isDarkMode,
      onChanged: (value) => setState(() => _isDarkMode = value),
      secondary: Icon(_isDarkMode ? Icons.dark_mode : Icons.light_mode),
    );
  }
}
```

---

## 五、滑块控件

### 5.1 Slider

#### 概念
- **是什么**：`Slider` 是 Material Design 风格的滑块控件，允许用户从一个连续或离散的数值范围中选择一个值。
- **为什么需要**：当需要用户输入一个范围中的数值（如音量、亮度）时，滑块提供了比文本输入更直观的操作方式。
- **解决什么问题**：它将数值选择转化为滑动手势，实时反馈数值变化，提升用户体验。

#### 原理
- **底层实现**：`Slider` 是无状态组件，通过 `value`、`min`、`max` 定义当前值和范围，`onChanged` 处理滑动事件。内部使用 `_SliderRenderObjectWidget` 绘制滑块轨道、拇指和刻度。
- **工作流程**：
  1. 用户触摸并拖动滑块拇指。
  2. 底层计算新值（受 `min`、`max` 和 `divisions` 约束），通过 `onChanged` 回调。
  3. 父组件更新 `value`，滑块重新定位。
  4. 可选的 `onChangeStart` 和 `onChangeEnd` 用于性能优化。
- **关键技术点**：
  - **离散模式**：通过设置 `divisions` 将滑块划分为若干步长。
  - **标签**：通过 `label` 显示当前值的悬浮提示。

#### 使用方法
- **基本用法**：
  ```dart
  double _sliderValue = 0.5;

  Slider(
    value: _sliderValue,
    onChanged: (newValue) {
      setState(() { _sliderValue = newValue; });
    },
  );
  ```
- **常见场景**：
  - 音量调节：`min: 0, max: 100, divisions: 100`。
  - 亮度调节：连续模式。
  - 带标签显示当前值：`label: _sliderValue.round().toString()`。
- **最佳实践**：
  - 使用 `onChangeEnd` 处理耗时操作（如保存到数据库），避免每次滑动都执行。
  - 对于离散值，设置 `divisions` 并确保 `value` 始终落在步长上。

#### 注意事项
- **常见错误**：
  - 未在 `onChanged` 中更新 `value`，导致滑块拖动无反馈。
  - `value` 超出 `min`-`max` 范围导致断言错误。
- **性能问题**：`onChanged` 频繁触发，若执行复杂操作会卡顿，建议使用 `onChangeEnd`。
- **兼容性**：Material Design，iOS 上可使用 `CupertinoSlider`。

#### 代码示例
```dart
class SliderDemo extends StatefulWidget {
  @override
  _SliderDemoState createState() => _SliderDemoState();
}

class _SliderDemoState extends State<SliderDemo> {
  double _value = 30.0;

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('当前值：${_value.round()}'),
        Slider(
          value: _value,
          min: 0,
          max: 100,
          divisions: 10,
          label: _value.round().toString(),
          onChanged: (newValue) => setState(() => _value = newValue),
          onChangeEnd: (newValue) => print('最终选择：$newValue'),
        ),
      ],
    );
  }
}
```

---

### 5.2 RangeSlider

#### 概念
- **是什么**：`RangeSlider` 是 Material Design 风格的范围滑块控件，允许用户选择一个包含起始值和结束值的区间。
- **为什么需要**：在价格区间筛选、日期范围选择等场景中，需要同时选择最小值和最大值。
- **解决什么问题**：它提供了一个双拇指滑块，让用户通过拖动两端来定义范围。

#### 原理
- **底层实现**：`RangeSlider` 内部使用 `_RangeSliderRenderObjectWidget` 渲染，通过 `values`（`RangeValues` 类型）表示当前范围，`min`、`max` 定义总范围。`onChanged` 返回新的 `RangeValues`。
- **工作流程**：
  1. 用户拖动左侧或右侧拇指，底层计算新的 `start` 或 `end` 值。
  2. `onChanged` 回调携带新的 `RangeValues`。
  3. 父组件更新 `values`，UI 同步。
  4. 支持离散模式（`divisions`）和标签（`RangeLabels`）。
- **关键技术点**：
  - **`RangeValues`**：包含 `start` 和 `end` 两个 `double` 字段，且保证 `start <= end`。
  - **双标签**：通过 `RangeLabels` 为两端分别设置悬浮标签。

#### 使用方法
- **基本用法**：
  ```dart
  RangeValues _values = RangeValues(20, 70);

  RangeSlider(
    values: _values,
    min: 0,
    max: 100,
    onChanged: (newValues) {
      setState(() { _values = newValues; });
    },
  );
  ```
- **常见场景**：
  - 电商价格筛选：`min: 0, max: 1000, divisions: 10`。
  - 时间范围选择：结合标签显示具体值。
- **最佳实践**：
  - 使用 `RangeLabels` 提供实时数值反馈。
  - 通过 `onChangeEnd` 触发搜索请求，避免频繁调用。

#### 注意事项
- **常见错误**：
  - 未更新 `values`，导致滑块拖动无效。
  - `values.start` 或 `values.end` 超出 `min`/`max` 范围。
- **性能问题**：同 `Slider`。
- **兼容性**：Material Design，iOS 无直接对应组件，但可接受。

#### 代码示例
```dart
class RangeSliderDemo extends StatefulWidget {
  @override
  _RangeSliderDemoState createState() => _RangeSliderDemoState();
}

class _RangeSliderDemoState extends State<RangeSliderDemo> {
  RangeValues _values = RangeValues(20, 80);

  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        Text('范围：${_values.start.round()} - ${_values.end.round()}'),
        RangeSlider(
          values: _values,
          min: 0,
          max: 100,
          divisions: 10,
          labels: RangeLabels(
            _values.start.round().toString(),
            _values.end.round().toString(),
          ),
          onChanged: (newValues) => setState(() => _values = newValues),
        ),
      ],
    );
  }
}
```

---

以上是 Flutter 中所有核心输入表单控件的完整学习笔记。每个知识点均按照“概念-原理-使用方法-注意事项-代码示例”的模板规范撰写，确保内容全面且符合官方标准。希望这份笔记能帮助你深入理解和高效使用 Flutter 表单组件。