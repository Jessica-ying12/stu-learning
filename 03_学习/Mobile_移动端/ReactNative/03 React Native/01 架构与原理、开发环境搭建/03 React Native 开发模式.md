## 一、React Native 概述

### 1.1 概念

- **是什么**  
  React Native 是一个由 Meta（原 Facebook）开源的跨平台移动应用开发框架，允许开发者使用 JavaScript 和 React 构建真正运行在 iOS 和 Android 平台上的原生应用。它通过将 React 组件映射为原生 UI 组件，实现了“一次学习，随处编写”的开发体验。

- **为什么需要**  
  传统移动开发需要为 iOS 和 Android 分别使用 Swift/Objective-C 和 Java/Kotlin 编写两套代码，成本高、周期长。而 Web 技术开发的混合应用（如 Cordova）性能差、体验不佳。React Native 在保证接近原生性能的同时，实现了代码复用，大幅降低了开发和维护成本。

- **解决什么问题**  
  解决了跨平台开发的效率与性能难以兼得的矛盾，让 Web 开发者能够利用已有的 React 知识快速进入移动开发领域，同时保持应用的流畅度和原生体验。

### 1.2 原理

- **底层实现**  
  React Native 的核心是一个 **C++ 桥接层**，负责 JavaScript 线程与原生线程之间的异步通信。JavaScript 代码通过 **JavaScriptCore**（iOS）或 **V8**（Android，或使用 Hermes 引擎）执行，并通过桥接调用原生模块（如摄像头、GPS）；原生模块也可以通过桥接向 JavaScript 发送事件。

- **工作流程**  
  1. **打包**：Metro 打包器将 JavaScript 代码打包成一个或多个 bundle 文件。  
  2. **加载**：应用启动时加载 bundle，JavaScript 代码开始执行。  
  3. **渲染**：React 组件通过 `render` 函数返回的 JSX 被转换为虚拟 DOM 树，桥接层将虚拟 DOM 的变化转化为对应的原生 UI 操作（创建、更新、删除原生视图）。  
  4. **事件响应**：用户交互触发原生事件，通过桥接传递给 JavaScript 中对应的事件处理函数，更新组件状态，触发 UI 重绘。

- **关键技术点**  
  - **原生组件映射**：`<View>` 对应 iOS 的 `UIView`、Android 的 `View`；`<Text>` 对应 `UITextView` 和 `TextView` 等。  
  - **桥接通信**：异步、序列化的 JSON 消息传递。  
  - **布局引擎**：使用 Yoga 引擎将 Flexbox 布局转换为原生布局参数。  
  - **热重载**：通过 WebSocket 监听文件变化，增量更新 bundle，实现快速刷新。

### 1.3 使用方法

- **基本用法**  
  创建一个新的 React Native 项目（以 CLI 为例）：
  ```bash
  npx react-native init MyApp
  cd MyApp
  npx react-native run-ios  # 或 run-android
  ```
  编写一个简单的组件：
  ```jsx
  import React from 'react';
  import { View, Text, StyleSheet } from 'react-native';

  const App = () => (
    <View style={styles.container}>
      <Text style={styles.text}>Hello React Native!</Text>
    </View>
  );

  const styles = StyleSheet.create({
    container: { flex: 1, justifyContent: 'center', alignItems: 'center' },
    text: { fontSize: 20 },
  });

  export default App;
  ```

- **常见场景**  
  - 从零开始构建跨平台应用。  
  - 在现有原生应用中嵌入 React Native 页面。  
  - 复用业务逻辑和 UI 组件，减少重复开发。

- **最佳实践**  
  - 遵循平台设计规范，使用 `Platform.select` 区分平台差异。  
  - 合理使用 `FlatList` 优化长列表性能。  
  - 使用 TypeScript 提升代码健壮性。

### 1.4 注意事项

- **常见错误**  
  - 桥接调用过多导致 UI 卡顿：避免在频繁操作（如动画、滚动）中发起大量桥接通信。  
  - 样式兼容性：某些 CSS 属性（如 `box-shadow`）在不同平台需使用特定样式。

- **性能问题**  
  - 避免在 `render` 方法中执行耗时操作。  
  - 使用 `React.memo` 或 `PureComponent` 减少不必要的重渲染。  
  - 使用 Hermes 引擎提升启动性能和减少包体积。

- **兼容性**  
  - 新版本 React Native 可能移除部分旧 API，升级时需查阅迁移指南。  
  - 第三方库需要适配对应版本的 React Native。

### 1.5 代码示例

```jsx
// 一个简单的跨平台计数器组件
import React, { useState } from 'react';
import { View, Text, Button, Platform, StyleSheet } from 'react-native';

const Counter = () => {
  const [count, setCount] = useState(0);

  return (
    <View style={styles.container}>
      <Text style={styles.text}>Count: {count}</Text>
      <Button title="Increment" onPress={() => setCount(count + 1)} />
      {Platform.OS === 'ios' && <Text>This is iOS</Text>}
      {Platform.OS === 'android' && <Text>This is Android</Text>}
    </View>
  );
};

const styles = StyleSheet.create({
  container: { flex: 1, justifyContent: 'center', alignItems: 'center' },
  text: { fontSize: 18, marginBottom: 10 },
});

export default Counter;
```

---

## 二、开发模式与生产模式

### 2.1 概念

- **是什么**  
  React Native 应用可以运行在两种全局模式下：**开发模式**和**生产模式**。开发模式面向开发者，提供丰富的调试工具和实时反馈；生产模式面向最终用户，追求最佳性能和最小包体积。

- **为什么需要**  
  区分两种模式是为了兼顾开发效率和用户体验。开发时需要快速迭代、错误提示清晰；发布时需要代码压缩、性能优化。

- **解决什么问题**  
  解决了开发体验与生产性能之间的冲突，使同一套代码能在不同环境下以不同形态运行。

### 2.2 原理

- **底层实现**  
  - **开发模式**：打包时保留代码格式、注释和详细的错误信息；通过 WebSocket 与开发服务器保持连接，支持热重载；注入开发者菜单（摇一摇触发）。  
  - **生产模式**：打包时对代码进行**混淆（uglify）**和**压缩（minify）**，移除 `console.log` 和开发警告；禁用开发者菜单；使用离线 bundle，不依赖开发服务器。

- **工作流程**  
  1. **开发阶段**：运行 `npx expo start` 或 `npx react-native start` 启动 Metro，应用从开发服务器加载 bundle，启用热重载。  
  2. **构建阶段**：执行 `expo build` 或 `react-native run-ios --configuration Release`，Metro 以生产模式打包，生成优化后的 bundle 和原生应用包。  
  3. **运行阶段**：用户安装的应用以生产模式运行，仅加载本地 bundle，无调试功能。

- **关键技术点**  
  - **打包器（Metro）**：根据环境变量 `__DEV__` 决定打包行为。  
  - **开发者菜单**：仅在开发模式下编译到应用中。  
  - **性能监控**：生产模式下自动关闭性能统计。

### 2.3 使用方法

- **基本用法**  
  - 开发模式：默认本地启动。  
    ```bash
    npx expo start  # Expo 项目
    npx react-native start  # CLI 项目
    ```
  - 生产模式：构建独立应用包。  
    ```bash
    eas build -p ios --profile production  # Expo + EAS
    cd ios && xcodebuild -configuration Release  # CLI 原生构建
    ```

- **常见场景**  
  - 开发时使用开发模式，调试代码。  
  - 发布前在模拟器或真机上以生产模式运行，进行最终性能测试和体验验收。

- **最佳实践**  
  - 开发中尽量模拟生产环境数据，避免因调试信息导致误判。  
  - 在 CI/CD 流程中自动构建生产包，确保每次发布都是生产模式。

### 2.4 注意事项

- **常见错误**  
  - 发布前未切换生产模式，导致包体积过大或性能低下。  
  - 生产模式下 `console.log` 被移除，依赖日志的调试代码可能失效。

- **性能问题**  
  - 生产模式包体积更小，启动更快，内存占用更低。  
  - 开发模式可能因热重载导致应用偶尔卡顿。

- **兼容性**  
  - 某些第三方库在生产模式下行为可能不同（如动画库的调试模式），需测试。

### 2.5 代码示例

```jsx
// 利用 __DEV__ 全局变量区分模式
if (__DEV__) {
  console.log('当前是开发模式');
  // 加载模拟数据或开启调试工具
} else {
  // 生产模式下上报错误或禁用调试功能
}
```

---

## 三、Expo 工具链

### 3.1 概念

- **是什么**  
  Expo 是一套围绕 React Native 构建的开源工具链和托管服务，旨在通过“约定优于配置”的理念，极大简化 React Native 应用的开发、构建和发布流程。

- **为什么需要**  
  React Native 原生开发需要配置复杂的原生环境（Xcode、Android Studio），且手动集成第三方库繁琐。Expo 将这些复杂性抽象化，让开发者专注于 JavaScript/TypeScript 代码。

- **解决什么问题**  
  解决了原生开发环境配置门槛高、原生模块集成复杂的问题，使 Web 开发者也能快速上手移动开发，并大幅提升开发效率。

### 3.2 原理

- **底层实现**  
  Expo 工具链包含以下核心部分：  
  - **Expo CLI**：命令行工具，用于创建、运行和构建项目。  
  - **Expo SDK**：一套预构建的、经过测试的原生模块集合，提供统一的 JavaScript API。  
  - **Expo Go**：一个通用的原生应用客户端，用于快速预览。  
  - **EAS (Expo Application Services)**：云服务，用于构建、提交和更新应用。  
  - **Config Plugin**：允许在不弹出项目的情况下修改原生配置。

- **工作流程**  
  1. 使用 `create-expo-app` 创建项目。  
  2. 编写 JavaScript/TypeScript 代码，通过 `expo` 命名空间调用原生功能。  
  3. 使用 `expo start` 启动开发服务器，通过 Expo Go 预览。  
  4. 需要原生扩展时，通过 EAS Build 生成自定义开发构建或生产包。  
  5. 通过 EAS Submit 直接上传到应用商店。

- **关键技术点**  
  - **托管工作流**：Expo 管理所有原生代码，开发者无需接触原生文件夹。  
  - **免配置**：自动处理 iOS 和 Android 项目配置。  
  - **丰富的 API**：提供超过 50 个模块，覆盖常见功能。

### 3.3 使用方法

- **基本用法**  
  创建并运行一个 Expo 项目：
  ```bash
  npx create-expo-app@latest MyExpoApp
  cd MyExpoApp
  npx expo start
  ```
  安装并使用一个 Expo 模块（如摄像头）：
  ```bash
  npx expo install expo-camera
  ```
  ```jsx
  import { Camera } from 'expo-camera';
  // 使用 Camera 组件
  ```

- **常见场景**  
  - 快速原型设计。  
  - 不需要自定义原生代码的纯 JS 应用。  
  - 利用 Expo 的云服务简化 CI/CD 流程。

- **最佳实践**  
  - 优先使用 Expo SDK 提供的 API，避免过早引入自定义原生代码。  
  - 使用 `expo prebuild` 生成原生项目，以便在需要时随时修改原生配置。  
  - 结合 EAS Update 实现 OTA 更新。

### 3.4 注意事项

- **常见错误**  
  - 错误地使用需要原生代码的库而未构建开发版本，导致在 Expo Go 中运行失败。  
  - 忽略 Config Plugin 直接修改原生文件，导致下次预构建时覆盖。

- **性能问题**  
  - Expo SDK 本身经过优化，但过度依赖大型模块可能增加包体积。  
  - 使用 EAS Build 时，云构建时间可能较长。

- **兼容性**  
  - Expo SDK 版本与 React Native 版本绑定，升级时需同步更新。  
  - 某些原生功能可能因平台限制在 Expo Go 中不可用。

### 3.5 代码示例

```jsx
// 使用 Expo 的摄像头模块
import React, { useState, useEffect } from 'react';
import { Text, View, TouchableOpacity } from 'react-native';
import { Camera } from 'expo-camera';

export default function App() {
  const [hasPermission, setHasPermission] = useState(null);
  const [type, setType] = useState(Camera.Constants.Type.back);

  useEffect(() => {
    (async () => {
      const { status } = await Camera.requestCameraPermissionsAsync();
      setHasPermission(status === 'granted');
    })();
  }, []);

  if (hasPermission === null) {
    return <View />;
  }
  if (hasPermission === false) {
    return <Text>No access to camera</Text>;
  }
  return (
    <View style={{ flex: 1 }}>
      <Camera style={{ flex: 1 }} type={type}>
        <View style={{ flex: 1, backgroundColor: 'transparent', flexDirection: 'row' }}>
          <TouchableOpacity
            style={{ alignSelf: 'flex-end', alignItems: 'center' }}
            onPress={() => {
              setType(
                type === Camera.Constants.Type.back
                  ? Camera.Constants.Type.front
                  : Camera.Constants.Type.back
              );
            }}>
            <Text style={{ fontSize: 18, marginBottom: 10, color: 'white' }}> Flip </Text>
          </TouchableOpacity>
        </View>
      </Camera>
    </View>
  );
}
```

---

## 四、Expo Go 客户端

### 4.1 概念

- **是什么**  
  Expo Go 是一个预构建好的、包含了 Expo SDK 所有核心原生模块的通用原生应用，可以在 iOS 和 Android 真机上安装。开发者通过扫描二维码，即可在 Expo Go 中加载并运行自己的 React Native 项目。

- **为什么需要**  
  在开发初期，无需编译原生代码就能在真机上快速预览应用，极大缩短了反馈循环，特别适合学习和原型验证。

- **解决什么问题**  
  解决了原生应用开发中“修改代码 → 重新编译 → 等待”的低效问题，实现了即时预览。

### 4.2 原理

- **底层实现**  
  Expo Go 是一个已签名的、包含了完整 Expo SDK 原生模块的独立应用。当开发服务器启动后，Expo Go 通过局域网或隧道连接到开发机，下载并执行 JavaScript bundle，同时通过 WebSocket 与开发服务器保持热重载连接。

- **工作流程**  
  1. 在项目中运行 `npx expo start`，启动 Metro 打包器和开发服务器。  
  2. 终端或浏览器生成一个二维码。  
  3. 用户打开真机上的 Expo Go 应用，扫描二维码。  
  4. Expo Go 从开发服务器下载 bundle 并运行，显示应用界面。  
  5. 代码修改后，Expo Go 自动或手动刷新，立即看到效果。

- **关键技术点**  
  - **隧道技术**：当手机与电脑不在同一局域网时，Expo 可通过 ngrok 等工具建立安全隧道，实现远程访问。  
  - **热重载**：基于 WebSocket 的模块热替换。  
  - **固定原生层**：Expo Go 的原生模块版本固定，无法扩展。

### 4.3 使用方法

- **基本用法**  
  1. 在手机应用商店搜索“Expo Go”并安装。  
  2. 在项目目录运行 `npx expo start`。  
  3. 使用手机相机（iOS）或 Expo Go 应用内扫码（Android）扫描终端二维码。  
  4. 应用即运行在手机上。

- **常见场景**  
  - 新手学习 React Native 时快速验证代码。  
  - 设计师或产品经理查看 UI 效果。  
  - 快速演示原型给客户。

- **最佳实践**  
  - 尽量保持手机与电脑在同一 WiFi 网络，以获得更快加载速度。  
  - 使用 `--tunnel` 选项解决跨网络问题。  
  - 注意 Expo Go 的局限性，当需要使用自定义原生模块时及时切换到开发构建。

### 4.4 注意事项

- **常见错误**  
  - 安装了需要原生代码的第三方库，但在 Expo Go 中运行时报错“Native module not found”。  
  - 手机和电脑不在同一网络且未使用隧道，导致无法连接。

- **性能问题**  
  - Expo Go 加载的是未压缩的开发 bundle，可能比生产包慢。  
  - 某些 API（如蓝牙、后台定位）在 Expo Go 中可能受限。

- **兼容性**  
  - iOS 上的 Expo Go 仅支持最新几个 SDK 版本，旧项目可能无法运行。  
  - 部分原生功能（如推送通知）无法在 Expo Go 中完整测试。

### 4.5 代码示例

```bash
# 启动 Expo 开发服务器，支持局域网和隧道
npx expo start --tunnel
```

```jsx
// 在代码中检测是否运行在 Expo Go 中
import { Constants } from 'expo-constants';

if (Constants.appOwnership === 'expo') {
  console.log('Running in Expo Go');
} else {
  console.log('Running in standalone app or development build');
}
```

---

## 五、React Native CLI

### 5.1 概念

- **是什么**  
  React Native CLI 是官方提供的原生开发模式，它将 iOS 和 Android 原生项目完全暴露给开发者，允许开发者对原生代码进行任意修改和扩展。

- **为什么需要**  
  当项目需要使用 Expo 不提供的原生功能，或需要与现有原生应用集成时，开发者需要直接操作原生代码。CLI 模式提供了这种灵活性。

- **解决什么问题**  
  解决了 Expo 托管工作流无法满足的自定义原生需求，让开发者拥有对原生层的完全控制权。

### 5.2 原理

- **底层实现**  
  React Native CLI 初始化项目时会生成标准的 iOS（`ios/` 文件夹）和 Android（`android/` 文件夹）原生项目结构。JavaScript 代码通过 Metro 打包后，由原生应用加载并运行。原生模块需要手动链接或使用自动链接机制。

- **工作流程**  
  1. 使用 `npx react-native init` 创建项目，生成原生文件夹。  
  2. 开发时运行 `npx react-native start` 启动 Metro。  
  3. 运行 `npx react-native run-ios` 或 `run-android`，编译原生代码并安装应用到设备/模拟器。  
  4. 修改原生代码（如添加 Swift 文件）后，需要重新编译。

- **关键技术点**  
  - **自动链接**：React Native 0.60+ 支持自动链接，安装带有原生代码的库时会自动修改原生项目配置。  
  - **原生模块开发**：开发者可以编写自定义原生模块，通过桥接暴露给 JavaScript。  
  - **手动配置**：某些复杂库仍需手动修改 `build.gradle`、`Podfile` 或 Info.plist。

### 5.3 使用方法

- **基本用法**  
  创建并运行一个 CLI 项目：
  ```bash
  npx react-native init MyCLIApp
  cd MyCLIApp
  npx react-native run-ios   # 或 run-android
  ```
  安装一个需要原生代码的库（如 `react-native-fs`）：
  ```bash
  npm install react-native-fs
  cd ios && pod install     # iOS 需要
  ```

- **常见场景**  
  - 需要深度定制原生功能（如自定义相机、蓝牙驱动）。  
  - 集成 React Native 到现有原生应用。  
  - 使用某些不支持 Expo 的第三方 SDK。

- **最佳实践**  
  - 使用 TypeScript 编写 JavaScript 代码，提高可维护性。  
  - 利用 CocoaPods 和 Gradle 管理原生依赖。  
  - 定期运行 `npx react-native doctor` 检查环境。

### 5.4 注意事项

- **常见错误**  
  - 忘记在 iOS 端运行 `pod install`，导致链接失败。  
  - 修改原生文件后未重新编译，运行时出现“symbol not found”错误。

- **性能问题**  
  - 原生代码错误可能导致应用崩溃，需仔细测试。  
  - 手动管理原生依赖版本，避免冲突。

- **兼容性**  
  - 不同 React Native 版本对应的原生 API 可能变化，升级需谨慎。  
  - 需要开发者熟悉 Xcode 和 Android Studio 的基本操作。

### 5.5 代码示例

```bash
# 创建一个 CLI 项目并运行
npx react-native init MyCLIApp
cd MyCLIApp
npx react-native run-ios
```

```jsx
// 调用一个自定义原生模块（假设已实现）
import { NativeModules } from 'react-native';
const { MyCustomModule } = NativeModules;

MyCustomModule.doSomething('hello', (result) => {
  console.log(result);
});
```

---

## 六、Expo 开发构建

### 6.1 概念

- **是什么**  
  Expo 开发构建（Development Build）是一个包含 `expo-dev-client` 库的自定义原生应用，它允许开发者在保留 Expo 便捷工具链的同时，使用任意原生代码，并在真机上进行调试。

- **为什么需要**  
  当项目需求超出 Expo Go 的能力范围（如需要使用自定义原生模块或测试原生配置），但又不想放弃 Expo 的便捷工具（如配置插件、EAS 服务）时，开发构建提供了两全其美的方案。

- **解决什么问题**  
  解决了 Expo Go 的局限性，使开发者可以在 Expo 生态中构建生产级应用，同时享受原生开发的完全灵活性。

### 6.2 原理

- **底层实现**  
  `expo-dev-client` 是一个库，将其添加到 Expo 项目后，运行 `expo prebuild` 会生成包含该库的原生项目。通过 EAS Build 或本地编译，生成一个包含了项目所有原生依赖的自定义应用。该应用启动时会连接开发服务器，支持热重载和开发者菜单。

- **工作流程**  
  1. 在 Expo 项目中安装 `expo-dev-client`。  
  2. 配置需要使用的原生库（如 `react-native-reanimated`）。  
  3. 使用 EAS Build 或本地命令生成开发构建包。  
  4. 将构建包安装到设备上。  
  5. 运行 `expo start --dev-client`，然后在设备上打开应用，它会自动连接开发服务器。  
  6. 修改代码，热重载生效。

- **关键技术点**  
  - **dev-client**：提供开发服务器连接、开发者菜单等功能。  
  - **预构建**：`expo prebuild` 生成原生项目，允许修改原生配置。  
  - **EAS Build**：云构建服务，简化打包和分发流程。

### 6.3 使用方法

- **基本用法**  
  1. 创建 Expo 项目并安装 dev-client：
     ```bash
     npx create-expo-app MyApp
     cd MyApp
     npx expo install expo-dev-client
     ```
  2. 安装一个需要原生代码的库（如 `react-native-video`）：
     ```bash
     npm install react-native-video
     ```
  3. 使用 EAS 构建开发构建：
     ```bash
     eas build --platform ios --profile development
     ```
  4. 下载并安装生成的 app。  
  5. 运行 `expo start --dev-client`，然后在设备上打开 app 进行开发。

- **常见场景**  
  - 需要使用 `react-native-reanimated` 2/3 等高性能动画库。  
  - 需要测试推送通知、应用图标等原生配置。  
  - 团队协作分发统一的开发环境。

- **最佳实践**  
  - 为开发构建单独配置 EAS profile（如 `development`），与应用商店版本区分。  
  - 将开发构建分发给所有团队成员，确保原生环境一致。  
  - 定期更新开发构建，同步最新的原生依赖。

### 6.4 注意事项

- **常见错误**  
  - 在开发构建中修改了原生文件后未重新构建，导致新功能不生效。  
  - 忘记在运行 `expo start` 时加 `--dev-client` 参数，仍然尝试连接 Expo Go。

- **性能问题**  
  - 首次构建开发构建耗时较长，但后续只需增量更新。  
  - 开发构建包含调试工具，性能略低于生产包。

- **兼容性**  
  - 需要确保所有原生库版本与 React Native 和 Expo SDK 兼容。  
  - 开发构建依赖于 EAS 或本地构建环境，需要相应配置。

### 6.5 代码示例

```bash
# 配置 eas.json 添加 development profile
# eas.json
{
  "build": {
    "development": {
      "developmentClient": true,
      "distribution": "internal"
    }
  }
}

# 构建开发版本
eas build --platform ios --profile development
```

```jsx
// 在代码中检测是否为开发构建
import { Constants } from 'expo-constants';

if (Constants.appOwnership === 'guest') {
  // Expo Go
} else if (Constants.appOwnership === 'standalone') {
  // 生产包
} else {
  // 开发构建 (appOwnership 可能为 null 或 'expo')
}
```

---

## 七、开发模式选择指南

### 7.1 概念

- **是什么**  
  开发模式选择指南帮助开发者根据项目需求、团队技能和阶段目标，在 Expo Go、Expo 开发构建和 React Native CLI 之间做出合理选择。

- **为什么需要**  
  不同的模式各有优劣，错误的选择可能导致开发效率低下或无法满足项目需求。明确的指南可以避免走弯路。

- **解决什么问题**  
  解决了初学者和经验开发者面对多种开发方式时的困惑，提供了一个清晰的决策路径。

### 7.2 原理

- **底层实现**  
  基于不同模式的特性（配置难度、灵活性、原生支持程度）构建决策树，引导用户根据自身情况选择最佳起点。

- **工作流程**  
  1. 评估项目是否需要自定义原生代码。  
  2. 评估团队是否具备原生开发能力。  
  3. 评估是否需要快速原型 vs 长期维护。  
  4. 根据评估结果选择对应模式。

- **关键技术点**  
  - **灵活性 vs 易用性权衡**：Expo Go 最易用但最受限，CLI 最灵活但最复杂，开发构建居中。

### 7.3 使用方法

- **基本用法**  
  - **初学者/仅需预览**：使用 `create-expo-app` + Expo Go。  
  - **生产级应用，需原生扩展**：使用 `create-expo-app` + 开发构建（首选）或 CLI。  
  - **集成现有原生应用/极致控制**：使用 React Native CLI。

- **常见场景**  
  - **学生作业/黑客松**：Expo Go 足够。  
  - **商业应用 MVP**：先使用 Expo Go 快速验证，后期切换开发构建。  
  - **银行类应用（需安全模块）**：CLI 更合适。

- **最佳实践**  
  - 从 Expo 开始，当遇到限制时再转向开发构建，而不是一开始就选 CLI。  
  - 使用 EAS 管理构建和更新，统一团队开发环境。

### 7.4 注意事项

- **常见错误**  
  - 一开始就使用 CLI，导致配置复杂、开发缓慢。  
  - 在 Expo Go 中开发了很长时间，最后发现无法集成必需的原生库，被迫重构。

- **性能问题**  
  - 开发构建和 CLI 都能实现最佳性能，Expo Go 则受限于通用包。

- **兼容性**  
  - 选择模式前需确认所有计划使用的第三方库是否支持所选模式。

### 7.5 代码示例

```bash
# 决策流程伪代码
if (needCustomNativeCode) {
  if (haveNativeTeam) {
    use CLI;
  } else {
    use Expo + DevBuild;
  }
} else {
  use Expo + Expo Go;
}
```

---

## 八、调试技巧与最佳实践

### 8.1 概念

- **是什么**  
  调试技巧与最佳实践是一系列帮助开发者高效定位问题、优化性能的方法和工具。

- **为什么需要**  
  移动端调试比 Web 复杂，涉及 JavaScript、原生代码和桥接通信，掌握正确方法能大幅提升开发效率。

- **解决什么问题**  
  解决了开发过程中难以定位 bug、性能瓶颈难以分析的问题。

### 8.2 原理

- **底层实现**  
  - **开发者菜单**：注入调试入口，提供 Reload、Debug JS Remotely、元素检查等功能。  
  - **Chrome 调试器**：通过 WebSocket 连接，可在 Chrome 开发者工具中打断点、查看网络请求。  
  - **Flipper**：Facebook 开源的移动应用调试平台，集成日志、网络、数据库查看等功能。

- **工作流程**  
  1. 调出开发者菜单，选择“Debug JS Remotely”。  
  2. Chrome 自动打开 `http://localhost:8081/debugger-ui`。  
  3. 在 Chrome 开发者工具中设置断点、查看 console。  
  4. 使用 Flipper 分析网络请求或布局。

- **关键技术点**  
  - **热重载**：修改代码后自动刷新页面。  
  - **元素检查器**：查看 UI 层级和样式。  
  - **性能监视器**：显示 FPS、内存使用等。

### 8.3 使用方法

- **基本用法**  
  - **调出开发者菜单**：模拟器快捷键（iOS `Cmd+D`，Android `Cmd+M`）或真机摇动。  
  - **启用 Chrome 调试**：点击“Debug JS Remotely”。  
  - **使用 Flipper**：安装 Flipper 桌面应用，运行 `npx react-native start` 后自动连接。

- **常见场景**  
  - 断点调试复杂逻辑。  
  - 检查 UI 布局是否正确。  
  - 分析网络请求失败原因。  
  - 监控列表滚动性能。

- **最佳实践**  
  - 尽量使用真机调试，模拟器可能掩盖某些性能问题。  
  - 使用 React DevTools 分析组件重渲染。  
  - 利用 `console.log` 谨慎，生产模式会自动移除。

### 8.4 注意事项

- **常见错误**  
  - 调试时开启“Debug JS Remotely”导致应用变慢，影响性能分析。  
  - 忘记关闭调试就打包生产包，导致包体积增大（实际不会，生产包不包含调试代码）。

- **性能问题**  
  - 调试模式会降低运行速度，性能测试应在生产模式下进行。  
  - 频繁使用 `console.log` 可能阻塞 JavaScript 线程。

- **兼容性**  
  - Flipper 可能需要特定版本 React Native 支持。  
  - Chrome 调试器在 Hermes 引擎下可能需使用 `react-native-debugger` 等替代工具。

### 8.5 代码示例

```jsx
// 使用 console.log 调试
const fetchData = async () => {
  console.log('Fetching data...');
  try {
    const response = await fetch('https://api.example.com/data');
    const json = await response.json();
    console.log('Data received:', json);
  } catch (error) {
    console.error('Fetch error:', error);
  }
};
```

```bash
# 使用 React DevTools 独立调试
npx react-devtools
```

---

本笔记涵盖了 React Native 开发的核心模式与工具，从基础概念到高级调试，每个知识点均按模板详细展开，力求完整、准确。实际开发中请结合官方文档和项目需求灵活应用。