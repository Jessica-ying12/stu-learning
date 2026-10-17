## 文档：https://supabase.com/docs/reference/dart/introduction

开发 Supabase Edge Functions 并供 Flutter 调用主要分为三个阶段：**本地开发**、**部署上线**以及 **Flutter 端集成**。

以下是完整的实战步骤：

---

## 一、 准备工作

确保你已经安装了以下工具：

* **Supabase CLI**: 这是开发和部署的关键。
    npm install supabase --save-dev
    supabase --version
    核心配置流程
    安装好 CLI 后，通常需要执行以下三个步骤来连接你的项目：

       登录控制台：

       Bash
    supabase login
    执行后会弹出一个浏览器窗口，让你生成并复制 Access Token。

       项目初始化（在你的 Flutter 项目根目录）：

       Bash
    supabase init
    这会创建一个 supabase 文件夹，用于存放 Edge Functions 源码和数据库配置。

       链接远程项目：

       Bash
    supabase link --project-ref 你的项目ID
    项目 ID 可以在 Supabase Dashboard 的 Project Settings -> General 中找到。
* **Docker**: 本地运行和测试 Edge Functions 需要它。

```bash
# 登录 Supabase
supabase login
# 初始化项目（如果在现有项目目录下则跳过）
supabase init

```

---

## 二、 编写 Edge Function (TypeScript)

Edge Functions 默认运行在 Deno 环境中，通常使用 TypeScript 编写。

### 1. 创建新函数

在终端执行：

```bash
supabase functions new hello-world

```

这会在 `supabase/functions/hello-world/index.ts` 创建一个模板文件。

### 2. 编写逻辑

编辑 `index.ts`，处理来自 Flutter 的请求：

```typescript
// index.ts
import { serve } from "https://deno.land/std@0.168.0/http/server.ts"

serve(async (req) => {
  // 1. 处理跨域 (CORS) - 必须，否则 Flutter Web 端会报错
  if (req.method === 'OPTIONS') {
    return new Response('ok', { headers: { 'Access-Control-Allow-Origin': '*' } })
  }

  try {
    // 2. 获取 Flutter 传来的参数
    const { name } = await req.json()
    const data = {
      message: `你好, ${name}! 这是来自 Edge Function 的响应。`,
    }

    // 3. 返回 JSON 响应
    return new Response(
      JSON.stringify(data),
      { 
        headers: { 
          "Content-Type": "application/json",
          "Access-Control-Allow-Origin": "*" // 允许跨域
        } 
      },
    )
  } catch (error) {
    return new Response(JSON.stringify({ error: error.message }), { status: 400 })
  }
})

```

---

## 三、 部署函数

将函数推送到 Supabase 云端：

```bash
# [PROJECT_ID] 在 Supabase 后台项目的 Settings -> General 中查找
supabase functions deploy hello-world --project-ref [PROJECT_ID]

```

---

## 四、 在 Flutter 中调用

在 Flutter 中，你不需要手动发送 HTTP 请求，直接使用 `supabase_flutter` SDK 即可。

### 1. 依赖配置

在 `pubspec.yaml` 中确保已添加：

```yaml
dependencies:
  supabase_flutter: ^2.0.0 # 建议使用最新稳定版

```

### 2. 调用代码

```dart
import 'package:supabase_flutter/supabase_flutter.dart';

// 初始化 (通常在 main.dart)
// await Supabase.initialize(url: 'YOUR_URL', anonKey: 'YOUR_ANON_KEY');

Future<void> callEdgeFunction() async {
  final supabase = Supabase.instance.client;

  try {
    // 调用名为 'hello-world' 的函数
    final res = await supabase.functions.invoke(
      'hello-world',
      body: {'name': 'Flutter 用户'},
    );

    // 获取返回的数据
    final data = res.data;
    print('响应结果: ${data['message']}');
    
  } on FunctionException catch (error) {
    print('Edge Function 错误: ${error.status} - ${error.details}');
  } catch (error) {
    print('意外错误: $error');
  }
}

```

---

## 💡 核心要点总结

| 环节 | 注意事项 |
| --- | --- |
| **跨域 (CORS)** | 必须在 TypeScript 代码中处理 `OPTIONS` 请求并添加 `Access-Control-Allow-Origin` 头部。 |
| **认证 (Auth)** | 默认情况下，`invoke` 会自动带上当前登录用户的 JWT。如果函数需要匿名访问，可以在部署时加 `--no-verify-jwt`。 |
| **本地测试** | 使用 `supabase functions serve` 可以在本地模拟运行，无需频繁部署。 |
| **参数类型** | Flutter 中的 `body` 对应 TS 中的 `req.json()`，确保格式对齐。 |

**需要我帮你写一个具体的业务逻辑（例如：集成 Stripe 支付或发送邮件）的函数模板吗？**