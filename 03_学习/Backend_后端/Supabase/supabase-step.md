# 新建项目
### 数据库密码：supa'sfristtest


标准化的生产级创建步骤：

---

### 第一阶段：云端初始化 (Console)

1. **登录与创建 Organization**：
* 登录 [Supabase Dashboard](https://www.google.com/search?q=https://supabase.com/dashboard/)。
* 创建一个 Organization（通常以公司或项目名命名）。


2. **新建 Project**：
* **Database Password**：**极其重要**。这是你的 PostgreSQL `postgres` 角色的密码。建议使用密码管理器生成并保存，后续本地连接远程库时必用。
* **Region**：选择离你用户最近的地区（如 `ap-northeast-1` 东京）。
* **Pricing Plan**：开发阶段选 **Free Tier** 即可。


3. **获取 API Keys**：
* 项目创建后，在 `Settings -> API` 中获取 `anon` key（前端使用）和 `service_role` key（后端/管理脚本使用，具有绕过 RLS 的最高权限，切勿泄露）。

### Publishable key: YOUR_SUPABASE_PUBLISHABLE_KEY
### Secret keys: YOUR_SUPABASE_SECRET_KEY


---

### 第二阶段：本地开发环境配置 (CLI)

作为高级工程师，你应该避免直接在网页 UI 上改表结构。使用 CLI 才能实现版本控制。

1. **安装 CLI**（以 Node 为例）：
```bash
npm install supabase --save-dev

```


2. **初始化本地项目**：
```bash
npx supabase init

```


这会生成一个 `supabase` 文件夹，包含 `config.toml` 和 `migrations` 目录。
3. **启动本地 Docker 环境**：
```bash
npx supabase start

```


Supabase 会在本地启动 Postgres、GoTrue、PostgREST 等全套服务。你会获得一个本地的 **Studio Dashboard** (通常是 `http://localhost:54323`)。


4. webstorm安装deno插件

  WebStorm 启用 Deno                                                                                                                                                
  1. 打开 Settings (Ctrl+Alt+S)
  2. 导航到 Languages & Frameworks → Deno
  3. 勾选 Enable Deno support
  4. Deno executable 填写：C:\Users\Administrator\.deno\bin\deno.exe
  5. 点击 Apply → OK

  配置完成后 WebStorm 会用 Deno 语言服务器替代内置 TypeScript 检查，.ts 扩展导入和 import map 就都能正确识别了。

---

### 第三阶段：数据库设计与迁移 (Migrations)

1. **创建迁移文件**：
不要直接去本地控制台点，运行：
```bash
npx supabase migration new init_schema

```


2. **编写 SQL**：
在生成的 `migrations/xxx_init_schema.sql` 中编写你的 DDL。
* 定义 `Tables` 和 `Foreign Keys`。
* 开启 `RLS` (Row Level Security)。
* 定义 `Indexes` 优化查询。


3. **应用到本地**：
```bash
npx supabase db reset

```



---

### 第四阶段：类型安全 (Type Safety)

这是 Supabase 开发的“爽点”。通过数据库 Schema 自动生成 TypeScript 类型：

```bash
npx supabase gen types typescript --local > types/supabase.ts

```

在前端初始化 Client 时引入：

```typescript
import { createClient } from '@supabase/supabase-js'
import { Database } from './types/supabase'

const supabase = createClient<Database>(URL, KEY)
// 此时你所有的 query 都会有完整的自动补全和类型检查

```

---

### 第五阶段：部署到生产 (Deploy)

1. **链接远程项目**：
```bash
npx supabase link --project-ref <你的项目ID>

```


2. **推送数据库变更**：
```bash
npx supabase db push

```


这会将你本地 `migrations` 文件夹中所有未同步的 SQL 脚本在远程生产库上执行。

---

### 核心检查清单 (Checklist)

* [ ] **Auth 配置**：是否在 `Authentication -> Providers` 中关闭了不需要的登录方式？
* [ ] **RLS 策略**：是否为每张表配置了 `SELECT/INSERT/UPDATE` 策略？
* [ ] **Email 配置**：生产环境务必配置自己的 SMTP 服务（如 Resend 或 SendGrid），Supabase 默认的测试邮件配额极低。

**如果你准备好了，我可以为你提供一个包含用户 Profile 自动关联、RLS 安全策略和基础表的 SQL 模板，直接作为你的第一个迁移文件。需要吗？**