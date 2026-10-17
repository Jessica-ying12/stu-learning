好的，我们将按照最细致的步骤，从零开始构建一个完整的 Flutter 项目 **“智能任务清单”**。每一步都会明确告诉你：**创建什么文件、写入什么代码、为什么这么写，以及这段代码如何与其他部分连接**。请按顺序一步步操作。

## 第一阶段：项目初始化与依赖添加

### 第1步：创建 Flutter 项目
打开终端，执行：
```bash
flutter create smart_todo
cd smart_todo
```
这会在当前目录下生成一个 Flutter 项目骨架。

### 第2步：添加第三方依赖
用 VS Code 或 Android Studio 打开项目，找到 `pubspec.yaml` 文件。在 `dependencies` 下添加所需库：

```yaml
dependencies:
  flutter:
    sdk: flutter
  # 状态管理
  provider: ^6.1.1
  # 本地数据库
  sqflite: ^2.3.0
  # 路径获取（配合 sqflite）
  path: ^1.9.0
  # 网络请求
  http: ^1.1.0
  # 本地键值存储
  shared_preferences: ^2.2.1
  # 日期格式化
  intl: ^0.18.1
  # 滑动删除
  flutter_slidable: ^3.0.1
```
保存文件，然后在终端运行：
```bash
flutter pub get
```
这会下载所有依赖包。

### 第3步：规划项目目录结构
在 `lib` 文件夹下创建以下子目录（可以在终端执行命令，也可以手动创建）：
```bash
mkdir lib/models lib/services lib/providers lib/screens lib/widgets lib/utils
```
现在你的 `lib` 目录结构应该是：
```
lib/
├── models/
├── services/
├── providers/
├── screens/
├── widgets/
├── utils/
└── main.dart
```

---

## 第二阶段：编写数据模型（Model）

### 第4步：创建任务模型类
**创建文件**：`lib/models/task.dart`

**写入代码**：
```dart
class Task {
  int? id;          // 数据库主键，插入时自动生成，可为空
  String title;     // 任务标题
  String? description; // 描述，可选
  bool isCompleted; // 是否完成
  DateTime createdAt; // 创建时间

  Task({
    this.id,
    required this.title,
    this.description,
    this.isCompleted = false,
    required this.createdAt,
  });

  // 将 Task 对象转换为 Map（用于数据库插入/更新）
  Map<String, dynamic> toMap() {
    return {
      'id': id,
      'title': title,
      'description': description,
      'isCompleted': isCompleted ? 1 : 0, // SQLite 用 0/1 表示布尔
      'createdAt': createdAt.toIso8601String(),
    };
  }

  // 从 Map 创建 Task 对象（数据库查询结果转模型）
  factory Task.fromMap(Map<String, dynamic> map) {
    return Task(
      id: map['id'],
      title: map['title'],
      description: map['description'],
      isCompleted: map['isCompleted'] == 1,
      createdAt: DateTime.parse(map['createdAt']),
    );
  }
}
```
**代码解释**：
- `id` 是数据库自动生成的主键，所以在构造函数中是可选的。
- `toMap()` 方法将对象转换成键值对，供数据库插入或更新。
- `fromMap` 工厂方法从数据库返回的 Map 中解析出 Task 对象。

**连通性**：这个类会被数据库服务使用，也会在 Provider 中作为数据类型。

---

## 第三阶段：编写服务层（Service）

### 第5步：创建数据库服务
**创建文件**：`lib/services/database_helper.dart`

**写入代码**：
```dart
import 'package:sqflite/sqflite.dart';
import 'package:path/path.dart';
import '../models/task.dart';

class DatabaseHelper {
  // 单例模式
  static final DatabaseHelper _instance = DatabaseHelper._internal();
  factory DatabaseHelper() => _instance;
  DatabaseHelper._internal();

  static Database? _database;

  // 获取数据库实例（如果已打开则返回，否则初始化）
  Future<Database> get database async {
    if (_database != null) return _database!;
    _database = await _initDatabase();
    return _database!;
  }

  // 初始化数据库：打开或创建数据库，并创建表
  Future<Database> _initDatabase() async {
    String path = join(await getDatabasesPath(), 'tasks.db');
    return await openDatabase(
      path,
      version: 1,
      onCreate: (db, version) async {
        // 创建任务表
        await db.execute('''
          CREATE TABLE tasks(
            id INTEGER PRIMARY KEY AUTOINCREMENT,
            title TEXT NOT NULL,
            description TEXT,
            isCompleted INTEGER NOT NULL DEFAULT 0,
            createdAt TEXT NOT NULL
          )
        ''');
      },
    );
  }

  // 插入任务
  Future<int> insertTask(Task task) async {
    Database db = await database;
    return await db.insert('tasks', task.toMap());
  }

  // 查询所有任务（按创建时间倒序）
  Future<List<Task>> getTasks() async {
    Database db = await database;
    final List<Map<String, dynamic>> maps = await db.query(
      'tasks',
      orderBy: 'createdAt DESC',
    );
    return List.generate(maps.length, (i) {
      return Task.fromMap(maps[i]);
    });
  }

  // 更新任务
  Future<int> updateTask(Task task) async {
    Database db = await database;
    return await db.update(
      'tasks',
      task.toMap(),
      where: 'id = ?',
      whereArgs: [task.id],
    );
  }

  // 删除任务
  Future<int> deleteTask(int id) async {
    Database db = await database;
    return await db.delete(
      'tasks',
      where: 'id = ?',
      whereArgs: [id],
    );
  }
}
```
**代码解释**：
- 采用单例模式，确保整个应用只有一个数据库实例。
- `_initDatabase` 在第一次调用时执行，创建数据库和表。
- 每个 CRUD 方法都返回 `Future`，因为数据库操作是异步的。

**连通性**：这个服务会被 `TaskProvider` 调用，用于操作数据库。

### 第6步：创建网络服务（天气 API）
**创建文件**：`lib/services/weather_api_client.dart`

**写入代码**：
```dart
import 'dart:convert';
import 'package:http/http.dart' as http;

class WeatherApiClient {
  static const String _baseUrl = 'https://api.openweathermap.org/data/2.5/weather';
  final String apiKey;

  WeatherApiClient({required this.apiKey});

  // 根据城市名获取天气
  Future<Map<String, dynamic>> getWeather(String city) async {
    final url = Uri.parse('$_baseUrl?q=$city&appid=$apiKey&units=metric&lang=zh_cn');
    final response = await http.get(url);

    if (response.statusCode == 200) {
      return jsonDecode(response.body);
    } else {
      throw Exception('获取天气失败');
    }
  }
}
```
**代码解释**：
- 构造函数需要传入 API Key（你需要去 OpenWeatherMap 注册免费账号获取）。
- `getWeather` 发起网络请求，返回解析后的 JSON Map。

**连通性**：这个服务会被 `SettingsProvider` 调用，用于刷新天气数据。

### 第7步：创建本地存储服务（用户设置）
**创建文件**：`lib/services/settings_service.dart`

**写入代码**：
```dart
import 'package:shared_preferences/shared_preferences.dart';

class SettingsService {
  static const String _themeKey = 'isDarkMode';
  static const String _cityKey = 'city';

  // 保存主题模式
  Future<void> setDarkMode(bool isDark) async {
    final prefs = await SharedPreferences.getInstance();
    await prefs.setBool(_themeKey, isDark);
  }

  // 获取主题模式
  Future<bool> getDarkMode() async {
    final prefs = await SharedPreferences.getInstance();
    return prefs.getBool(_themeKey) ?? false;
  }

  // 保存城市
  Future<void> setCity(String city) async {
    final prefs = await SharedPreferences.getInstance();
    await prefs.setString(_cityKey, city);
  }

  // 获取城市
  Future<String> getCity() async {
    final prefs = await SharedPreferences.getInstance();
    return prefs.getString(_cityKey) ?? 'Beijing';
  }
}
```
**代码解释**：
- 封装了 `SharedPreferences` 的读写，分别存储主题模式和城市名称。

**连通性**：这个服务会被 `SettingsProvider` 调用，用于保存和读取用户偏好。

---

## 第四阶段：编写状态管理（Provider）

### 第8步：创建任务状态提供者
**创建文件**：`lib/providers/task_provider.dart`

**写入代码**：
```dart
import 'package:flutter/material.dart';
import '../models/task.dart';
import '../services/database_helper.dart';

class TaskProvider with ChangeNotifier {
  List<Task> _tasks = [];
  final DatabaseHelper _dbHelper = DatabaseHelper();

  List<Task> get tasks => _tasks;

  // 加载所有任务
  Future<void> loadTasks() async {
    _tasks = await _dbHelper.getTasks();
    notifyListeners(); // 通知监听器（UI）更新
  }

  // 添加任务
  Future<void> addTask(Task task) async {
    await _dbHelper.insertTask(task);
    await loadTasks(); // 重新加载列表
  }

  // 更新任务
  Future<void> updateTask(Task task) async {
    await _dbHelper.updateTask(task);
    await loadTasks();
  }

  // 删除任务
  Future<void> deleteTask(int id) async {
    await _dbHelper.deleteTask(id);
    await loadTasks();
  }
}
```
**代码解释**：
- `ChangeNotifier` 是 Flutter 提供的类，当调用 `notifyListeners()` 时，所有监听此 Provider 的组件会重建。
- 每个方法都调用数据库服务，并重新加载列表，确保数据一致。

**连通性**：这个 Provider 将被 UI 页面（如主页）使用，通过 `Provider.of` 或 `Consumer` 获取数据和方法。

### 第9步：构建设置状态提供者
**创建文件**：`lib/providers/settings_provider.dart`

**写入代码**：
```dart
import 'package:flutter/material.dart';
import '../services/settings_service.dart';
import '../services/weather_api_client.dart';

class SettingsProvider with ChangeNotifier {
  final SettingsService _settingsService = SettingsService();
  late WeatherApiClient _weatherClient;
  bool _isDarkMode = false;
  String _city = 'Beijing';
  Map<String, dynamic>? _weatherData;

  bool get isDarkMode => _isDarkMode;
  String get city => _city;
  Map<String, dynamic>? get weatherData => _weatherData;

  SettingsProvider() {
    // 注意：你需要在这里填入你的真实 API Key
    _weatherClient = WeatherApiClient(apiKey: '你的API密钥'); // TODO: 替换
    loadSettings();
  }

  // 加载保存的设置
  Future<void> loadSettings() async {
    _isDarkMode = await _settingsService.getDarkMode();
    _city = await _settingsService.getCity();
    notifyListeners();
    // 加载城市后自动获取天气
    await fetchWeather();
  }

  // 切换主题
  Future<void> toggleDarkMode(bool value) async {
    _isDarkMode = value;
    await _settingsService.setDarkMode(value);
    notifyListeners();
  }

  // 更新城市并获取天气
  Future<void> updateCity(String newCity) async {
    _city = newCity;
    await _settingsService.setCity(newCity);
    await fetchWeather();
    notifyListeners();
  }

  // 获取天气
  Future<void> fetchWeather() async {
    try {
      _weatherData = await _weatherClient.getWeather(_city);
    } catch (e) {
      _weatherData = null;
    }
    notifyListeners();
  }
}
```
**代码解释**：
- 构造函数中初始化网络客户端并加载设置。
- 所有修改状态的方法都会调用对应的服务并触发 `notifyListeners()`。

**连通性**：这个 Provider 将被 UI 页面（主页、设置页）使用，提供主题、城市和天气数据。

---

## 第五阶段：编写可复用 Widget

### 第10步：创建天气卡片组件
**创建文件**：`lib/widgets/weather_card.dart`

**写入代码**：
```dart
import 'package:flutter/material.dart';

class WeatherCard extends StatelessWidget {
  final Map<String, dynamic>? weatherData;
  final String city;

  const WeatherCard({Key? key, required this.weatherData, required this.city})
      : super(key: key);

  @override
  Widget build(BuildContext context) {
    // 如果天气数据为空，显示错误提示
    if (weatherData == null) {
      return Card(
        child: Padding(
          padding: const EdgeInsets.all(16),
          child: Text('无法获取天气数据，请检查城市或网络'),
        ),
      );
    }

    final weather = weatherData!;
    final temp = weather['main']['temp'];
    final description = weather['weather'][0]['description'];
    final iconCode = weather['weather'][0]['icon'];
    final iconUrl = 'https://openweathermap.org/img/wn/$iconCode@2x.png';

    return Card(
      child: Padding(
        padding: const EdgeInsets.all(16),
        child: Row(
          children: [
            // 天气图标
            Image.network(iconUrl, width: 50, height: 50),
            const SizedBox(width: 16),
            // 天气信息
            Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                Text('$city', style: const TextStyle(fontSize: 18, fontWeight: FontWeight.bold)),
                Text('$temp°C', style: const TextStyle(fontSize: 16)),
                Text(description),
              ],
            ),
          ],
        ),
      ),
    );
  }
}
```
**代码解释**：
- 这是一个纯展示组件，接收 `weatherData` 和 `city` 作为参数。
- 根据数据是否存在显示不同内容。

**连通性**：这个组件将在主页中被使用，数据来自 `SettingsProvider`。

---

## 第六阶段：编写页面（Screens）

### 第11步：创建启动页（Splash Screen）
**创建文件**：`lib/screens/splash_screen.dart`

**写入代码**：
```dart
import 'package:flutter/material.dart';

class SplashScreen extends StatefulWidget {
  @override
  _SplashScreenState createState() => _SplashScreenState();
}

class _SplashScreenState extends State<SplashScreen> {
  @override
  void initState() {
    super.initState();
    // 模拟加载数据，2秒后进入主页（实际加载逻辑稍后添加）
    Future.delayed(const Duration(seconds: 2), () {
      Navigator.of(context).pushReplacementNamed('/home'); // 注意路由名
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: const [
            FlutterLogo(size: 100),
            SizedBox(height: 20),
            Text('智能任务清单', style: TextStyle(fontSize: 24)),
          ],
        ),
      ),
    );
  }
}
```
**代码解释**：
- 启动页显示 Logo 和文字，2 秒后跳转到主页（我们稍后会定义路由）。

**连通性**：启动页是应用第一个页面，需要在 `main.dart` 中设置路由。

### 第12步：创建主页（Home Screen）基础框架
**创建文件**：`lib/screens/home_screen.dart`

**先写入基础框架代码**（后续步骤逐步完善）：
```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../providers/task_provider.dart';
import '../providers/settings_provider.dart';
import '../widgets/weather_card.dart';

class HomeScreen extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('任务清单'),
        actions: [
          IconButton(
            icon: const Icon(Icons.settings),
            onPressed: () {
              // 跳转到设置页（稍后实现）
              Navigator.pushNamed(context, '/settings');
            },
          ),
        ],
      ),
      body: Consumer2<TaskProvider, SettingsProvider>(
        builder: (context, taskProvider, settingsProvider, child) {
          return Column(
            children: [
              // 天气卡片
              WeatherCard(
                weatherData: settingsProvider.weatherData,
                city: settingsProvider.city,
              ),
              // 任务列表标题
              Padding(
                padding: const EdgeInsets.all(16),
                child: Row(
                  mainAxisAlignment: MainAxisAlignment.spaceBetween,
                  children: [
                    const Text(
                      '今日任务',
                      style: TextStyle(fontSize: 20, fontWeight: FontWeight.bold),
                    ),
                    Text('共 ${taskProvider.tasks.length} 项'),
                  ],
                ),
              ),
              // 任务列表区域（稍后添加）
              Expanded(
                child: ListView.builder(
                  itemCount: taskProvider.tasks.length,
                  itemBuilder: (ctx, index) {
                    final task = taskProvider.tasks[index];
                    // 临时显示任务标题
                    return ListTile(
                      title: Text(task.title),
                    );
                  },
                ),
              ),
            ],
          );
        },
      ),
      floatingActionButton: FloatingActionButton(
        child: const Icon(Icons.add),
        onPressed: () {
          // 跳转到添加任务页
          Navigator.pushNamed(context, '/add_task');
        },
      ),
    );
  }
}
```
**代码解释**：
- 使用 `Consumer2` 同时监听两个 Provider，当它们变化时重建这部分 UI。
- 天气卡片已集成，任务列表暂时只显示标题。
- 跳转使用命名路由，稍后在 `main.dart` 中定义。

### 第13步：完善主页任务列表（添加滑动删除和复选框）
在 `home_screen.dart` 中，替换 `ListView.builder` 的 `itemBuilder` 部分为完整功能：

```dart
import 'package:flutter_slidable/flutter_slidable.dart'; // 别忘了导入

// ... 在 Consumer2 的 builder 内部 ...

Expanded(
  child: ListView.builder(
    itemCount: taskProvider.tasks.length,
    itemBuilder: (ctx, index) {
      final task = taskProvider.tasks[index];
      return Slidable(
        key: ValueKey(task.id),
        endActionPane: ActionPane(
          motion: const DrawerMotion(),
          children: [
            SlidableAction(
              onPressed: (context) {
                // 删除任务
                taskProvider.deleteTask(task.id!);
              },
              backgroundColor: Colors.red,
              foregroundColor: Colors.white,
              icon: Icons.delete,
              label: '删除',
            ),
          ],
        ),
        child: ListTile(
          leading: Checkbox(
            value: task.isCompleted,
            onChanged: (value) {
              task.isCompleted = value!;
              taskProvider.updateTask(task);
            },
          ),
          title: Text(
            task.title,
            style: TextStyle(
              decoration: task.isCompleted
                  ? TextDecoration.lineThrough
                  : null,
            ),
          ),
          subtitle: task.description != null
              ? Text(task.description!)
              : null,
          trailing: Text(
            _formatDate(task.createdAt),
            style: const TextStyle(color: Colors.grey),
          ),
          onTap: () {
            // 跳转到编辑页面，并传递当前任务
            Navigator.pushNamed(
              context,
              '/add_task',
              arguments: task, // 传递参数
            );
          },
        ),
      );
    },
  ),
),

// 在 HomeScreen 类中添加辅助方法
String _formatDate(DateTime date) {
  return '${date.year}-${date.month}-${date.day}';
}
```
**代码解释**：
- `Slidable` 实现左滑出现删除按钮。
- `Checkbox` 点击切换完成状态，调用 `updateTask`。
- 点击整个 `ListTile` 跳转到添加/编辑页面，并传递当前任务作为参数。

### 第14步：创建添加/编辑任务页面
**创建文件**：`lib/screens/add_task_screen.dart`

**写入代码**：
```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../models/task.dart';
import '../providers/task_provider.dart';

class AddTaskScreen extends StatefulWidget {
  final Task? task; // 如果传入 task，则为编辑模式

  const AddTaskScreen({Key? key, this.task}) : super(key: key);

  @override
  _AddTaskScreenState createState() => _AddTaskScreenState();
}

class _AddTaskScreenState extends State<AddTaskScreen> {
  final _formKey = GlobalKey<FormState>();
  late String _title;
  String? _description;

  @override
  void initState() {
    super.initState();
    if (widget.task != null) {
      // 编辑模式：用原有数据填充表单
      _title = widget.task!.title;
      _description = widget.task!.description;
    } else {
      _title = '';
      _description = '';
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: Text(widget.task == null ? '添加任务' : '编辑任务'),
      ),
      body: Padding(
        padding: const EdgeInsets.all(16),
        child: Form(
          key: _formKey,
          child: Column(
            children: [
              TextFormField(
                initialValue: _title,
                decoration: const InputDecoration(labelText: '任务标题'),
                validator: (value) {
                  if (value == null || value.isEmpty) {
                    return '请输入标题';
                  }
                  return null;
                },
                onSaved: (value) => _title = value!,
              ),
              TextFormField(
                initialValue: _description,
                decoration: const InputDecoration(labelText: '描述（可选）'),
                maxLines: 3,
                onSaved: (value) => _description = value,
              ),
              const SizedBox(height: 20),
              ElevatedButton(
                onPressed: _saveTask,
                child: Text(widget.task == null ? '添加' : '保存'),
              ),
            ],
          ),
        ),
      ),
    );
  }

  void _saveTask() {
    if (_formKey.currentState!.validate()) {
      _formKey.currentState!.save();

      final taskProvider = Provider.of<TaskProvider>(context, listen: false);

      if (widget.task == null) {
        // 新增
        final newTask = Task(
          title: _title,
          description: _description!.isEmpty ? null : _description,
          createdAt: DateTime.now(),
        );
        taskProvider.addTask(newTask);
      } else {
        // 编辑：需要更新任务，但保持 id 不变
        final updatedTask = Task(
          id: widget.task!.id,
          title: _title,
          description: _description!.isEmpty ? null : _description,
          isCompleted: widget.task!.isCompleted,
          createdAt: widget.task!.createdAt,
        );
        taskProvider.updateTask(updatedTask);
      }

      Navigator.pop(context); // 返回上一页
    }
  }
}
```
**代码解释**：
- 根据 `widget.task` 是否为空判断是添加还是编辑模式。
- 表单使用 `GlobalKey` 进行验证和保存。
- 保存后调用 Provider 对应方法，然后返回。

**连通性**：此页面通过 `Provider.of` 获取 `TaskProvider`，添加或更新后主页会自动刷新（因为 Provider 通知了监听器）。

### 第15步：创建设置页面
**创建文件**：`lib/screens/settings_screen.dart`

**写入代码**：
```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../providers/settings_provider.dart';

class SettingsScreen extends StatelessWidget {
  final TextEditingController _cityController = TextEditingController();

  @override
  Widget build(BuildContext context) {
    final settings = Provider.of<SettingsProvider>(context);

    return Scaffold(
      appBar: AppBar(
        title: const Text('设置'),
      ),
      body: ListView(
        children: [
          SwitchListTile(
            title: const Text('深色模式'),
            value: settings.isDarkMode,
            onChanged: (value) {
              settings.toggleDarkMode(value);
              // 主题切换会在 MyApp 中自动响应，因为 settings.isDarkMode 变化了
            },
          ),
          ListTile(
            title: const Text('当前城市'),
            subtitle: Text(settings.city),
            onTap: () async {
              _cityController.text = settings.city;
              final newCity = await _showCityDialog(context);
              if (newCity != null && newCity.isNotEmpty) {
                await settings.updateCity(newCity);
              }
            },
          ),
          const ListTile(
            title: Text('关于'),
            subtitle: Text('智能任务清单 v1.0'),
          ),
        ],
      ),
    );
  }

  Future<String?> _showCityDialog(BuildContext context) {
    return showDialog<String>(
      context: context,
      builder: (ctx) {
        return AlertDialog(
          title: const Text('修改城市'),
          content: TextField(
            controller: _cityController,
            decoration: const InputDecoration(hintText: '输入城市名（英文）'),
          ),
          actions: [
            TextButton(
              onPressed: () => Navigator.pop(ctx),
              child: const Text('取消'),
            ),
            TextButton(
              onPressed: () => Navigator.pop(ctx, _cityController.text),
              child: const Text('确定'),
            ),
          ],
        );
      },
    );
  }
}
```
**代码解释**：
- `SwitchListTile` 切换深色模式，调用 `toggleDarkMode`。
- 点击城市行弹出对话框，输入新城市后调用 `updateCity`。
- `Provider.of<SettingsProvider>` 获取实例，当城市变化时，主页的天气卡片会自动更新（因为天气数据也在同一个 Provider 中）。

---

## 第七阶段：配置路由与主题（main.dart）

### 第16步：编写主入口文件
**修改文件**：`lib/main.dart`

**写入代码**：
```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import 'providers/task_provider.dart';
import 'providers/settings_provider.dart';
import 'screens/splash_screen.dart';
import 'screens/home_screen.dart';
import 'screens/add_task_screen.dart';
import 'screens/settings_screen.dart';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (_) => TaskProvider()),
        ChangeNotifierProvider(create: (_) => SettingsProvider()),
      ],
      child: Consumer<SettingsProvider>(
        builder: (context, settings, child) {
          return MaterialApp(
            title: '智能任务清单',
            debugShowCheckedModeBanner: false,
            theme: settings.isDarkMode ? ThemeData.dark() : ThemeData.light(),
            initialRoute: '/',
            routes: {
              '/': (context) => SplashScreen(),
              '/home': (context) => HomeScreen(),
              '/add_task': (context) => AddTaskScreen(),
              '/settings': (context) => SettingsScreen(),
            },
            onGenerateRoute: (settings) {
              // 处理带参数的路由（如编辑任务）
              if (settings.name == '/add_task' && settings.arguments != null) {
                return MaterialPageRoute(
                  builder: (context) => AddTaskScreen(task: settings.arguments as Task),
                );
              }
              return null;
            },
          );
        },
      ),
    );
  }
}
```
**代码解释**：
- `MultiProvider` 同时提供两个 Provider。
- `Consumer<SettingsProvider>` 监听主题变化，动态切换 `MaterialApp` 的 `theme`。
- 定义了命名路由，并处理带参数的路由（编辑任务时传递 Task 对象）。

**连通性**：至此，所有页面和状态都通过 Provider 连接起来了。

---

## 第八阶段：完善启动页的数据加载

### 第17步：在启动页加载初始数据
**修改文件**：`lib/screens/splash_screen.dart`

**替换原有代码**：
```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../providers/task_provider.dart';
import '../providers/settings_provider.dart';

class SplashScreen extends StatefulWidget {
  @override
  _SplashScreenState createState() => _SplashScreenState();
}

class _SplashScreenState extends State<SplashScreen> {
  @override
  void initState() {
    super.initState();
    _initializeData();
  }

  Future<void> _initializeData() async {
    // 获取 Provider 实例（注意 listen: false）
    final taskProvider = Provider.of<TaskProvider>(context, listen: false);
    final settingsProvider = Provider.of<SettingsProvider>(context, listen: false);

    // 同时加载任务列表和天气数据
    await Future.wait([
      taskProvider.loadTasks(),
      settingsProvider.fetchWeather(), // 确保天气数据已加载
    ]);

    // 数据加载完成后跳转到主页
    if (mounted) {
      Navigator.of(context).pushReplacementNamed('/home');
    }
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: Center(
        child: Column(
          mainAxisAlignment: MainAxisAlignment.center,
          children: const [
            FlutterLogo(size: 100),
            SizedBox(height: 20),
            Text('智能任务清单', style: TextStyle(fontSize: 24)),
            SizedBox(height: 20),
            CircularProgressIndicator(), // 显示加载中
          ],
        ),
      ),
    );
  }
}
```
**代码解释**：
- 在 `initState` 中调用 `_initializeData`，加载任务和天气。
- 使用 `Future.wait` 并行加载，提高效率。
- 加载完成后跳转到主页，并显示加载指示器。

---

## 第九阶段：运行与测试

### 第18步：运行应用
在终端执行：
```bash
flutter run
```
如果一切正常，你会看到启动页，然后进入主页。你可以：
- 点击右下角 `+` 添加任务
- 点击任务进行编辑
- 左滑任务删除
- 点击设置图标切换深色模式、修改城市（需要有效 API Key 才能获取天气）

---

## 各文件之间的连通关系总结

为了让理解更清晰，以下是每个文件在整个应用中的角色和连接方式：

| 文件 | 作用 | 被谁使用 |
|------|------|----------|
| `models/task.dart` | 定义任务数据结构 | 被 `database_helper`、`task_provider`、`add_task_screen` 使用 |
| `services/database_helper.dart` | 封装数据库操作 | 被 `task_provider` 调用 |
| `services/weather_api_client.dart` | 封装网络请求 | 被 `settings_provider` 调用 |
| `services/settings_service.dart` | 封装 SharedPreferences | 被 `settings_provider` 调用 |
| `providers/task_provider.dart` | 管理任务状态，通知 UI 更新 | 被 `home_screen`、`add_task_screen` 通过 `Provider` 使用 |
| `providers/settings_provider.dart` | 管理主题、城市、天气状态 | 被 `home_screen`、`settings_screen` 和 `main.dart` 使用 |
| `widgets/weather_card.dart` | 展示天气卡片 | 被 `home_screen` 使用，数据来自 `settings_provider` |
| `screens/splash_screen.dart` | 启动页，加载初始数据 | 路由配置在 `main.dart`，通过 Provider 加载数据 |
| `screens/home_screen.dart` | 主页，展示任务列表和天气 | 依赖 `task_provider` 和 `settings_provider`，通过 `Consumer` 监听 |
| `screens/add_task_screen.dart` | 添加/编辑任务页 | 被主页导航调用，通过 `Provider` 调用 `task_provider` 方法 |
| `screens/settings_screen.dart` | 设置页 | 被主页导航调用，通过 `Provider` 操作 `settings_provider` |
| `main.dart` | 应用入口，配置 Provider 和路由 | 启动应用，连接所有部分 |

---

## 扩展练习（可选）

如果你想进一步巩固，可以尝试添加以下功能：
- 任务分类（使用数据库新表）
- 本地通知提醒
- 使用 BLoC 替换 Provider
- 添加单元测试

至此，你已经从零完成了一个功能完整的 Flutter 项目，涵盖了数据模型、本地数据库、网络请求、状态管理、UI 构建和路由等核心知识点。每一步的代码和解释都非常详细，你可以按顺序逐步实现，并理解它们是如何协同工作的。

如果在实现过程中遇到任何问题，随时可以提问！