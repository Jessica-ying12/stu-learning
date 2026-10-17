## 一、核心依赖安装

### #### Node.js 安装
##### 概念
- **是什么**：Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行时环境，用于在服务器端执行 JavaScript 代码。在 React Native 开发中，Node.js 是整个工具链的基础。
- **为什么需要**：React Native 的开发工具（如 Metro 打包器、npm/yarn）都依赖 Node.js 运行。它提供了包管理能力，并能启动开发服务器实时编译和推送代码。
- **解决什么问题**：解决了 JavaScript 代码在开发环境中的执行和依赖管理问题，使得我们可以使用 npm 或 yarn 安装和管理项目依赖。

##### 原理
- **底层实现**：Node.js 基于 V8 引擎，采用事件驱动、非阻塞 I/O 模型，使其轻量且高效。
- **工作流程**：在 React Native 开发中，当运行 `npm start` 或 `expo start` 时，Node.js 会启动一个 Metro 服务器，监听文件变化，将 JavaScript 代码打包成 bundle 并推送到设备。
- **关键技术点**：npm（Node 包管理器）用于安装依赖；npx 用于临时执行包命令；CommonJS 模块系统。

##### 使用方法
- **基本用法**：访问 Node.js 官网下载 LTS 版本安装包，按提示安装。安装完成后，在终端运行 `node --version` 和 `npm --version` 验证。
- **常见场景**：全局安装工具（如 `npm install -g expo-cli`）、本地安装项目依赖（`npm install <package>`）、运行脚本（`npm run start`）。
- **最佳实践**：使用 Node 版本管理工具（如 nvm、nvm-windows）管理多个 Node 版本，并在项目根目录创建 `.nvmrc` 文件锁定版本，确保团队开发环境一致。

##### 注意事项
- **常见错误**：Node 版本过高或过低可能导致依赖安装失败或构建错误，务必选择官方推荐的 LTS 版本。
- **性能问题**：Node 本身性能稳定，但过多的全局包可能污染环境，建议尽量使用 npx 执行命令。
- **兼容性**：React Native 对 Node 版本有最低要求（例如 React Native 0.81 需要 Node 20.19.4+），升级 Node 前需确认兼容性。

##### 代码示例
```bash
# 安装 Node.js LTS 后验证
node --version
npm --version

# 使用 nvm 安装指定版本
nvm install 20.19.4
nvm use 20.19.4
```

---

### #### Watchman 安装
##### 概念
- **是什么**：Watchman 是 Facebook 开源的文件监控服务，用于监视文件变化并触发相应操作。
- **为什么需要**：在 React Native 开发中，Watchman 能高效地监听项目文件的变化（如保存代码），并通知 Metro 打包器重新构建和刷新应用，提升开发效率。
- **解决什么问题**：解决了传统文件轮询方式性能低下的问题，提供更稳定、快速的文件变化检测，尤其在大型项目中效果显著。

##### 原理
- **底层实现**：Watchman 使用操作系统提供的文件系统事件接口（如 inotify、FSEvents）实现实时监控，并维护一个文件状态数据库。
- **工作流程**：启动 Watchman 服务后，它会递归监控指定目录，当文件被修改、添加或删除时，触发回调或通知订阅者（如 Metro）。
- **关键技术点**：事件驱动、订阅机制、高效的状态同步。

##### 使用方法
- **基本用法**：
  - **macOS**：`brew install watchman`
  - **Windows**：下载安装包或使用 Chocolatey：`choco install watchman`
  - 安装后无需额外配置，Metro 会自动使用它。
- **常见场景**：与 Metro 结合使用，实现热重载（Fast Refresh）；也可用于其他需要文件监控的开发工具。
- **最佳实践**：确保 Watchman 版本与操作系统兼容，定期更新以获取性能改进和 bug 修复。

##### 注意事项
- **常见错误**：如果 Watchman 未安装或版本过旧，Metro 可能退回到轮询模式，导致文件变化响应延迟或 CPU 占用过高。
- **性能问题**：Watchman 本身资源占用极低，但若监控目录过多或包含大量文件（如 `node_modules`），可配置忽略规则（Watchman 默认忽略 `.git` 和 `node_modules`）。
- **兼容性**：在 Linux 系统上可能需要调整系统文件监控限制（`fs.inotify.max_user_watches`）。

##### 代码示例
```bash
# 安装 Watchman (macOS)
brew update
brew install watchman

# 验证安装
watchman --version

# 查看监控状态
watchman watch-list
```

---

### #### Java Development Kit (JDK) 安装
##### 概念
- **是什么**：JDK 是 Java 开发工具包，包含 Java 运行时环境（JRE）、编译器和工具，用于开发和运行 Java 程序。
- **为什么需要**：Android 应用的原生部分使用 Java 或 Kotlin 编写，React Native 在构建 Android 版本时需要 JDK 来编译 Java 代码，并运行 Gradle 构建工具。
- **解决什么问题**：为 Android 构建提供必要的编译环境和工具链。

##### 原理
- **底层实现**：JDK 提供 `javac` 编译器将 Java 源码编译成字节码（.class 文件），然后通过 `dx` 或 `d8` 工具转换为 Dalvik 字节码（.dex 文件），最终打包为 APK。
- **工作流程**：当运行 `npm run android` 时，Gradle 会调用 JDK 中的 `javac` 编译项目中的 Java 代码（包括 React Native 原生模块），并与依赖库一起打包。
- **关键技术点**：`JAVA_HOME` 环境变量、Gradle 构建工具、字节码转换。

##### 使用方法
- **基本用法**：下载 Azul Zulu JDK 17 安装包（或使用包管理器），安装后配置 `JAVA_HOME` 环境变量并将 `$JAVA_HOME/bin` 添加到 `PATH`。
- **常见场景**：本地运行 Android 应用、构建发布包（APK/AAB）、调试原生代码。
- **最佳实践**：使用官方推荐的 JDK 17 版本，避免使用过新或过旧的 JDK；通过 `sdkman` 等工具管理多个 JDK 版本。

##### 注意事项
- **常见错误**：
  - JDK 版本不匹配：React Native 0.73+ 要求 JDK 17，若使用 JDK 11 或 21 可能导致 Gradle 构建失败。
  - `JAVA_HOME` 未正确设置：系统找不到 JDK，导致构建命令报错。
- **性能问题**：JDK 版本对编译速度有影响，较新版本通常有优化，但必须考虑兼容性。
- **兼容性**：Android Gradle 插件（AGP）与 JDK 版本有对应关系，需参考官方文档。

##### 代码示例
```bash
# 安装 JDK 17 (macOS 使用 Homebrew)
brew tap homebrew/cask-versions
brew install --cask zulu@17

# 配置环境变量（以 zsh 为例）
echo 'export JAVA_HOME=/Library/Java/JavaVirtualMachines/zulu-17.jdk/Contents/Home' >> ~/.zshrc
echo 'export PATH=$JAVA_HOME/bin:$PATH' >> ~/.zshrc
source ~/.zshrc

# 验证
java -version
javac -version
```

---

## 二、开发工具链配置

### #### Android 开发环境
##### 概念
- **是什么**：Android 开发环境包括 Android Studio（IDE）、Android SDK（软件开发工具包）、模拟器（AVD）及相关工具，用于构建和调试 Android 应用。
- **为什么需要**：React Native 项目中的 Android 部分是原生代码，必须通过 Android 工具链编译打包成 APK 才能在设备上运行。
- **解决什么问题**：提供了完整的 Android 应用构建、调试和模拟运行的能力。

##### 原理
- **底层实现**：
  - **Android Studio**：基于 IntelliJ IDEA，集成了 SDK 管理、AVD 管理、代码编辑、调试等功能。
  - **Android SDK**：包含不同 Android 版本的 API 库、构建工具、平台工具（如 adb）等。
  - **Gradle**：自动化构建系统，读取 `build.gradle` 配置，依赖管理，编译打包。
- **工作流程**：在 React Native 中运行 `npm run android` 时，会调用 Gradle 执行 `assembleDebug` 任务，编译 Java/Kotlin 代码，合并资源，生成 APK 并安装到设备。
- **关键技术点**：`ANDROID_HOME` 环境变量、adb 调试桥、模拟器加速（HAXM 或 Hypervisor.Framework）。

##### 使用方法
- **基本用法**：
  1. 下载并安装 Android Studio，安装时勾选 `Android SDK`、`Android SDK Platform`、`Android Virtual Device`。
  2. 通过 SDK Manager 安装指定版本的 SDK Platform（如 Android 15 - API 35）和构建工具。
  3. 配置 `ANDROID_HOME` 环境变量，并将 `platform-tools` 和 `emulator` 添加到 `PATH`。
  4. 创建 AVD 模拟器，选择系统镜像并启动。
- **常见场景**：调试 Android 应用、测试不同屏幕尺寸和 Android 版本、打包发布。
- **最佳实践**：
  - 使用稳定版 Android Studio，保持 SDK 和构建工具更新。
  - 为模拟器分配足够的内存和存储空间，启用硬件加速以获得流畅体验。
  - 使用 `adb reverse` 解决真机调试连接问题。

##### 注意事项
- **常见错误**：
  - `ANDROID_HOME` 未设置或路径错误，导致 `adb` 命令找不到。
  - 未安装对应 API 级别的 SDK Platform，构建时提示缺少 `android.jar`。
  - 模拟器启动失败，可能是因为未开启虚拟化技术（VT-x/AMD-V）或未安装 HAXM。
- **性能问题**：模拟器性能取决于电脑配置，建议使用真机调试以获取更真实性能表现。
- **兼容性**：React Native 版本与 Android 版本有对应关系，例如 React Native 0.73 要求 compileSdkVersion 为 34。

##### 代码示例
```bash
# 配置环境变量 (macOS)
export ANDROID_HOME=$HOME/Library/Android/sdk
export PATH=$PATH:$ANDROID_HOME/emulator
export PATH=$PATH:$ANDROID_HOME/platform-tools
export PATH=$PATH:$ANDROID_HOME/tools
export PATH=$PATH:$ANDROID_HOME/tools/bin

# 验证 adb
adb --version

# 列出已连接的设备
adb devices

# 端口转发（解决真机连接问题）
adb reverse tcp:8081 tcp:8081
```

---

### #### iOS 开发环境
##### 概念
- **是什么**：iOS 开发环境包括 Xcode（IDE）、iOS SDK、模拟器和 CocoaPods（依赖管理工具），用于构建和调试 iOS 应用。
- **为什么需要**：React Native 项目中的 iOS 部分需要编译成原生 iOS 应用，依赖 Xcode 工具链。
- **解决什么问题**：为 macOS 用户提供 iOS 应用的编译、调试和模拟运行能力。

##### 原理
- **底层实现**：
  - **Xcode**：苹果官方 IDE，包含编译器（clang）、调试器（lldb）、iOS SDK 和模拟器。
  - **CocoaPods**：Ruby 编写的依赖管理工具，用于集成第三方原生库。
- **工作流程**：运行 `npm run ios` 时，React Native CLI 会调用 Xcode 命令行工具，编译项目并启动模拟器。如果项目使用 CocoaPods，需先执行 `pod install` 安装依赖。
- **关键技术点**：Xcode 版本匹配、模拟器管理、证书签名。

##### 使用方法
- **基本用法**：
  1. 从 Mac App Store 安装 Xcode。
  2. 打开 Xcode，进入 `Preferences > Locations`，设置 Command Line Tools 为当前 Xcode 版本。
  3. 安装 CocoaPods：`sudo gem install cocoapods`。
  4. 在 React Native 项目 iOS 目录下运行 `pod install` 安装依赖（初次运行或修改 Podfile 后需要）。
- **常见场景**：开发 iOS 应用、调试 UI 和原生模块、打包 TestFlight 或 App Store 版本。
- **最佳实践**：
  - 保持 Xcode 更新到最新稳定版，避免使用 beta 版。
  - 使用 `.xcodeproj` 或 `.xcworkspace` 打开项目，后者用于包含 Pods 的项目。
  - 利用模拟器不同型号测试布局适配。

##### 注意事项
- **常见错误**：
  - Xcode 版本过低，不支持当前 React Native 的最低 iOS 版本。
  - CocoaPods 安装失败，可能是 Ruby 版本问题或网络原因，可尝试更换 gem 源。
  - 模拟器启动失败，可能是资源不足或模拟器固件损坏。
- **性能问题**：模拟器性能较好，但某些功能（如传感器、推送通知）需要真机测试。
- **兼容性**：仅限 macOS 系统；React Native 版本与 Xcode 版本有隐含依赖，通常建议使用最新 Xcode。

##### 代码示例
```bash
# 安装 CocoaPods
sudo gem install cocoapods

# 在项目 iOS 目录安装 Pods
cd ios
pod install

# 运行 iOS 应用（指定模拟器）
npm run ios -- --simulator="iPhone 14 Pro"

# 清理 Xcode 构建缓存
rm -rf ~/Library/Developer/Xcode/DerivedData
```

---

## 三、项目创建与管理工具

### #### 安装 Expo CLI
##### 概念
- **是什么**：Expo CLI 是 Expo 工具链的命令行接口，用于创建、运行和发布 Expo 管理的 React Native 项目。
- **为什么需要**：Expo 提供了一套托管工作流，封装了复杂的原生配置，使开发者无需安装 Android Studio 和 Xcode 即可快速开始 React Native 开发。
- **解决什么问题**：降低了环境搭建门槛，提供了快速预览（Expo Go）、热重载、丰富的 API 等能力。

##### 原理
- **底层实现**：Expo CLI 基于 Node.js，集成了 Metro 打包器、Expo 开发服务器和客户端 SDK。它通过 Expo Go 应用在真机上运行 JavaScript 代码，无需编译原生部分。
- **工作流程**：运行 `expo start` 启动开发服务器，生成二维码；Expo Go 扫描二维码后下载 bundle 并在手机沙盒中运行。
- **关键技术点**：Expo SDK、托管工作流、OTA（Over-the-Air）更新。

##### 使用方法
- **基本用法**：
  ```bash
  npm install -g expo-cli
  expo init my-app
  cd my-app
  expo start
  ```
- **常见场景**：快速原型开发、教学演示、无需原生模块的中小型项目。
- **最佳实践**：结合 EAS Build 构建独立应用，用于发布商店；使用 Expo 的配置文件 `app.json` 自定义应用图标、名称等。

##### 注意事项
- **常见错误**：
  - 全局安装后命令找不到，可能是 npm 全局路径未加入 `PATH`。
  - Expo Go 无法连接，确保手机和电脑在同一 Wi-Fi 网络，或使用 `tunnel` 连接方式。
- **性能问题**：托管工作流无法直接集成需要修改原生代码的第三方库，可能限制性能优化。
- **兼容性**：Expo SDK 版本与 React Native 版本绑定，升级时需同时更新。

##### 代码示例
```bash
# 安装 Expo CLI
npm install -g expo-cli

# 创建新项目（选择模板）
expo init my-new-app

# 进入项目并启动
cd my-new-app
expo start

# 清理 Expo 缓存
expo start -c
```

---

### #### 创建项目
##### 概念
- **是什么**：创建 React Native 项目的过程，即生成包含必要文件（如 `package.json`、`App.js`）的初始工程。
- **为什么需要**：为项目提供标准化的目录结构和依赖配置，便于后续开发和协作。
- **解决什么问题**：自动化搭建项目骨架，避免手动配置的繁琐和错误。

##### 原理
- **底层实现**：CLI 工具（如 `@react-native-community/cli` 或 `expo-cli`）从模板仓库克隆或生成文件，并执行 `npm install` 安装依赖。
- **工作流程**：运行 `npx react-native init` 或 `expo init` 后，CLI 下载模板，替换项目名，创建目录，安装依赖，生成原生项目文件夹（React Native CLI 方式）。
- **关键技术点**：模板机制、依赖解析、原生项目生成。

##### 使用方法
- **基本用法**：
  - **React Native CLI**：`npx @react-native-community/cli@latest init MyApp`
  - **Expo CLI**：`npx create-expo-app MyApp`（或 `expo init MyApp`）
- **常见场景**：开始一个新项目，选择适合的模板（如空白模板、标签页模板）。
- **最佳实践**：
  - 使用 TypeScript 模板以获得类型安全：`npx react-native init MyApp --template react-native-template-typescript`
  - 初始化后进入目录运行项目，确保环境正确。

##### 注意事项
- **常见错误**：
  - 全局安装过旧的 `react-native-cli` 导致冲突，应先卸载：`npm uninstall -g react-native-cli`。
  - 网络问题导致模板下载失败，可配置 npm 镜像或使用代理。
- **性能问题**：初次创建项目可能需要较长时间下载依赖，保持网络畅通。
- **兼容性**：CLI 版本与 React Native 版本匹配，推荐使用 `@latest` 确保最新稳定版。

##### 代码示例
```bash
# React Native CLI 创建项目
npx @react-native-community/cli@latest init AwesomeProject

# Expo CLI 创建项目
npx create-expo-app AwesomeProject

# 进入项目目录
cd AwesomeProject
```

---

### #### 项目结构
##### 概念
- **是什么**：React Native 项目的文件和文件夹组织方式。
- **为什么需要**：理解项目结构有助于快速定位代码、配置文件，以及理解 React Native 的工作原理。
- **解决什么问题**：为开发、构建、调试提供标准化的文件布局。

##### 原理
- **底层实现**：
  - **React Native CLI 项目**：
    - `/android`：Android 原生项目，可由 Android Studio 打开。
    - `/ios`：iOS 原生项目，可由 Xcode 打开。
    - `/node_modules`：存放所有依赖包。
    - `App.js`：根组件，应用入口。
    - `index.js`：注册根组件到原生平台。
    - `package.json`：项目依赖和脚本。
    - `metro.config.js`：Metro 打包器配置。
  - **Expo 项目**：无 `/android` 和 `/ios` 文件夹，原生逻辑由 Expo SDK 管理。
- **工作流程**：Metro 从 `index.js` 开始打包，解析 `App.js` 及其依赖，生成 bundle。
- **关键技术点**：入口文件、原生桥接、配置文件。

##### 使用方法
- **基本用法**：在 `App.js` 中编写组件，在 `app.json` 中配置应用名、图标等，在 `package.json` 中添加脚本。
- **常见场景**：添加新屏幕、安装第三方库、修改应用图标和启动画面。
- **最佳实践**：
  - 保持 `App.js` 简洁，将组件拆分到 `src` 目录。
  - 使用绝对路径导入（配置 `babel-plugin-module-resolver`）。
  - 将原生模块代码放在 `/android` 和 `/ios` 相应位置。

##### 注意事项
- **常见错误**：
  - 误删或修改原生项目文件导致构建失败。
  - 在 Expo 项目中尝试修改原生文件，但项目为托管模式，无法直接修改。
- **性能问题**：`node_modules` 体积较大，不参与版本控制（通过 `.gitignore` 排除）。
- **兼容性**：不同 CLI 版本生成的结构可能略有差异，需查阅对应文档。

##### 代码示例
```
AwesomeProject/
├── android/               # Android 原生代码
├── ios/                   # iOS 原生代码
├── node_modules/          # 依赖包
├── .gitignore             # Git 忽略配置
├── App.js                 # 根组件
├── app.json               # 项目配置（Expo 项目特有）
├── index.js               # 入口文件
├── package.json           # 依赖和脚本
├── metro.config.js        # Metro 配置
└── README.md              # 项目说明
```

---

## 四、项目运行与调试

### #### 运行项目
##### 概念
- **是什么**：在开发环境中启动 React Native 应用，使其在模拟器或真机上运行并支持热重载。
- **为什么需要**：实时查看代码效果，调试功能，验证修改。
- **解决什么问题**：提供了高效的开发反馈循环。

##### 原理
- **底层实现**：
  - **Metro 打包器**：启动一个 HTTP 服务器，监听文件变化，将 JavaScript 代码打包成 bundle 并提供给设备。
  - **原生应用**：启动时会连接 Metro 服务器下载 bundle，然后执行。
  - **热重载**：Watchman 检测到文件变化后，Metro 重新打包并通知应用刷新。
- **工作流程**：
  1. 运行 `npm start` 启动 Metro。
  2. 在另一个终端运行 `npm run android` 或 `npm run ios`，编译原生代码并安装应用。
  3. 应用启动后连接 Metro，加载 bundle。
- **关键技术点**：开发服务器、设备连接、热更新。

##### 使用方法
- **基本用法**：
  - 启动 Metro：`npm start`
  - 运行 Android：`npm run android`（需先启动模拟器或连接真机）
  - 运行 iOS：`npm run ios`
- **常见场景**：同时打开两个终端，一个运行 Metro，另一个运行平台命令；也可以直接 `npm run android` 自动启动 Metro（但不会自动重启）。
- **最佳实践**：
  - 使用 `--reset-cache` 参数清除缓存：`npm start -- --reset-cache`
  - 在 VS Code 中集成终端，方便同时查看 Metro 输出和运行命令。

##### 注意事项
- **常见错误**：
  - Metro 端口（8081）被占用，可关闭其他应用或修改端口。
  - 运行 Android 时 Gradle 构建失败，检查网络和 JDK 配置。
  - iOS 模拟器启动失败，检查 Xcode 路径和命令行工具设置。
- **性能问题**：首次运行需要较长时间编译原生代码，后续增量编译会快很多。
- **兼容性**：确保设备（模拟器/真机）与电脑在同一网络或通过 USB 连接。

##### 代码示例
```bash
# 启动 Metro
npm start

# 运行 Android 应用
npm run android

# 运行 iOS 应用
npm run ios

# 指定 iOS 模拟器型号
npm run ios -- --simulator="iPhone 14"

# 清除缓存启动 Metro
npx react-native start --reset-cache
```

---

### #### 真机预览
##### 概念
- **是什么**：在真实的物理设备上运行和调试 React Native 应用。
- **为什么需要**：真机能提供更真实的性能、传感器、推送通知等测试环境，是开发不可或缺的一环。
- **解决什么问题**：模拟器无法完全模拟真实设备的行为，真机测试能发现模拟器难以复现的问题。

##### 原理
- **底层实现**：
  - **Android**：通过 USB 连接，启用开发者模式和 USB 调试，使用 `adb` 安装 APK 并转发端口。
  - **iOS**：通过 USB 或 Wi-Fi 连接，需要开发者账号和签名证书。
  - **Expo Go**：通过局域网扫描二维码，加载 JavaScript bundle 运行在 Expo 客户端中。
- **工作流程**：
  - 原生方式：构建 APK/IPA 安装到设备，设备通过 USB 或 Wi-Fi 连接 Metro 获取更新。
  - Expo Go：设备运行 Expo Go，扫描开发服务器二维码，下载 bundle 并运行。
- **关键技术点**：USB 调试、端口转发、签名证书。

##### 使用方法
- **基本用法**：
  - **Expo Go**：安装 Expo Go，手机连接同一 Wi-Fi，扫描 `expo start` 输出的二维码。
  - **Android 真机**：
    1. 开启开发者选项和 USB 调试。
    2. 连接 USB，运行 `adb devices` 确认设备识别。
    3. 运行 `npm run android`，应用会自动安装并启动。
    4. 如需 Wi-Fi 调试，可断开 USB 后使用 `adb connect`。
  - **iOS 真机**：
    1. 需要 Apple 开发者账号（免费或付费），在 Xcode 中配置签名。
    2. 连接设备，在 Xcode 中选择设备运行，或使用 `npm run ios -- --device`。
- **常见场景**：测试性能、相机、地理位置等硬件功能，以及发布前最终验证。
- **最佳实践**：
  - 使用 `adb reverse` 解决 Android 真机连接问题：`adb reverse tcp:8081 tcp:8081`
  - iOS 真机调试建议使用 USB 连接，避免 Wi-Fi 不稳定。

##### 注意事项
- **常见错误**：
  - Android 真机无法识别：未安装设备驱动或未开启 USB 调试。
  - iOS 真机无法运行：证书签名错误或未信任开发者。
  - Expo Go 无法连接：检查 Wi-Fi 网络，可尝试使用 `tunnel` 模式（`expo start --tunnel`）。
- **性能问题**：真机运行更接近用户实际体验，但调试版本可能因开发模式而有性能损耗。
- **兼容性**：不同 Android 厂商的 USB 驱动可能不同，需单独安装。

##### 代码示例
```bash
# 检查 Android 设备连接
adb devices

# 端口转发
adb reverse tcp:8081 tcp:8081

# 运行 iOS 到真机（需 Xcode 配置签名）
npm run ios -- --device

# Expo 隧道模式（适用于网络复杂环境）
expo start --tunnel
```

---

### #### 模拟器配置
##### 概念
- **是什么**：模拟器是运行在电脑上的虚拟设备，用于模拟真实手机的环境。
- **为什么需要**：方便快速测试不同屏幕尺寸、系统版本，无需多台真机。
- **解决什么问题**：降低测试成本，提高开发效率。

##### 原理
- **底层实现**：
  - **Android 模拟器**：基于 QEMU 虚拟化，通过硬件加速（Intel HAXM、Hypervisor.Framework）提升性能。
  - **iOS 模拟器**：基于 macOS 的 CoreSimulator 框架，模拟 iOS 系统环境。
- **工作流程**：通过 AVD Manager（Android）或 Xcode（iOS）创建和管理模拟器实例，运行应用时自动选择目标模拟器。
- **关键技术点**：硬件加速、系统镜像、模拟器配置。

##### 使用方法
- **基本用法**：
  - **Android**：
    1. 在 Android Studio 中打开 AVD Manager。
    2. 创建新虚拟设备，选择设备型号和系统镜像（建议下载带有 Google APIs 的镜像）。
    3. 设置 AVD 属性（内存、存储等），点击启动。
  - **iOS**：
    1. 在 Xcode 中打开 `Devices and Simulators` 面板。
    2. 点击 `+` 添加新模拟器，选择设备类型和 iOS 版本。
    3. 运行 `npm run ios` 时自动启动默认模拟器，或通过 `--simulator` 参数指定。
- **常见场景**：测试不同屏幕尺寸（如 iPhone SE 和 iPhone 14 Pro Max）、不同 Android 版本兼容性。
- **最佳实践**：
  - 为 Android 模拟器分配足够 RAM（2GB+）和存储空间，启用 `Store snapshot for faster startup`。
  - 使用冷启动和热启动结合，减少等待时间。
  - 为 Apple Silicon Mac 选择 ARM 架构的系统镜像以获得原生性能。

##### 注意事项
- **常见错误**：
  - Android 模拟器无法启动：BIOS 中未启用虚拟化技术（VT-x/AMD-V），或未安装 HAXM。
  - iOS 模拟器启动卡死：可尝试重置模拟器内容（`Erase All Content and Settings`）。
- **性能问题**：模拟器性能受主机 CPU 和内存影响，关闭不必要的后台应用可改善。
- **兼容性**：某些硬件特性（如蓝牙、NFC）模拟器不支持，需用真机测试。

##### 代码示例
```bash
# 列出所有可用 Android 模拟器
emulator -list-avds

# 启动指定 Android 模拟器
emulator -avd Pixel_6_API_35

# 列出所有 iOS 模拟器设备列表
xcrun simctl list devices

# 启动指定 iOS 模拟器
open -a Simulator --args -CurrentDeviceUDID <UDID>
```

---

### #### 清理缓存
##### 概念
- **是什么**：清除开发过程中产生的临时文件和缓存，以解决奇怪的问题或释放空间。
- **为什么需要**：Metro 缓存、Gradle 缓存、npm 缓存等可能因损坏或过时而导致构建失败、热重载失效等问题。
- **解决什么问题**：恢复开发环境的稳定性，消除因缓存引起的异常行为。

##### 原理
- **底层实现**：
  - **Metro 缓存**：存储在 `node_modules/.cache/metro`，包含打包的中间文件。
  - **Gradle 缓存**：位于 `~/.gradle/caches`，包含编译过的依赖和任务输出。
  - **npm 缓存**：位于 `~/.npm`，存储已下载的包。
- **工作流程**：通过 CLI 命令删除相应缓存目录，或使用工具提供的重置选项。
- **关键技术点**：缓存机制、构建工具缓存、依赖缓存。

##### 使用方法
- **基本用法**：
  - **清理 Metro 缓存**：
    - Expo：`expo start -c`
    - React Native CLI：`npx react-native start --reset-cache`
  - **清理 Gradle 缓存**：进入 `android` 目录，运行 `./gradlew clean`（仅清理项目构建文件）或手动删除 `~/.gradle/caches`。
  - **清理 npm 依赖并重装**：
    ```bash
    rm -rf node_modules package-lock.json
    npm install
    ```
  - **iOS 清理**：删除 `ios/Pods` 和 `ios/Podfile.lock`，重新运行 `pod install`。
- **常见场景**：代码修改后界面不更新、构建时报莫名其妙的错误、升级依赖后项目无法运行。
- **最佳实践**：
  - 优先尝试清理 Metro 缓存，因为它最轻量。
  - 如果问题持续，再考虑清理 Gradle 或重装 node_modules。
  - 使用脚本组合清理命令，如 `npm run clean`。

##### 注意事项
- **常见错误**：清理后首次重新构建可能较慢，需耐心等待。
- **性能问题**：频繁清理会失去缓存带来的加速效果，应在必要时才执行。
- **兼容性**：不同平台和工具的缓存位置可能不同，需根据实际情况操作。

##### 代码示例
```bash
# 清理 Metro 缓存 (React Native CLI)
npx react-native start --reset-cache

# 清理 Expo 缓存
expo start -c

# 清理 Gradle 项目构建文件
cd android && ./gradlew clean && cd ..

# 完全清理 Android 依赖缓存
rm -rf ~/.gradle/caches/

# 重装 npm 依赖
rm -rf node_modules package-lock.json
npm install

# 清理 iOS Pods
cd ios && pod deintegrate && pod install && cd ..
```

---

以上学习笔记涵盖了 React Native 环境搭建的核心知识点，每个部分按照概念、原理、使用方法、注意事项和代码示例进行了详细阐述，确保内容完整、结构清晰。