## 一、JavaScript 引擎

### 概念
- **是什么**：JavaScript引擎是一个专门处理JavaScript代码的程序或解释器，为所有JS代码（业务逻辑、React核心库、第三方库）提供执行环境。
- **为什么需要**：React Native使用JavaScript编写应用逻辑，必须有一个引擎来解析和执行这些代码，并实现与原生平台的交互。
- **解决什么问题**：提供高性能的JS执行能力，决定应用的启动速度、内存占用和运行效率。

### 原理
- **底层实现**：JavaScript引擎将JS源代码编译为字节码或机器码，管理内存分配、垃圾回收、调用栈等。React Native通过C++层与引擎交互，注入全局对象和方法。
- **工作流程**：
  1. 应用启动时，引擎初始化并加载JS bundle。
  2. 引擎解析JS代码，生成抽象语法树（AST）。
  3. 执行代码，通过JSI（新架构）或Bridge（旧架构）调用原生功能。
  4. 垃圾回收定期清理不再使用的内存。
- **关键技术点**：
  - **即时编译（JIT）**：部分引擎（如V8）在运行时将热点代码编译为机器码提升性能。
  - **预编译字节码**：Hermes支持将JS预编译为字节码，减少启动时的解析时间。
  - **垃圾回收算法**：不同引擎采用不同策略（如标记-清除、分代回收）管理内存。

### 使用方法
- **基本用法**：开发者无需直接操作引擎，React Native自动选择并配置。可通过`react-native.config.js`指定引擎（如Hermes）。
- **常见场景**：
  - 在Android上默认使用Hermes，iOS上使用JavaScriptCore。
  - 可通过配置切换引擎（如启用Hermes的调试功能）。
- **最佳实践**：
  - 保持Hermes启用（0.70+默认），以获得最佳启动性能。
  - 使用Hermes的调试工具（如Chrome DevTools）进行JS调试。

### 注意事项
- **常见错误**：误以为可以随意更换引擎而不考虑兼容性；Hermes不完全支持所有ES6+特性（但现代版本已接近完整）。
- **性能问题**：引擎的GC停顿可能导致UI卡顿，需注意内存泄漏和对象生命周期管理。
- **兼容性**：不同引擎对JS特性的支持有细微差异，建议使用`@react-native-community/cli`的`--reset-cache`避免缓存问题。

### 代码示例
```javascript
// 检查当前使用的引擎（仅开发环境）
if (global.HermesInternal) {
  console.log('正在使用 Hermes 引擎');
} else {
  console.log('正在使用 JavaScriptCore 或其他引擎');
}
```

---

## 二、原生线程

### 概念
- **是什么**：原生线程指移动操作系统的主线程（Android中常称UI线程，iOS中为主线程），以及React Native内部用于处理不同任务的后台线程。
- **为什么需要**：将UI渲染和用户交互与JS执行分离，避免JS长时间运行阻塞界面，保证应用流畅。
- **解决什么问题**：实现多线程并发，确保UI响应优先，防止JS计算导致的掉帧。

### 原理
- **底层实现**：React Native启动时会创建多个原生线程：
  - **主线程（UI线程）**：负责所有原生视图的创建、更新、布局和绘制，以及处理用户触摸事件。
  - **JS线程**：运行JavaScript引擎，执行所有JS代码（业务逻辑、React渲染）。
  - **后台线程**：用于低优先级任务（如Fabric的并发渲染、图片解码、数据预取）。
- **工作流程**：
  1. 用户触摸屏幕，事件由操作系统分发到主线程。
  2. 主线程通过JSI（新架构）或Bridge（旧架构）将事件传递给JS线程。
  3. JS线程执行事件处理函数，可能触发状态更新和重新渲染。
  4. 渲染结果通过Fabric（新架构）或Bridge（旧架构）返回主线程，主线程更新UI。
- **关键技术点**：
  - **线程间通信**：新架构使用JSI实现同步通信，旧架构使用异步消息队列。
  - **并发渲染**：Fabric支持在多个后台线程并行计算布局和影子树。

### 使用方法
- **基本用法**：开发者无需手动管理线程，React Native自动调度。但可以通过`InteractionManager`或`requestAnimationFrame`优化任务优先级。
- **常见场景**：
  - 使用`InteractionManager.runAfterInteractions()`将非紧急任务延迟到动画或交互完成后执行。
  - 使用`LayoutAnimation`自动处理布局变化的动画，避免手动线程调度。
- **最佳实践**：
  - 避免在JS线程执行耗时同步操作（如大量数据计算），可移至`InteractionManager`或Web Worker。
  - 使用`useMemo`、`useCallback`减少不必要的渲染，降低JS线程负载。

### 注意事项
- **常见错误**：在主线程执行耗时JS操作（如同步网络请求）会导致界面卡顿。
- **性能问题**：JS线程过载会直接导致掉帧，需监控帧率（可使用`react-native-performance`等工具）。
- **兼容性**：新架构下线程模型更复杂，旧库可能未考虑多线程并发，需测试兼容性。

### 代码示例
```javascript
import { InteractionManager } from 'react-native';

// 将非紧急任务延迟到交互完成后
InteractionManager.runAfterInteractions(() => {
  // 执行大量计算或数据加载
  processLargeData();
});
```

---

## 三、核心组件

### 概念
- **是什么**：核心组件是React Native提供的一组跨平台UI构建块，每个组件对应一个原生平台的实际视图（如Android的ViewGroup、iOS的UIView）。
- **为什么需要**：让开发者用统一的React语法描述UI，无需分别编写Android和iOS的原生代码。
- **解决什么问题**：封装平台差异，提供一致的开发体验，同时保证渲染结果接近原生。

### 原理
- **底层实现**：
  - 每个核心组件在Android和iOS上都有对应的原生视图管理器（View Manager）。
  - 当JS创建组件时，React Native通过UIManager模块在原生层创建对应的视图实例。
  - 组件的属性（props）通过JSI或Bridge传递给原生层，更新视图属性。
- **工作流程**：
  1. JS代码声明`<View>`组件。
  2. React渲染器生成虚拟DOM，通过Fabric（新架构）或UIManager（旧架构）通知原生层。
  3. 原生层创建对应的`ViewGroup`（Android）或`UIView`（iOS）。
  4. 后续属性变化时，仅更新原生视图的属性，避免重建。
- **关键技术点**：
  - **视图拍平（View Flattening）**：Fabric可自动合并嵌套的布局节点，减少视图层级，提升性能。
  - **样式转换**：React的样式对象通过Yoga引擎转换为原生布局参数。

### 使用方法
- **基本用法**：从'react-native'导入组件并在JSX中使用。
- **常见场景**：
  - 构建页面布局：`<View>`作为容器，`<Text>`显示文本，`<Image>`展示图片。
  - 处理用户输入：`<TextInput>`、`<Switch>`等。
  - 滚动列表：`<ScrollView>`或`<FlatList>`。
- **最佳实践**：
  - 尽量使用`<FlatList>`而非`<ScrollView>`渲染长列表，通过`getItemLayout`优化性能。
  - 避免过度嵌套视图，利用`<View>`的`collapsable`属性（Android）减少无用节点。

### 注意事项
- **常见错误**：误以为所有HTML标签在React Native中都可使用；忘记给文本添加`<Text>`包裹导致报错。
- **性能问题**：频繁更新整个组件树可能导致性能下降，应使用`shouldComponentUpdate`或React.memo优化。
- **兼容性**：某些样式属性（如`boxShadow`）在双平台实现可能不同，需查阅文档。

### 代码示例
```javascript
import React from 'react';
import { View, Text, StyleSheet } from 'react-native';

const MyComponent = () => {
  return (
    <View style={styles.container}>
      <Text style={styles.text}>Hello React Native</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#f0f0f0',
  },
  text: {
    fontSize: 20,
    color: '#333',
  },
});

export default MyComponent;
```

---

## 四、Bridge（旧架构）

### 概念
- **是什么**：Bridge是React Native旧架构中连接JavaScript线程和原生线程的唯一通道，负责异步传递消息。
- **为什么需要**：在旧架构中，JS和原生运行在不同线程且无法直接通信，需要Bridge作为中介。
- **解决什么问题**：实现跨线程的函数调用和数据传递，但牺牲了同步性和部分性能。

### 原理
- **底层实现**：
  - Bridge由C++实现，内部维护一个消息队列。
  - JS端调用原生模块时，将调用信息（模块名、方法名、参数）序列化为JSON，放入队列。
  - 原生线程定期从队列批量取出消息，反序列化后执行，结果再通过相同路径返回。
- **工作流程**：
  1. JS代码调用`NativeModules.MyModule.method(args)`。
  2. 调用信息被序列化，通过Bridge发送到原生侧。
  3. 原生模块执行方法，完成后将结果序列化回传。
  4. JS端通过回调或Promise接收结果。
- **关键技术点**：
  - **异步批处理**：多次调用可合并为一次批量处理，减少线程唤醒开销。
  - **JSON序列化**：所有数据必须可序列化为JSON，无法传递函数或复杂对象引用。

### 使用方法
- **基本用法**：在旧版本中，开发者通过`NativeModules`直接调用原生模块，或创建自己的原生模块并注册到Bridge。
- **常见场景**：
  - 调用设备API（如`CameraRoll`、`Geolocation`）。
  - 自定义原生模块暴露给JS使用。
- **最佳实践**：
  - 尽量减少跨Bridge调用的频率，避免频繁小数据传递。
  - 使用批处理或合并请求减少通信次数。

### 注意事项
- **常见错误**：试图传递不可序列化的数据（如函数、循环引用）导致报错。
- **性能问题**：频繁调用Bridge会造成性能瓶颈，尤其在动画等高频率场景。
- **兼容性**：自新架构默认启用后，Bridge已不再维护，新项目应避免依赖Bridge特性。

### 代码示例
```javascript
// 旧架构下调用原生模块（假设已注册MyModule）
import { NativeModules } from 'react-native';
const { MyModule } = NativeModules;

MyModule.doSomething('参数', (error, result) => {
  if (error) {
    console.error(error);
  } else {
    console.log('结果:', result);
  }
});
```

---

## 五、JSI（JavaScript Interface）

### 概念
- **是什么**：JSI是一个用C++编写的轻量级框架，允许JavaScript引擎直接持有并调用C++对象的引用，实现同步通信和内存共享。
- **为什么需要**：替代异步Bridge，解决旧架构的性能瓶颈和无法同步调用的限制。
- **解决什么问题**：实现JS与原生（C++）之间的零开销通信，支持同步调用、对象引用传递，为新架构（Fabric、TurboModules）提供基础。

### 原理
- **底层实现**：
  - JSI定义了一组C++ API，JS引擎实现这些API即可与JSI交互。
  - 原生模块可创建`HostObject`（C++对象），通过JSI注入JS运行时。
  - 在JS中，`HostObject`表现为普通JS对象，其属性读取和方法调用直接映射到C++函数。
- **工作流程**：
  1. 原生模块用C++实现`HostObject`，注册到JS运行时。
  2. JS代码访问该对象（如`nativeModule.getValue()`）。
  3. JSI将调用转发给对应的C++函数，同步执行并返回值。
  4. 若返回值为C++对象，JSI创建新的`HostObject`引用，无需序列化。
- **关键技术点**：
  - **同步调用**：JS线程直接调用C++函数，不经过消息队列。
  - **内存共享**：C++对象指针可直接在JS中持有，通过JSI方法操作。
  - **可插拔引擎**：JSI抽象了引擎接口，可接入Hermes、V8、JSC等。

### 使用方法
- **基本用法**：应用开发者通常不直接使用JSI，而是通过TurboModules间接使用。库作者可编写C++模块并暴露给JS。
- **常见场景**：
  - 开发高性能原生库（如VisionCamera、Reanimated），需要直接操作内存或同步调用。
  - 将现有C++库封装为React Native模块。
- **最佳实践**：
  - 使用Codegen生成JSI胶水代码，避免手动编写。
  - 注意线程安全：JSI调用可能发生在JS线程，需确保C++代码线程安全。

### 注意事项
- **常见错误**：忘记处理C++异常导致JS崩溃；未正确管理`HostObject`生命周期引起内存泄漏。
- **性能问题**：虽然JSI本身高效，但C++实现若复杂可能阻塞JS线程，需优化算法。
- **兼容性**：JSI是C++ API，需熟悉C++和JNI（Android）/Objective-C++（iOS）桥接。

### 代码示例
```cpp
// C++ 部分：创建一个简单的HostObject
#include <jsi/jsi.h>

using namespace facebook::jsi;

class MyHostObject : public HostObject {
public:
  Value get(Runtime& rt, const PropNameID& name) override {
    auto propName = name.utf8(rt);
    if (propName == "getValue") {
      return Function::createFromHostFunction(rt,
        PropNameID::forAscii(rt, "getValue"), 0,
        [](Runtime& rt, const Value& thisVal, const Value* args, size_t count) -> Value {
          return Value(42); // 返回数字42
        });
    }
    return Value::undefined();
  }
};

// 将HostObject注入JS运行时
runtime.global().setProperty(runtime, "myNativeModule",
  Object::createFromHostObject(runtime, std::make_shared<MyHostObject>()));
```

```javascript
// JS 中使用
const value = myNativeModule.getValue(); // 同步返回42
console.log(value); // 42
```

---

## 六、Turbo Modules

### 概念
- **是什么**：Turbo Modules是新架构中原生模块的标准化实现方式，支持懒加载、类型安全，并基于JSI实现高性能通信。
- **为什么需要**：替代旧架构中需要应用启动时一次性加载所有原生模块的方式，减少启动时间，并提供更好的类型安全。
- **解决什么问题**：实现原生模块的按需加载，避免不必要的初始化开销；通过Codegen保证JS与原生间的类型契约，减少运行时错误。

### 原理
- **底层实现**：
  - Turbo Modules由三部分组成：JS接口定义（TypeScript/Flow）、Codegen生成的C++/原生代码、实际的原生实现。
  - 当JS首次`import`一个Turbo Module时，JSI触发C++层实例化该模块，并将其作为`HostObject`注入JS运行时。
  - 模块实例化后，JS可直接调用其方法，无需二次加载。
- **工作流程**：
  1. 开发者用TypeScript定义模块接口（如`NativeMyModule.ts`）。
  2. React Native的Codegen根据接口生成C++、Android（Kotlin/Java）、iOS（Objective-C/Swift）的模板代码。
  3. 开发者在原生平台实现具体功能。
  4. 应用运行时，JS调用`TurboModuleRegistry.get('MyModule')`首次请求模块。
  5. JSI调用C++工厂函数创建模块实例，返回`HostObject`给JS。
  6. 后续所有调用直接通过该`HostObject`同步执行。
- **关键技术点**：
  - **Codegen**：确保JS和原生层参数类型匹配，自动生成序列化/反序列化代码。
  - **懒加载**：模块仅在首次使用时初始化，未使用的模块不占用内存和启动时间。
  - **跨平台C++**：模块核心逻辑可用C++编写，一次实现支持所有平台。

### 使用方法
- **基本用法**：开发者创建自定义Turbo Module，或使用官方提供的Turbo Modules（如`PlatformColor`、`AccessibilityInfo`）。
- **常见场景**：
  - 需要高性能原生功能的库（如文件操作、数据库访问）。
  - 希望减少应用启动时间的项目，通过懒加载优化。
- **最佳实践**：
  - 遵循Codegen规范定义接口，确保类型安全。
  - 将模块核心逻辑用C++实现，以获得最佳性能和跨平台一致性。
  - 使用`TurboModuleRegistry.get`时考虑错误处理，因为模块可能不存在。

### 注意事项
- **常见错误**：未运行Codegen导致类型不匹配；在模块初始化前调用方法（但懒加载保证了首次调用即初始化）。
- **性能问题**：虽然懒加载好，但首次调用模块会触发实例化，可能造成短暂延迟，需避免在关键路径首次调用。
- **兼容性**：新架构默认启用后，Turbo Modules是推荐方式；但仍可使用互操作层兼容旧模块。

### 代码示例
```typescript
// 定义接口（NativeMyModule.ts）
import type { TurboModule } from 'react-native';
import { TurboModuleRegistry } from 'react-native';

export interface Spec extends TurboModule {
  readonly getConstants: () => {|
    someConstant: string,
  |};
  doSomething(input: string): Promise<string>;
}

export default TurboModuleRegistry.get<Spec>('MyModule');
```

```kotlin
// Android实现（MyModule.kt）
package com.myapp
import com.facebook.react.turbomodule.core.interfaces.TurboModule
// ... 其他导入

class MyModule(reactContext: ReactApplicationContext) : NativeMyModuleSpec(reactContext) {
  override fun getConstants(): Map<String, Any> = mapOf("someConstant" to "Hello")

  override fun doSomething(input: String, promise: Promise) {
    promise.resolve("Processed: $input")
  }
}
```

```javascript
// JS使用
import MyModule from './NativeMyModule';

async function test() {
  const result = await MyModule.doSomething('参数');
  console.log(result); // "Processed: 参数"
}
```

---

## 七、Fabric渲染器

### 概念
- **是什么**：Fabric是React Native新架构中全新的UI渲染系统，取代旧架构中的原生渲染方式。
- **为什么需要**：解决旧架构渲染异步、无法同步读取布局、无法利用多线程渲染等问题，提升UI性能和开发体验。
- **解决什么问题**：实现同步布局计算、多线程并发渲染、视图拍平优化，并支持React的并发特性。

### 原理
- **底层实现**：
  - Fabric的渲染核心用C++编写，称为"渲染管道"（Renderer Pipeline）。
  - React在JS侧生成组件树，Fabric在C++侧创建对应的"影子树"（Shadow Tree），每个影子节点包含组件的布局和属性。
  - 影子树由Yoga引擎计算布局，然后通过JSI将变更同步到原生视图。
  - 原生视图管理器根据差异更新实际的UIView/ViewGroup。
- **工作流程**：
  1. React在JS线程触发更新，创建新的React元素树。
  2. Fabric的C++核心接收更新，创建新的影子树。
  3. Yoga引擎在后台线程计算布局（可并行）。
  4. Fabric比较新旧影子树，生成原生视图的变更指令（插入、更新、删除）。
  5. 通过JSI将指令同步发送到主线程，主线程执行UI更新。
- **关键技术点**：
  - **不可变树**：每次更新都新建影子树，保证线程安全。
  - **视图拍平**：自动合并不必要的嵌套视图，减少原生视图层级。
  - **并发渲染**：低优先级更新可在后台线程渲染，不阻塞用户交互。
  - **同步布局读取**：JS可以同步读取布局信息（如`measure`），无需异步回调。

### 使用方法
- **基本用法**：开发者无需特别配置，新架构启用后Fabric自动生效。但可通过`useLayoutEffect`等Hooks利用同步布局能力。
- **常见场景**：
  - 使用`useLayoutEffect`在布局后立即读取尺寸并调整UI。
  - 使用`<View>`的`onLayout`获取位置信息，但新架构下可在同一渲染周期内同步更新。
- **最佳实践**：
  - 利用`useLayoutEffect`处理需要同步布局信息的副作用。
  - 避免在`useLayoutEffect`中执行耗时操作，以免阻塞绘制。

### 注意事项
- **常见错误**：在`useLayoutEffect`中触发状态更新可能导致无限循环，需小心依赖项。
- **性能问题**：影子树的创建和布局计算虽然高效，但频繁整体重建可能仍消耗CPU，应合理使用`React.memo`和`shouldComponentUpdate`。
- **兼容性**：部分第三方UI库可能未适配Fabric的同步特性，需测试或等待更新。

### 代码示例
```javascript
import React, { useLayoutEffect, useRef, useState } from 'react';
import { View, Text } from 'react-native';

const MeasureExample = () => {
  const viewRef = useRef(null);
  const [dimensions, setDimensions] = useState({ width: 0, height: 0 });

  useLayoutEffect(() => {
    // 同步测量，无需等待下一帧
    viewRef.current?.measure((x, y, width, height) => {
      setDimensions({ width, height }); // 会在本次渲染周期内应用
    });
  }, []);

  return (
    <View ref={viewRef} style={{ flex: 1 }}>
      <Text>宽度: {dimensions.width}, 高度: {dimensions.height}</Text>
    </View>
  );
};
```

---

## 八、Codegen

### 概念
- **是什么**：Codegen是React Native新架构中的代码生成工具，根据TypeScript或Flow接口定义自动生成C++、Android、iOS的胶水代码。
- **为什么需要**：手动编写跨平台桥接代码容易出错且重复，Codegen确保类型安全，减少样板代码。
- **解决什么问题**：自动生成JS与原生之间的类型检查和序列化代码，避免类型错误导致的应用崩溃，提升开发效率。

### 原理
- **底层实现**：
  - Codegen作为React Native CLI的一部分，在构建时运行。
  - 它解析项目中所有用TypeScript/Flow编写的原生模块接口文件（通常命名为`Native<ModuleName>.ts`）。
  - 根据接口生成：
    - C++头文件和实现（供JSI使用）。
    - Android的Java/Kotlin模板（实现`NativeModuleSpec`抽象类）。
    - iOS的Objective-C++模板（实现`RCTTurboModule`协议）。
  - 生成的文件位于`build/generated`目录，开发者无需手动修改。
- **工作流程**：
  1. 开发者编写接口定义，描述模块的常量、方法及其参数/返回类型。
  2. 运行`npx react-native codegen`或在构建时自动触发。
  3. Codegen生成各平台的胶水代码，确保类型匹配。
  4. 开发者在生成的模板基础上实现具体业务逻辑。
  5. 最终编译时，胶水代码与业务代码一起打包。
- **关键技术点**：
  - **类型安全**：JS调用的参数类型必须在编译时匹配原生期望的类型，否则Codegen报错。
  - **跨平台一致性**：生成的C++核心可被Android和iOS共享，减少重复代码。
  - **增量生成**：仅当接口文件变化时重新生成，加速构建。

### 使用方法
- **基本用法**：在项目中创建`specs`目录，按规范编写接口文件，然后配置`package.json`中的`codegenConfig`。
- **常见场景**：
  - 创建自定义Turbo Module时，必须提供接口文件以供Codegen生成。
  - 库作者发布包含Turbo Modules的库时，需包含接口文件，确保使用者也受益于Codegen。
- **最佳实践**：
  - 使用TypeScript而非Flow（更广泛使用），并严格定义类型。
  - 将接口文件放在`src/specs`目录，并在`package.json`中指定。
  - 在CI中运行Codegen检查，确保接口变更不会破坏兼容性。

### 注意事项
- **常见错误**：接口定义与实际原生实现不一致，导致运行时崩溃；未运行Codegen直接调用模块。
- **性能问题**：Codegen本身只在构建时运行，不影响运行时性能。
- **兼容性**：确保项目使用的React Native版本支持新架构和Codegen（0.68+），且TypeScript版本符合要求。

### 代码示例
```typescript
// src/specs/NativeCalculator.ts
import type { TurboModule } from 'react-native';
import { TurboModuleRegistry } from 'react-native';

export interface Spec extends TurboModule {
  add(a: number, b: number): number;
  multiply(a: number, b: number): Promise<number>;
}

export default TurboModuleRegistry.get<Spec>('Calculator');
```

```json
// package.json
{
  "codegenConfig": {
    "name": "AppSpecs",
    "type": "modules",
    "jsSrcsDir": "./src/specs"
  }
}
```

运行`npx react-native codegen`后，将生成C++/Android/iOS的模板，开发者只需填充实现。

---

## 九、事件循环（Event Loop）

### 概念
- **是什么**：事件循环是React Native新架构中定义JavaScript线程上任务执行顺序的机制，遵循HTML标准规范。
- **为什么需要**：为了支持React的并发特性（如可中断渲染），需要规范任务调度，明确微任务、宏任务、渲染步骤的执行时机。
- **解决什么问题**：使React Native中的事件处理、定时器、渲染更新等行为与Web标准对齐，为未来支持更多浏览器API打下基础。

### 原理
- **底层实现**：
  - React Native的JS运行时（如Hermes）实现了符合HTML规范的事件循环。
  - 事件循环维护多个任务队列：宏任务队列（setTimeout、setInterval）、微任务队列（Promise.then、MutationObserver）、渲染步骤（requestAnimationFrame、布局更新）。
  - 每个循环迭代中，先执行一个宏任务，然后清空所有微任务，最后执行渲染步骤（如有必要）。
- **工作流程**：
  1. JS线程空闲时，事件循环从宏任务队列取出一个任务执行。
  2. 该任务执行过程中可能产生微任务，这些微任务会被添加到微任务队列。
  3. 宏任务执行完毕后，事件循环清空微任务队列中的所有微任务。
  4. 检查是否需要渲染（是否有`requestAnimationFrame`回调或UI更新），执行渲染步骤。
  5. 重复以上步骤。
- **关键技术点**：
  - **可中断渲染**：React在渲染过程中可中断低优先级更新，让事件循环优先处理用户输入等紧急任务。
  - **任务优先级**：虽然事件循环本身不直接支持优先级，但React的并发特性通过调度器（Scheduler）在事件循环之上实现优先级调度。

### 使用方法
- **基本用法**：开发者通过标准的异步API（如`setTimeout`、`Promise`、`requestAnimationFrame`）与事件循环交互。
- **常见场景**：
  - 使用`setTimeout`延迟执行非关键任务。
  - 使用`Promise`处理异步操作。
  - 使用`requestAnimationFrame`实现流畅动画。
- **最佳实践**：
  - 避免长时间运行的同步任务，以免阻塞事件循环。
  - 使用`InteractionManager`将非紧急任务推迟到交互完成后执行（该API基于事件循环实现）。

### 注意事项
- **常见错误**：误以为`setTimeout(fn, 0)`会在当前任务结束后立即执行，实际上它会被放入宏任务队列，需等待当前事件循环结束。
- **性能问题**：过多的微任务（如无限`Promise.resolve().then(...)`）可能导致事件循环卡在微任务阶段，使渲染延迟。
- **兼容性**：新架构事件循环与浏览器行为高度一致，但部分API（如`requestIdleCallback`）尚未实现。

### 代码示例
```javascript
console.log('1');

setTimeout(() => {
  console.log('2'); // 宏任务
}, 0);

Promise.resolve().then(() => {
  console.log('3'); // 微任务
});

console.log('4');

// 输出顺序: 1, 4, 3, 2
// 因为宏任务在当前宏任务结束后执行，微任务在当前宏任务结束后立即执行
```

---

## 十、Yoga布局引擎

### 概念
- **是什么**：Yoga是一个用C++编写的跨平台布局引擎，实现Flexbox布局模型，React Native使用它来计算组件的尺寸和位置。
- **为什么需要**：将CSS的Flexbox布局能力带到移动端，确保JS层声明的布局在Android和iOS上表现一致。
- **解决什么问题**：提供高效、跨平台的布局计算，支持复杂的弹性布局，减少手动布局的工作量。

### 原理
- **底层实现**：
  - Yoga将视图层次结构转换为布局树，每个节点包含样式属性（如`flexDirection`、`justifyContent`）。
  - 根据Flexbox规范，Yoga执行两次遍历：从根节点向下计算尺寸（如果未确定）和位置；再从子节点向上更新父节点尺寸。
  - 最终为每个节点计算出相对于父节点的位置和尺寸。
- **工作流程**：
  1. Fabric创建影子树时，为每个影子节点关联一个Yoga节点。
  2. 所有Yoga节点组成一棵布局树。
  3. 当需要布局时，Yoga遍历树，结合节点样式和约束（如父节点宽度）计算每个节点的最终框架。
  4. 计算结果存储回影子节点，供原生视图更新使用。
- **关键技术点**：
  - **双向绑定**：Yoga支持测量函数，允许自定义文本等需要自行测量尺寸的内容。
  - **缓存机制**：Yoga缓存布局结果，当节点属性未变时跳过重新计算。
  - **线程安全**：Yoga节点可在不同线程计算，Fabric利用此特性实现并发布局。

### 使用方法
- **基本用法**：开发者通过`StyleSheet`创建样式对象，使用Flexbox属性（`flex`、`flexDirection`、`alignItems`等）控制布局。
- **常见场景**：
  - 实现响应式布局，适配不同屏幕尺寸。
  - 创建弹性容器，自动分配剩余空间。
  - 处理文本换行和图像缩放。
- **最佳实践**：
  - 使用`flex`简写而非分别设置`flexGrow`、`flexShrink`、`flexBasis`，除非需要精细控制。
  - 避免在深层次嵌套中使用过多的绝对定位，尽量利用Flexbox的自然流式布局。

### 注意事项
- **常见错误**：混淆CSS的Flexbox和Yoga的细微差异（如Yoga默认`flexDirection`为`column`而非`row`）。
- **性能问题**：频繁动态改变样式会导致Yoga反复计算，应尽量减少布局属性变化。
- **兼容性**：Yoga支持所有主流Flexbox属性，但部分CSS特性（如`grid`）不支持。

### 代码示例
```javascript
import { StyleSheet, View, Text } from 'react-native';

const styles = StyleSheet.create({
  container: {
    flex: 1,
    flexDirection: 'row',      // 水平排列子元素
    justifyContent: 'space-between', // 子元素两端对齐
    alignItems: 'center',       // 垂直居中
    padding: 20,
  },
  box: {
    width: 50,
    height: 50,
    backgroundColor: 'blue',
  },
});

const LayoutExample = () => (
  <View style={styles.container}>
    <View style={styles.box} />
    <View style={styles.box} />
    <View style={styles.box} />
  </View>
);
```

---

## 十一、新架构的并发特性与React支持

### 概念
- **是什么**：新架构通过Fabric和事件循环的改进，全面支持React 18及更高版本的并发渲染特性，如Suspense、Transitions、自动批处理。
- **为什么需要**：旧架构无法支持并发渲染，限制了React现代特性在移动端的应用。新架构解锁这些能力，提升用户体验和开发体验。
- **解决什么问题**：使React Native应用能够更好地管理渲染优先级，实现可中断渲染、状态过渡平滑，避免掉帧。

### 原理
- **底层实现**：
  - **并发渲染器**：React 18的并发渲染器与Fabric的不可变影子树和后台线程结合，允许在内存中构建多个版本的UI，并根据优先级提交。
  - **调度器**：React的调度器利用事件循环的宏任务机制，将低优先级更新分解为多个小任务，给高优先级任务让路。
  - **自动批处理**：新架构中，无论是同步还是异步更新，React都能自动合并多个状态更新为一次渲染，减少不必要的UI刷新。
- **工作流程**：
  1. 用户触发紧急事件（如输入），React标记为高优先级。
  2. 调度器暂停当前低优先级渲染，优先处理高优先级更新。
  3. 高优先级更新通过Fabric快速提交到原生层显示。
  4. 低优先级更新在后台线程继续，完成后合并到最终UI。
- **关键技术点**：
  - **Suspense**：允许组件等待异步数据，同时显示fallback UI，避免空白状态。
  - **Transitions**：将某些更新标记为非紧急，让UI保持响应性。
  - **useDeferredValue**：延迟更新部分UI，优先显示新输入。

### 使用方法
- **基本用法**：直接使用React 18+的并发API，如`startTransition`、`Suspense`。
- **常见场景**：
  - 在搜索框中，用户输入应高优更新，搜索结果可延迟渲染。
  - 使用`Suspense`包裹数据加载组件，显示加载指示器。
  - 利用自动批处理减少不必要的重绘。
- **最佳实践**：
  - 将非关键更新包裹在`startTransition`中，提升交互流畅度。
  - 为异步组件添加`Suspense`边界，提供更好的加载体验。
  - 使用`useTransition`获取pending状态，展示加载反馈。

### 注意事项
- **常见错误**：滥用`startTransition`导致重要更新被延迟；未处理Suspense的fallback导致白屏。
- **性能问题**：并发渲染虽好，但若每个更新都创建新树，内存开销可能增大，需注意优化。
- **兼容性**：需要React 18+和新架构同时启用，旧架构不支持这些特性。

### 代码示例
```javascript
import React, { useState, useTransition, Suspense } from 'react';
import { View, Text, TextInput, ActivityIndicator } from 'react-native';

const SearchComponent = () => {
  const [query, setQuery] = useState('');
  const [searchResults, setSearchResults] = useState([]);
  const [isPending, startTransition] = useTransition();

  const handleChange = (text) => {
    setQuery(text); // 紧急更新：立即显示输入
    startTransition(() => {
      // 非紧急更新：搜索结果
      const results = performExpensiveSearch(text);
      setSearchResults(results);
    });
  };

  return (
    <View>
      <TextInput value={query} onChangeText={handleChange} />
      {isPending && <ActivityIndicator />}
      <Suspense fallback={<Text>加载中...</Text>}>
        <ResultsList data={searchResults} />
      </Suspense>
    </View>
  );
};
```

---

以上为React Native架构核心知识点的完整整理，涵盖旧架构、新架构及各核心组件的概念、原理、使用方法、注意事项和代码示例。所有内容基于官方文档和权威资料，确保准确性和完整性。