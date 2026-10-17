# Supabase 初步流程
在切入 Supabase 这种 **BaaS (Backend as a Service)** 平台时，最核心的思维转变是从“编写 API 逻辑”转向“架构数据库策略”。

Supabase 并不是传统意义上的 Web 框架（如 NestJS 或 Go Gin），它是一套围绕 **PostgreSQL** 构建的工具链。要构建一个生产级项目，你需要掌握以下硬性知识：

### 1. 核心架构与“硬核”组件

Supabase 的强大源于它对顶级开源项目的整合，了解这些组件能让你在遇到性能或逻辑瓶颈时快速定位：

* **PostgreSQL (核心)**：它是整个系统的灵魂。你不仅是写 SQL，还要精通视图（Views）、函数（RPC/Functions）、枚举类型和触发器（Triggers）。
* **PostgREST**：自动将你的数据库 Schema 映射为标准 RESTful API。这意味着你不再需要手写路由，API 的查询能力直接取决于你的数据库结构。
* **GoTrue**：基于 JWT 的身份验证系统，处理注册、登录及第三方 OAuth。
* **Realtime (Elixir 编写)**：通过监听 PostgreSQL 的逻辑复制日志（Logical Replication），实现毫秒级的 WebSocket 数据推送。

---

### 2. 关键硬性知识点

#### 🛡️ 行级安全策略 (Row Level Security, RLS)

这是后端工程师最不容忽视的一环。在 Supabase 中，**数据库就是 API**。为了防止用户 A 看到用户 B 的数据，你必须编写 SQL 策略。

> **硬规则**：永远不要在未开启 RLS 的情况下发布表。

```sql
-- 例子：只允许用户阅读自己的个人档案
alter table profiles enable row level security;

create policy "Users can see their own profile"
  on profiles for select
  using ( auth.uid() = id );

```

#### 🌩️ Edge Functions (计算层)

当你需要处理支付（Stripe Webhooks）、发送邮件或调用 AI 接口等**不适合放在前端或 SQL 里**的操作时，你需要使用 Deno 编写的 Edge Functions。

#### 🧪 数据库迁移 (Migrations)

不要通过控制台手动点选创建表。高级工程师应当使用 **Supabase CLI**。

* 掌握 `supabase db diff`：对比本地与远程库差异。
* 掌握 `supabase db remote commit`：将线上更改同步回本地迁移文件。

---

### 3. 项目构建流程 (最佳实践)

1. **本地开发环境初始化**：
* 安装 [Docker](https://www.docker.com/)（Supabase 本地运行的必要依赖）。
* 运行 `supabase init` 初始化项目结构。
* 运行 `supabase start` 在本地启动全套组件（包括 Studio 仪表盘）。


2. **Schema 设计与类型生成**：
* 在本地编写 SQL 迁移文件，定义表、外键、索引。
* **核心步骤**：使用 `supabase gen types typescript` 自动生成全量数据库类型，确保前端调用时拥有完美的类型安全感。


3. **身份认证与权限配置**：
* 在 `auth.users` 表的基础上，建立业务层的 `public.profiles` 表。
* 通过 **Database Triggers** 实现用户注册后自动初始化业务数据。


4. **环境同步与部署 (CI/CD)**：
* 将代码提交至 GitHub，配置 GitHub Actions。
* 使用 `supabase link` 连接远程项目。
* 生产环境部署通过 `supabase db push` 推送经过本地验证的迁移脚本。



---

### 4. 高级进阶：什么时候该退出 Supabase？

作为高级工程师，你需要预判：

* **计算密集型逻辑**：如果你的后端涉及大量的复杂计算（如视频转码、大规模图算法），Edge Functions 可能不够用，此时建议接入一个传统的 API 服务（如部署在 Fly.io 的 Go/Node 服务）作为补充。
* **复杂事务处理**：虽然 PostgREST 支持 RPC 调用 PostgreSQL 函数来处理事务，但如果业务逻辑极其复杂且嵌套极深，维护 SQL 函数的成本会超过编写传统后端代码。

**你想让我为你生成一个包含 RLS 策略和数据库函数的基础 SaaS 项目 SQL 模板吗？**

[Supabase 架构与全栈开发指南](https://www.youtube.com/watch?v=TS1TVXUFFCc)
这个视频详细演示了如何使用 CLI 进行本地初始化以及如何将本地开发的数据库 Schema 同步到线上，是掌握专业开发流的必看内容。