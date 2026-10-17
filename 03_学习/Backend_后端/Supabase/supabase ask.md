
# 从地基到屋顶：Supabase 核心概念全解
## ① PostgreSQL / Postgres（Q1 + Q12，同一个东西）
✅ **一句话**：世界上最先进的开源关系型数据库，Supabase 的一切根基。
PostgreSQL 和 Postgres 是**同一个东西**。历史渊源：
```
1986  UC Berkeley → POSTGRES 项目（用 PostQUEL 查询语言）
1994  Postgres95（替换为 SQL 语言）
1996  更名 PostgreSQL（强调 SQL 能力）
至今  社区普遍简称 Postgres（不易混淆）
```
**它提供什么：**
关系型数据库的核心能力 — 表、行、列、SQL 查询、JOIN、事务、索引。但 Postgres 远不止于此：
- **扩展系统**：pgvector（AI 向量搜索）、PostGIS（地理信息）、pg_cron（定时任务）等 1000+ 扩展
- **行级安全（RLS）**：数据库层面的访问控制（后面详解）
- **逻辑复制**：实时捕获数据变更（Realtime 的底层机制）
- **函数/触发器**：在数据库内运行业务逻辑
**为什么 Supabase 选 Postgres 而不是 MongoDB/DynamoDB？** 因为 Postgres 是唯一一个兼具"关系型数据库的严谨性"和"足够丰富的扩展能力去覆盖 NoSQL 场景"的数据库。用官方的话说：没有其他数据库能在提供 Firebase 功能的同时保持超越它的可扩展性。
---
## ② Schema（Q8）
✅ **一句话**：数据库中的"命名空间"，用来组织表、视图、函数等对象。
这个概念很多人搞混。Schema **不是**"表结构"（虽然中文常这么翻译）。
```sql
-- Postgres 中的 schema 是一个逻辑分区
public.posts          -- public schema 下的 posts 表
auth.users            -- auth schema 下的 users 表（GoTrue 管理）
storage.objects       -- storage schema 下的 objects 表
```
你可以把 schema 想象成**文件夹**：
```
PostgreSQL 数据库
├── public/          ← 你的业务表放这里（默认 schema）
│   ├── posts
│   ├── profiles
│   └── comments
├── auth/            ← GoTrue 的认证数据（Supabase 管理，别动）
│   ├── users
│   └── sessions
├── storage/         ← Storage API 的元数据
│   ├── buckets
│   └── objects
└── extensions/      ← Postgres 扩展
```
**为什么 Schema 对 Supabase 如此重要？** 因为 PostgREST 正是通过**内省 schema 的结构**来自动生成 API 的。你在 `public` schema 里建了一张表，API 就自动出现了。

---
## ③ 安全模型（Q2）— Supabase 的安全哲学
✅ **一句话**：安全不在应用层实现，而是下沉到数据库层，由 PostgreSQL 原生的 RLS 机制保证。
传统 Web 应用的安全是这样的：
```
客户端 → [后端服务器：验证身份 → 检查权限 → 过滤数据] → 数据库
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
           安全逻辑散落在后端代码各处，容易遗漏
```
Supabase 的安全模型：
```
客户端 → [Kong: 验证 API Key] → [PostgREST: 解析 JWT] → [PostgreSQL: RLS 强制过滤]
                                                          ^^^^^^^^^^^^^^^^^^^^^^^^
                                                          安全逻辑集中在数据库，
                                                          无论 API 怎么调用都绕不过
```
**核心思想**：与其相信每个 API endpoint 都正确实现了权限检查，不如让数据库本身拒绝未授权的数据访问。这就是 RLS。

---
## ④ RLS — Row Level Security（Q3）
✅ **一句话**：PostgreSQL 原生功能，让你为每张表定义"谁能看到/修改哪些行"的规则。
RLS 是 Supabase 安全模型的**核心引擎**。没有它，Supabase 的"无后端"架构就不成立。
```sql
-- 第一步：开启 RLS（不开的话，任何人都能访问所有数据！）
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;
-- 第二步：定义策略（Policy）
CREATE POLICY "用户只能看自己的帖子"
  ON posts                          -- 作用于 posts 表
  FOR SELECT                        -- 仅影响读操作
  USING (auth.uid() = user_id);     -- 条件：JWT 中的用户 ID = 行的 user_id
CREATE POLICY "用户只能改自己的帖子"
  ON posts
  FOR UPDATE
  USING (auth.uid() = user_id)      -- 能看到哪些行
  WITH CHECK (auth.uid() = user_id); -- 修改后的行也必须满足条件
```
**`auth.uid()` 是什么？** 它不是魔法，就是一个 Postgres 函数，读取当前会话中 JWT 的 `sub` 字段：
```sql
-- auth.uid() 的本质：
current_setting('request.jwt.claims', true)::json->>'sub'
-- PostgREST 在每次请求前，把 JWT 的 claims 设置到 Postgres 会话变量里
```
**RLS 的性能注意点**：RLS 策略中引用的列（如 `user_id`）一定要加索引，否则每次查询都会全表扫描。

---
## ⑤ GoTrue（Q6）— 认证服务
✅ **一句话**：一个用 Go 编写的 JWT 签发服务器，负责"你是谁"这个问题。
GoTrue 处理用户注册、登录、令牌刷新：
```
用户点击"登录" → GoTrue 验证凭据 → 签发 JWT → 返回给客户端
                                      │
                JWT 内容 ≈ {
                  "sub": "user-uuid-123",    ← auth.uid() 读的就是这个
                  "role": "authenticated",    ← Postgres 角色
                  "email": "user@example.com",
                  "exp": 1735689600          ← 过期时间
                }
```
支持 20+ OAuth 提供商（Google/GitHub/Apple...）、Magic Link、OTP、SSO (SAML)。
**Auth 数据存在哪？** 就在你的 PostgreSQL 的 `auth` schema 里。这意味着你可以用标准 SQL 触发器关联 auth 数据到你的业务表（见下面的语法解析）。

---
## ⑥ `anon` key（Q7）
✅ **一句话**：一个**公开的、权限受限的** API 密钥，代表"未登录用户"的身份。
Supabase 给每个项目两个 key：

| Key | 身份 | 权限 | 能否暴露在前端 |
|---|---|---|---|
| `anon` | 匿名用户 / 未登录状态 | **受 RLS 约束** | ✅ 可以（必须搭配 RLS） |
| `service_role` | 超级管理员 | **绕过所有 RLS** | ❌ 绝对不行 |
**工作流程**：
```
1. 未登录用户请求 → 带 anon key → PostgREST 以 "anon" 角色执行查询 → RLS 决定能看什么
2. 用户登录后请求 → 带 anon key + JWT → PostgREST 以 "authenticated" 角色执行 → RLS 用 JWT 中的 uid 过滤
```
`anon` key 本质上是一个 JWT，其 `role` 字段为 `anon`。它**不是密码**，它只是告诉 Kong 网关"我有权调用这个项目的 API"。真正的安全由 RLS 保证。

---
## ⑦ Kong（Q5）— API 网关
✅ **一句话**：所有请求的"前门"，负责路由分发、API Key 验证和限流。
```
客户端的一切请求
       │
       ▼
  ┌─────────────────────────────────────┐
  │              Kong                    │
  │  1. 验证 API Key（anon/service_role）│
  │  2. 路由到对应服务：                  │
  │     /rest/v1/*   → PostgREST        │
  │     /auth/v1/*   → GoTrue           │
  │     /realtime/*  → Realtime         │
  │     /storage/v1/*→ Storage API      │
  │     /functions/* → Edge Functions   │
  │  3. 限流、日志、CORS                  │
  └─────────────────────────────────────┘
```
Kong 基于 **Nginx + Lua**，是一个成熟的开源 API 网关。Supabase 用它统一所有微服务的入口，你不需要直接和它打交道。

---
## ⑧ PostgREST（Q4）— 自动 API 生成
✅ **一句话**：一个 Haskell 编写的服务器，把你的 PostgreSQL 数据库直接变成 RESTful API。
**核心机制**：
```
你建了一张表：
  CREATE TABLE posts (id serial, title text, user_id uuid);
PostgREST 自动内省 schema，立刻生成：
  GET    /rest/v1/posts          → SELECT
  POST   /rest/v1/posts          → INSERT
  PATCH  /rest/v1/posts?id=eq.1  → UPDATE
  DELETE /rest/v1/posts?id=eq.1  → DELETE
```
**关键特性**：
- **每个请求 = 单条 SQL**（因此极快，基准测试超 Firebase 300%+）
- 支持任意深度的**关联查询**：`?select=id,title,author(name)` 自动 JOIN
- 支持调用 Postgres 函数：`POST /rest/v1/rpc/my_function`
- Schema 变更后 API 自动刷新
- 和 `pg_graphql` 扩展配合还能提供 GraphQL API
**为什么"单条 SQL"很重要？** 传统 ORM 容易产生 N+1 查询。PostgREST 把嵌套资源请求编译成一条带 JOIN 的 SQL，消除了这个问题。
---
## ⑨ SQL 语法解析（Q9-代码）
逐行拆解这段代码：
```sql
-- ═══════════════════════════════════════════════════
-- 第一部分：创建函数
-- ═══════════════════════════════════════════════════
CREATE FUNCTION public.handle_new_user()
--             ^^^^^^ ^^^^^^^^^^^^^^^^
--             schema    函数名
--             在 public schema 中创建
RETURNS trigger AS $$
--      ^^^^^^^    ^^
--      返回类型    美元符引用（$$ 之间的内容是函数体，
--      是触发器    避免内部引号转义问题）
BEGIN
  -- 函数体开始
  INSERT INTO public.profiles (id, email)
  VALUES (new.id, new.email);
  --      ^^^
  --      "new" 是触发器的特殊变量，
  --      代表刚被 INSERT 的那一行数据
  --      这里 new.id = auth.users 表中新插入用户的 id
  --           new.email = 新用户的 email
  RETURN new;
  -- 触发器函数必须返回 new（INSERT/UPDATE 触发器）
  -- 或 old（DELETE 触发器）或 NULL（取消操作）
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;
-- ^^^^^^^^          ^^^^^^^^^^^^^^^^^
-- 使用 PL/pgSQL     关键！以"函数创建者"的权限执行，
-- （Postgres 的     而不是"调用者"的权限。
--  过程语言）       这样 GoTrue 写入 auth.users 时，
--                  这个函数能以更高

```

# Supabase 核心概念：从地基到屋顶
## 📐 认知路线图
```
Q12 PostgreSQL（地基）
  → Q1 Postgres（同一个东西）
    → Q8 Schema（数据库的组织方式）
      → Q9 SQL 语法解析（Schema 里的代码长什么样）
        → Q2 安全模型（谁能访问什么）
          → Q3 RLS（安全模型的核心机制）
            → Q6 GoTrue（谁来签发身份证明）
              → Q7 anon key（前门钥匙）
                → Q4 PostgREST（数据库的 HTTP 外壳）
                  → Q5 Kong（所有请求的门卫）
                    → Q9b Realtime（实时推送）
                      → Q10 S3 + TUS（文件存储）
                        → Q11 Edge Functions（自定义逻辑）
```
---
## Q12 + Q1：PostgreSQL / Postgres — 同一个东西
**Postgres 和 PostgreSQL 是同一个数据库的两个名字。**
历史渊源：1986 年加州伯克利大学的 POSTGRES 项目 → 1994 年加入 SQL 支持改名 Postgres95 → 最终命名 PostgreSQL。因为 "PostgreSQL" 太长且容易拼错，社区普遍简称 **Postgres**。
**Postgres 是什么？** 一个开源的**关系型数据库管理系统（RDBMS）**。
如果你从未接触过数据库，可以这样理解：
```
Excel 表格         → Postgres 表（table）
Excel 工作簿       → Postgres 数据库（database）
Excel 的一行       → 一条记录（row）
Excel 的一列       → 一个字段（column），但有
我检测到 **研究/理解** 意图 — 用户在上一轮 Supabase 全景指南基础上，提出了 12 个具体概念的深入理解需求。这些都是概念解释类问题，不涉及代码实现。我的方案：直接综合回答，按知识依赖关系排序。
这是一个**分析回答类任务**，所有概念我已在上一轮调研中掌握充分，无需额外检索。无需多模型协作 — 这些是对已收集知识的结构化解释，我可以直接给出高质量答案。
我按**知识依赖链**重新排列顺序：从底层基石向上构建，每个概念都建立在前一个之上。
---

```

# Supabase 核心概念逐层拆解
## ① PostgreSQL / Postgres（Q1 + Q12，合并回答）
PostgreSQL 和 Postgres 是**同一个东西**。
PostgreSQL 诞生于 1986 年加州大学伯克利分校的 POSTGRES 项目。1994 年加入 SQL 支持后改名 Postgres95，最终定名 PostgreSQL。因为全名拗口且容易混淆，社区普遍简称 **Postgres**。Supabase 官方也用 Postgres。
**它是什么？** 一个开源的**关系型数据库管理系统**（RDBMS）。
**"关系型"意味着什么？** 数据以**表**的形式存储，表与表之间通过**外键**建立关系。你用 **SQL**（Structured Query Language）来查询和操作数据。
**为什么 Supabase 选 Postgres 而非 MongoDB/MySQL？**
Postgres 是极少数同时具备以下能力的数据库：

| 能力            | 说明                                      |
| ------------- | --------------------------------------- |
| **行级安全（RLS）** | 数据库层面的访问控制，这是 Supabase 安全模型的根基          |
| **逻辑复制**      | 实时监听数据变更（Realtime 功能的基础）                |
| **丰富的扩展生态**   | pgvector（AI）、PostGIS（地理）、pg_cron（定时任务）… |
| **JSON 支持**   | `jsonb` 类型让你在关系型数据库里也能存非结构化数据           |
| **函数 / 触发器**  | 数据库内直接写业务逻辑                             |
| **视图 / 物化视图** | 预计算、API 抽象层                             |
一句话总结：**Postgres 是 Supabase 的操作系统，其他一切组件都是它的"应用程序"。**
---
## ② Schema（Q8）
Schema 在 PostgreSQL 中是**数据库内部的命名空间**。
把它想象成一个**文件夹系统**：
```
一个 PostgreSQL 数据库
├── public (schema)        ← 你的业务表默认放这里
│   ├── posts (table)
│   ├── profiles (table)
│   └── comments (table)
├── auth (schema)          ← Supabase Auth (GoTrue) 的数据
│   ├── users (table)      ← 所有注册用户
│   └── sessions (table)
├── storage (schema)       ← Supabase Storage 的元数据
│   ├── buckets (table)
│   └── objects (table)
└── extensions (schema)    ← Postgres 扩展
```
**为什么要分 Schema？**
- **隔离**：`auth.users` 是 Supabase 管理的系统表，你不应该直接修改它；`public.*` 是你自己的领地
- **API 暴露控制**：PostgREST 默认只暴露 `public` schema 的表作为 REST API，`auth` schema 不会被暴露
- **权限边界**：不同 schema 可以有不同的访问权限
**Schema 还有另一个含义**：在更广义的语境中（比如 "PostgREST 从 schema 生成 API"），它指的是数据库的**结构定义**（有哪些表、每个表有什么列、列是什么类型、表之间什么关系）。两个含义要根据上下文区分。
---
## ③ 安全模型（Q2）
Supabase 的安全模型可以用一句话概括：
> **数据库就是防火墙。**
传统 Web 应用有三层：
```
客户端 → 后端服务器（鉴权 + 过滤）→ 数据库
         ↑ 安全逻辑写在这里
```
Supabase 砍掉了中间层：
```
客户端 → API 网关 (Kong) → PostgREST → PostgreSQL
                                        ↑ 安全逻辑写在这里（RLS）
```
安全由三道防线组成：
```
第一道：Kong（API 网关）
   → 验证 API Key 是否有效
   → 路由到正确的服务
第二道：JWT 令牌
   → GoTrue 签发，包含用户身份信息
   → PostgREST 解析并注入到 Postgres 会话
第三道：RLS（行级安全）
   → 在 Postgres 内部，逐行判断"这个用户能不能碰这条数据"
   → 即使有人拿到了 API Key，也只能访问 RLS 策略允许的数据
```
**这个设计的深层逻辑**：安全规则和数据放在同一个地方（数据库内），不可能出现"后端忘了做权限检查"的 bug，因为**数据库自己就是权限检查器**。

---
## ④ RLS — 行级安全（Q3）
RLS（Row Level Security）是 PostgreSQL 的**原生功能**，不是 Supabase 发明的。但 Supabase 把它变成了核心安全机制。
**一句话理解**：RLS 是写在表上的 **SQL 布尔表达式**，每次查询时 Postgres 自动追加这个条件。
```sql
-- 没有 RLS 时：
SELECT * FROM posts;
-- 返回所有帖子（危险！）
-- 开启 RLS 并设置策略后：
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;
CREATE POLICY "用户只能看自己的帖子"
  ON posts
  FOR SELECT                          -- 对 SELECT 操作生效
  USING (user_id = auth.uid());       -- 这是一个布尔表达式
-- 现在同样的查询：
SELECT * FROM posts;
-- Postgres 自动变成：
SELECT * FROM posts WHERE user_id = auth.uid();
-- 只返回属于当前用户的帖子
```
**`auth.uid()` 的本质**：
它不是魔法，只是一个 Postgres 函数：
```sql
-- 它实际上做的是：
current_setting('request.jwt.claims', true)::json->>'sub'
-- 即：从当前会话的 JWT claims 中提取 "sub"（subject）字段
-- 这个值是 PostgREST 在收到请求时设置的
```
**RLS 策略的四种操作**：

| 操作 | 关键字 | 对应 HTTP |
|---|---|---|
| 读 | `FOR SELECT` | GET |
| 增 | `FOR INSERT` | POST |
| 改 | `FOR UPDATE` | PATCH |
| 删 | `FOR DELETE` | DELETE |
**一个完整的例子**：
```sql
-- 任何人都能读公开帖子
CREATE POLICY "公开帖子所有人可读"
  ON posts FOR SELECT
  USING (is_public = true);
-- 但只有作者能改自己的帖子
CREATE POLICY "作者可编辑"
  ON posts FOR UPDATE
  USING (user_id = auth.uid());
-- 管理员可以删任何帖子
CREATE POLICY "管理员可删除"
  ON posts FOR DELETE
  USING (auth.jwt()->>'role' = 'admin');
```
---
## ⑤ GoTrue — 认证服务（Q6）
GoTrue 是一个用 **Go 语言**编写的 HTTP 服务器，职责单一：**管理用户身份，签发 JWT。**
```
用户输入邮箱+密码
        │
        ▼
┌──────────────┐
│   GoTrue     │
│              │
│ 1. 验证凭证  │ ← 密码哈希比对 / OAuth 验证 / Magic Link 验证
│ 2. 创建会话  │ ← 写入 auth.sessions 表
│ 3. 签发 JWT  │ ← 包含 {sub: "user-uuid", role: "authenticated", ...}
│              │
└──────┬───────┘
       │
       ▼ 返回 JWT 给客户端
```
JWT（JSON Web Token）长这样（base64 解码后）：
```json
{
  "sub": "d0714948-...",          // 用户唯一 ID
  "role": "authenticated",        // 角色
  "email": "user@example.com",
  "aud": "authenticated",
  "exp": 1735689600,             // 过期时间
  "iat": 1735603200              // 签发时间
}
```
GoTrue 支持的登录方式（前面已列过，这里强调原理）：
- **密码登录**：GoTrue 自己存哈希密码到 `auth.users`
- **OAuth（Google/GitHub 等）**：GoTrue 充当 OAuth 客户端，拿到第三方令牌后自己签发 JWT
- **Magic Link**：发一封包含一次性令牌的邮件，点击后 GoTrue 验证并签发 JWT
- **OTP**：发短信验证码，验证后签发 JWT
**GoTrue 的数据全部存在 PostgreSQL 中**（`auth` schema），所以你可以用触发器监听用户注册事件，这就引出了 Q9 的语法解析。
---
## ⑥ `anon` key（Q7）
Supabase 项目创建后会生成两个 API Key：

| Key | 角色 | 暴露范围 | RLS |
|---|---|---|---|
| **`anon`** | 匿名/公开 | ✅ 可以放在前端代码中 | **受 RLS 约束** |
| **`service_role`** | 超级管理员 | ❌ 绝对不能暴露给客户端 | **绕过所有 RLS** |
**`anon` key 的本质**：它是一个 JWT，其 `role` 字段值为 `"anon"`。
当未登录的用户访问你的 API 时，Kong 使用 `anon` key 创建一个身份为 "匿名" 的 Postgres 会话。此时 RLS 策略中的 `auth.uid()` 返回 `NULL`。
```sql
-- 这条策略允许匿名用户读取公开帖子
CREATE POLICY "匿名可读公开内容"
  ON posts FOR SELECT
  USING (is_public = true);  -- 不依赖 auth.uid()，所以匿名可用
-- 这条策略阻止匿名用户写入
CREATE POLICY "登录才能发帖"
  ON posts FOR INSERT
  WITH CHECK (auth.uid() IS NOT NULL);  -- 匿名 uid 是 NULL，被拦截
```
**为什么 `anon` key 可以公开？** 因为它的一切权限都被 RLS 限制。即使有人反编译你的前端代码拿到了 key，也只能做 RLS 策略允许的操作。**key 是入场券，RLS 是真正的保安。**

---
## ⑦ Kong — API 网关（Q5）
Kong 是一个基于 **Nginx** 的开源 API 网关（用 Lua 编写）。
在 Supabase 中，Kong 是**所有请求的入口**：
```
客户端的所有请求
        │
        ▼
┌──────────────────────┐
│       Kong           │
│                      │
│  /rest/v1/*    → PostgREST     （数据 API）
│  /auth/v1/*    → GoTrue        （认证）
│  /realtime/v1  → Realtime      （WebSocket）
│  /storage/v1/* → Storage API   （文件）
│  /functions/v1 → Edge Runtime  （边缘函数）
│                      │
│  + API Key 验证      │
│  + 请求限流          │
│  + CORS 处理         │
└──────────────────────┘
```
**Kong 做什么**：路由分发（把请求导向正确的微服务）+ 安全关卡（验证 API Key）+ 流量控制。
**Kong 不做什么**：不处理业务逻辑，不读写数据，不签发 JWT。它是一个纯粹的"交通警察"。

---
## ⑧ PostgREST（Q4）
PostgREST 是一个用 **Haskell** 编写的独立 HTTP 服务器，职责：**把 PostgreSQL 数据库变成 REST API。**
工作原理分三步：
```
步骤 1（启动时）：内省 PostgreSQL
  → 读取 information_schema
  → 发现所有表、视图、函数、列类型、外键关系
  → 构建内存中的 API 路由表
步骤 2（收到请求时）：翻译 HTTP → SQL
  GET /rest/v1/posts?select=id,title&order=created_at.desc&limit=5
  → 翻译为单条 SQL：
> 我检测到 **研究/理解** 意图 — 用户在上一轮 Supabase 全景指南之后，对其中提到的关键概念提出了 12 个追问，需要深入解释。我的方案：直接综合已有知识 → 结构化输出。
这是一个**纯分析/解释类任务**，所有问题都基于上一轮已深入调研过的 Supabase 体系。无需再次检索，也无需多模型协作——所有概念我已掌握充足上下文。
我会按**从底层到上层的依赖顺序**组织回答，让每个概念出现时，它的前置依赖你已经理解了。
---
``````
# Supabase 核心概念逐层拆解
## 一、地基层
### Q1 & Q12：PostgreSQL 是什么？
这两个问题本质一样，合并回答。
PostgreSQL（常简称 Postgres）是一个 **开源关系型数据库管理系统**，1986 年诞生于加州大学伯克利分校。
**"关系型"是什么意思？** 数据以**表格**（table）形式存储，表与表之间通过**外键**建立关联关系。你可以用 SQL（Structured Query Language）做任意复杂的查询——JOIN、聚合、窗口函数、递归查询，全部支持。
与 MySQL、SQLite 同属关系型数据库，但 Postgres 的独特之处在于**可扩展性**：

| 特性               | 说明                                                   |
| ---------------- | ---------------------------------------------------- |
| **自定义类型**        | 你可以定义自己的数据类型                                         |
| **Extension 机制** | 插件生态极其丰富（pgvector 做向量搜索、PostGIS 做地理信息、pg_cron 做定时任务） |
| **RLS（行级安全）**    | 数据库层面的细粒度权限控制（后面详解）                                  |
| **逻辑复制**         | 数据变更可以实时流式输出（Supabase Realtime 的基础）                  |
| **函数/触发器**       | 数据库内部可以跑业务逻辑代码                                       |
**为什么 Supabase 选 Postgres 而不是 MongoDB/Redis 等？** 因为 Postgres 是唯一一个**同时具备**关系查询能力、Extension 扩展性、行级安全、逻辑复制的数据库。它一个顶四个，才能让 Supabase 把"整个后端"折叠进数据库层。


---
### Q8：Schema 是什么？
在 Postgres 中，**schema** 是数据库内部的**命名空间**——一个逻辑分组容器。
类比文件系统理解：
```
数据库 (database)
├── public schema        ← 你的业务表默认放这里
│   ├── posts 表
│   ├── profiles 表
│   └── comments 表
├── auth schema          ← Supabase Auth (GoTrue) 的数据
│   ├── users 表
│   ├── sessions 表
│   └── ...
├── storage schema       ← Supabase Storage 的元数据
│   ├── buckets 表
│   ├── objects 表
│   └── ...
└── extensions schema    ← Postgres 扩展
```
**关键理解**：
- `public` 是默认 schema，你建表不指定 schema 时就在这里
- Supabase 各组件各自有自己的 schema（`auth`、`storage`、`realtime` 等），互不干扰
- **PostgREST 默认只暴露 `public` schema 的内容作为 API**——`auth.users` 不会被直接暴露给客户端，这是安全设计
- 引用其他 schema 的表需要显式指定：`auth.users`、`storage.objects`
---
## 二、安全层
### Q2：安全模型？
Supabase 的安全模型是**三道防线的纵深防御**：
```
第 1 道 ── Kong (API Gateway)
           │  验证 API Key（anon key 或 service_role key）
           │  限流、路由
           ▼
第 2 道 ── GoTrue (Auth)
           │  签发和验证 JWT
           │  JWT 中携带用户身份信息
           ▼
第 3 道 ── PostgreSQL RLS
              根据 JWT 中的用户身份
              在数据库层逐行决定：这行数据，你能不能碰
```
传统应用中，安全逻辑写在后端代码里（Controller 层）。Supabase 把安全逻辑**下沉到数据库层**，这意味着：
- 不管请求从哪来（REST API、GraphQL、直连数据库），安全策略都生效
- 不可能因为"忘了在某个接口加权限校验"而出安全漏洞
- 安全策略就是 SQL，可以版本控制、可以测试
---
### Q3：RLS（Row Level Security）是什么？
RLS 是 **PostgreSQL 原生功能**（不是 Supabase 发明的），它让你对表的每一行设置"谁能看、谁能改"。
**没有 RLS 的世界**：
```sql
SELECT * FROM posts;  -- 返回所有人的所有帖子，无差别
```
**有 RLS 的世界**：
```sql
-- 第一步：开启 RLS
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;
-- 第二步：定义策略
CREATE POLICY "用户只能看自己的帖子"
  ON posts
  FOR SELECT                        -- 只影响 SELECT 操作
  USING (user_id = auth.uid());     -- 过滤条件
```
开启后，同样执行 `SELECT * FROM posts`：
- 用户 A 只能看到 `user_id = A` 的行
- 用户 B 只能看到 `user_id = B` 的行
- **数据库自动过滤，应用层完全无感**
`auth.uid()` 是 Supabase 提供的辅助函数，本质是：
```sql
-- 从当前会话的 JWT claims 中提取 sub（用户ID）字段
current_setting('request.jwt.claims', true)::json->>'sub'
```
PostgREST 在处理每个请求时，会把 JWT 的内容设置到 Postgres 会话变量中，RLS 策略就能读到当前用户是谁。
**策略可以很复杂**：
```sql
-- 管理员能看所有帖子，普通用户只能看自己的和公开的
CREATE POLICY "帖子可见性"
  ON posts FOR SELECT
  USING (
    auth.jwt()->>'role' = 'admin'      -- 管理员看全部
    OR user_id = auth.uid()            -- 自己的帖子
    OR is_public = true                -- 公开帖子
  );
```
---
### Q7：`anon` key 是什么？
Supabase 项目创建后会生成两个 API Key：

| Key | 身份 | 权限 | 使用场景 |
|---|---|---|---|
| **`anon`** | 匿名/公共 | 受 RLS 策略约束 | 前端代码、浏览器中使用 |
| **`service_role`** | 超级管理员 | **绕过所有 RLS** | 仅限后端服务器使用 |
`anon` key 是**可以安全暴露在客户端的**。它被嵌入你的前端 JS 代码中。
为什么安全？因为即使攻击者拿到 `anon` key，他能做的事情**完全由 RLS 策略决定**。如果你的 RLS 写的是"未登录用户什么都不能干"，那 `anon` key 就等于一张白纸。
```javascript
// 这行代码出现在前端，是安全的
const supabase = createClient(SUPABASE_URL, SUPABASE_ANON_KEY)
```
**`service_role` key 绝对不能出现在客户端代码中**——它能无视所有安全策略直接操作数据库。

---
### Q6：GoTrue 是什么？
GoTrue 是 Supabase 的**认证服务**，用 Go 语言编写，职责单一：**管理用户身份，签发 JWT**。
```
用户输入邮箱密码 → GoTrue 验证 → 签发 JWT Token
                                    │
                                    ▼
                         {
                           "sub": "uuid-of-user",    ← 用户ID
                           "email": "a@b.com",
                           "role": "authenticated",  ← 角色
                           "exp": 1735689600          ← 过期时间
                         }
```
**JWT（JSON Web Token）** 是一个自包含的令牌——服务端不需要查数据库就能验证它的真实性（通过密钥签名验证）。
GoTrue 支持的登录方式：密码、Magic Link、OTP、Google/GitHub/Apple 等 20+ 社交登录、SAML SSO。
**关键设计**：GoTrue 把用户数据直接存在你项目的 PostgreSQL 的 `auth` schema 中。这意味着你可以用外键、触发器把 auth 数据和你的业务数据打通。

---
## 三、API 与网关层
### Q5：Kong 是什么？
Kong 是一个基于 Nginx 的**开源 API 网关**。在 Supabase 中，它是**所有外部请求的入口**。
```
客户端的每个请求
    │
    ▼
  Kong 做三件事：
    1. 验证 API Key（anon / service_role）→ 无效则拒绝
    2. 路由分发：
       /rest/v1/*     → PostgREST
       /auth/v1/*     → GoTrue
       /realtime/v1/* → Realtime
       /storage/v1/*  → Storage API
       /functions/v1/* → Edge Functions
    3. 限流（Rate Limiting）
    │
    ▼
  对应的后端服务
```
你不需要直接与 Kong 交互。它是透明的基础设施层。但理解它的存在能帮你理解为什么所有 Supabase 服务共享同一个域名 `https://<project>.supabase.co`——因为 Kong 在前面统一接收，再按路径分发。

---
### Q4：PostgREST 是什么？
PostgREST 是用 Haskell 写的独立 Web 服务器，做一件事：**把 PostgreSQL 数据库自动变成 REST API**。
**工作原理**：
```
第 1 步（启动时）：读取 Postgres 的 information_schema
    "public schema 里有 posts 表，有 id/title/content/user_id 列"
    "posts.user_id 外键关联 profiles.id"
    "有一个函数 get_popular_posts()"
第 2 步（请求时）：HTTP → SQL 翻译
  GET /rest/v1/posts?select=title,profiles(name)&user_id=eq.123
                            │
                            ▼
  SELECT posts.title, profiles.name
  FROM posts
  JOIN profiles ON posts.user_id = profiles.id
  WHERE posts.user_id = '123';
第 3 步（schema 变更时）：自动刷新 API
    你加了一列 → API 立即可用
    你删了一张表 → API 立即消失
```
**性能关键**：每个 HTTP 请求解析为**单条 SQL 语句**。不是 ORM 那种 N+1 查询，是一条 SQL 解决问题。这就是"比 Firebase 快 300%"的原因。

---
## 四、实时与存储层
### Q9（第一个）：语法解析
```sql
-- ═══ 第一部分：创建函数 ═══
CREATE FUNCTION public.handle_new_user()  -- 在 public schema 中创建名为 handle_new_user 的函数
RETURNS trigger                           -- 返回类型是 trigger（只有触发器函数才能这样声明）
AS $$                                     -- $$ 是 Postgres 的"美元引用"，标记函数体的开始
                                          --（替代单引号，避免函数体内的引号转义地狱）
BEGIN                                     -- PL/pgSQL 语法：函数体开始
  INSERT INTO public.profiles (id, email) -- 向 profiles 表插入一行
  VALUES (new.id, new.email);             -- new 是触发器的魔法变量：
                                          --   代表"刚刚被 INSERT 的那行数据"
                                          --   这里 new.id 和 new.email 来自 auth.users 表
  RETURN new;                             -- 触发器函数必须返回 new（AFTER 触发器中
                                          --   返回值实际不影响结果，但语法要求）
END;                                      -- 函数体结束
$$ LANGUAGE plpgsql                       -- 声明使用 PL/pgSQL 语言（Postgres 内置过程语言）
   SECURITY DEFINER;                      -- ⚠️ 关键：以函数创建者的权限执行，而非调用者
                                          --   因为调用者（GoTrue）可能没有 profiles 表的写权限
                                          --   SECURITY DEFINER 相当于 Linux 的 setuid
-- ═══ 第二部分：创建触发器 ═══
CREATE TRIGGER on_auth_user_created       -- 触发器名称
  AFTER INSERT                            -- 在 INSERT 操作之后触发
  ON auth.users                           -- 监听 auth.users 表（GoTrue 的用户表）
  FOR EACH ROW                            -- 每插入一行就触发一次（而非每条语句触发一次）
  EXECUTE PROCEDURE public.handle_new_user(); -- 触发时执行上面创建的函数
```
**整体效果**：每当 GoTrue 创建新用户（INSERT 到 `auth.users`），自动在 `public.profiles` 表中创建对应记录。这是 Supabase 中**连接 auth 系统和业务数据的标准模式**。
**`SECURITY DEFINER` 的精妙之处**：普通用户通过 API 注册时，RLS 可能阻止他们直接写 `profiles` 表。但这个函数以创建者（通常是数据库 owner）的身份执行，绕过了 RLS。这是一种受控的提权——只在这个特定函数内生效。

---
### Q9（第二个）：Realtime
Supabase Realtime 是一个用 **Elixir**（基于 OTP/Phoenix）编写的 WebSocket 服务器。
**为什么选 Elixir？** Elixir 的 BEAM 虚拟机天生为高并发、低延迟通信设计。WhatsApp 的前身 Erlang 和它同源。每个 WebSocket 连接是一个轻量级进程（不是系统线程），单节点可以扛数十万连接。
**三种功能的底层机制不同**：
```
┌─ Broadcast ──────────────────────────────────┐
│  纯消息转发，不经过数据库                        │
│  Client A → Realtime Server → Client B/C/D    │
│  用途：光标位置、打字状态、游戏事件               │
└──────────────────────────────────────────────┘
┌─ Presence ───────────────────────────────────┐
│  CRDT（无冲突复制数据类型）实现                  │
│  服务端维护"谁在线"状态，自动处理断连/重连        │
│  用途：在线用户列表、协作编辑器的参与者显示        │
└──────────────────────────────────────────────┘
┌─ Postgres Changes ───────────────────────────┐
│  基于 PostgreSQL 逻辑复制（Logical Replication）│
│  Postgres WAL → Realtime 解析 → 推送到客户端   │
│  用途：实时仪表盘、数据变更通知                   │
│  ⚠️ 受 RLS 约束——只推送用户有权看到的变更        │
└──────────────────────────────────────────────┘
```
---
### Q10：S3 兼容协议 + TUS
**S3 兼容协议**
Amazon S3（Simple Storage Service）定义了一套对象存储的 HTTP API 标准，已经成为事实上的行业标准。"S3 兼容"意味着：
- 你可以用任何 S3 客户端/SDK（如 AWS SDK、MinIO Client、Cyberduck）直接连 Supabase Storage
- 迁移成本极低——从 AWS S3 迁到 Supabase（或反过来）只需改一个 endpoint URL
- 生态中大量工具直接可用（备份工具、CDN 集成、数据管道）
**TUS 协议**
TUS（**T**he **U**pload **S**ystem 的缩写，但实际是拉丁语"盾牌"）是一个**断点续传上传协议**。
传统上传的问题：上传一个 2GB 视频，传到 1.8GB 时网络断了，只能重头再来。
TUS 的解决方案：
```
客户端                         服务端
  │── POST /upload ──────────→│  创建上传，返回 upload-url
  │── PATCH (bytes 0-500MB) ─→│  保存前 500MB
  │── PATCH (500MB-1GB) ─────→│  保存到 1GB
  │   ✕ 网络断开
  │   ...重新连接...
  │── HEAD /upload-url ──────→│  "你已上传到 1GB"
  │── PATCH (1GB-2GB) ───────→│  从断点继续！
  │                           │  ✓ 上传完成
```
Supabase Storage 同时支持这两种协议，你可以根据场景选择：
- 小文件（<6MB）→ 普通 REST 上传
- 大文件 → TUS 断点续传
---
### Q11：Edge Functions
**"Edge"是什么意思？**
传统服务器部署在某个固定机房（比如 us-east-1）。"边缘"（Edge）指的是把代码部署到**全球数百个节点**，用户请求自动路由到最近的节点执行。
```
传统：  上海用户 ──── 300ms ────→ 美国服务器 ──── 300ms ────→ 响应
Edge：  上海用户 ──── 5ms ─────→ 上海边缘节点 ──── 5ms ─────→ 响应
```
**Supabase Edge Functions 的本质**：
- **运行时**：Deno（不是 Node.js）。Deno 由 Node.js 创始人重新设计，TypeScript 原生支持、默认安全沙箱
- **部署方式**：写一个 `.ts` 文件，`supabase functions deploy` 即可全球部署
- **执行模型**：无状态、短生命周期（类似 AWS Lambda，但延迟更低因为在边缘）
**适合做什么**：需要服务端逻辑但不想维护服务器的场景——处理 Webhook、调用第三方 API（Stripe/OpenAI）、生成 OG 图片、发邮件。
**不适合做什么**：长时间计算、重度数据库操作（此时应该用传统服务器 + `service_role` key 连 Supabase）。

---
## 📌 概念依赖图（总览）
```
PostgreSQL (Q1/Q12)
    │
    ├── Schema (Q8)          ← 命名空间，组织表和函数
    │
    ├── RLS (Q3)             ← 行级安全，数据库层权限控制
    │     │
    │     └── 依赖 GoTrue 签发的 JWT 来识别"当前用户是谁"
    │
    ├── 触发器 + 函数 (Q9)    ← 数据库内事件驱动逻辑
    │
    └── 逻辑复制              ← Realtime (Q9) 的数据源
          │
          └── Realtime 监听 WAL 变更
GoTrue (Q6)                  ← 认证服务，签发 JWT
    │
    └── JWT 被 Kong、PostgREST、RLS 消费
Kong (Q5)                    ← API 网关，所有请求的入口
    │
    ├── 验证 anon/service_role key (Q7)
    │
    └── 路由到 → PostgREST (Q4) / GoTrue / Realtime / Storage / Edge Functions
PostgREST (Q4)               ← 数据库 → REST API 自动转换
Storage                      ← S3 + TUS (Q10)
Edge Functions (Q11)         ← 边缘计算，服务端逻辑
安全模型 (Q2) = Kong + GoTrue + RLS 三层纵深
```