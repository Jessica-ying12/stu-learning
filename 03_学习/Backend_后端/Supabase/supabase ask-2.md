
# Supabase 进阶概念逐个击破
依然按依赖顺序：先懂底层，再懂上层。
---
## Q6：SQL 语法详解
这个放最前面，因为后续很多答案都需要 SQL 基础。
### SQL 是什么
SQL（Structured Query Language）是与关系型数据库对话的**唯一语言**。不管你用什么编程语言（JS/Python/Go），最终到达数据库的都是 SQL。
### 四大核心操作（CRUD）
```sql
-- ═══ CREATE（创建） ═══
INSERT INTO posts (title, content, user_id)
VALUES ('我的第一篇', '内容...', 'uuid-123');   // 第一段值
--      表名        列名列表          值列表
-- ═══ READ（读取） ═══
SELECT title, content          -- 要哪些列（* 代表全部）
FROM posts                     -- 从哪张表
WHERE user_id = 'uuid-123'    -- 过滤条件
ORDER BY created_at DESC       -- 排序（DESC 降序，ASC 升序）
LIMIT 10                       -- 只要前 10 条
OFFSET 20;                     -- 跳过前 20 条（分页用）
-- ═══ UPDATE（更新） ═══
UPDATE posts
SET title = '修改后的标题', content = '新内容'  -- 要改的列和值
WHERE id = 'post-uuid-456';                    -- 一定要有 WHERE！
                                               -- 不加 WHERE 会更新整张表的所有行
-- ═══ DELETE（删除） ═══
DELETE FROM posts
WHERE id = 'post-uuid-456';   -- 同样，不加 WHERE 会删除所有行
```
### 建表语法
```sql
CREATE TABLE posts (
  -- 列名       类型            约束
  id          UUID            DEFAULT gen_random_uuid() PRIMARY KEY,
  --          ↑通用唯一标识    ↑默认值：自动生成         ↑主键：唯一标识每行
  title       TEXT            NOT NULL,
  --          ↑任意长度文本    ↑不允许为空
  content     TEXT,
  --                          没写 NOT NULL = 允许为空
  user_id     UUID            NOT NULL REFERENCES auth.users(id),
  --                                    ↑外键：这个值必须在 auth.users 表的 id 列中存在
  is_public   BOOLEAN         DEFAULT false,
  --          ↑布尔值          ↑默认为 false
  created_at  TIMESTAMPTZ     DEFAULT now()
  --          ↑带时区的时间戳   ↑默认为当前时间
);
```
### JOIN（关联查询）
```sql
-- 查询帖子同时获取作者名字
SELECT posts.title, profiles.name
FROM posts
JOIN profiles ON posts.user_id = profiles.id;
--   ↑要关联的表  ↑关联条件：posts 的 user_id 等于 profiles 的 id
-- JOIN 类型：
-- INNER JOIN (= JOIN)：只返回两边都匹配的行
-- LEFT JOIN：左表全部返回，右表没匹配的填 NULL
-- RIGHT JOIN：反过来
-- FULL JOIN：两边都全返回
```
### 常用类型速查
| 类型 | 说明 | 示例 |
|---|---|---|
| `UUID` | 通用唯一标识符 | `'a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11'` |
| `TEXT` | 任意长度字符串 | `'hello world'` |
| `VARCHAR(n)` | 限长字符串 | `'hello'`（最多 n 个字符） |
| `INTEGER` / `BIGINT` | 整数 | `42` / `9999999999` |
| `BOOLEAN` | 布尔 | `true` / `false` |
| `TIMESTAMPTZ` | 带时区时间 | `'2026-03-25T10:30:00+08:00'` |
| `JSONB` | 二进制 JSON | `'{"key": "value"}'` |
| `UUID[]` | UUID 数组 | `'{uuid1, uuid2}'` |

---
## Q4：RESTful API 是什么
REST（Representational State Transfer）是一种 **API 设计风格**，核心思想是：**用 HTTP 方法 + URL 路径来表达对资源的操作**。
```
动词（HTTP 方法）  +  名词（URL 路径）  =  操作
GET     /posts              → 获取帖子列表
GET     /posts/123          → 获取 id=123 的帖子
POST    /posts              → 创建新帖子
PATCH   /posts/123          → 修改 id=123 的帖子（部分更新）
PUT     /posts/123          → 替换 id=123 的帖子（全量更新）
DELETE  /posts/123          → 删除 id=123 的帖子
```
**对应到 Supabase**：PostgREST 自动把你的每张表变成一个 REST 端点：
```
你的表 posts  →  https://<project>.supabase.co/rest/v1/posts
GET    /rest/v1/posts?select=title,content&limit=10
POST   /rest/v1/posts   (body: {"title": "...", "content": "..."})
PATCH  /rest/v1/posts?id=eq.123   (body: {"title": "新标题"})
DELETE /rest/v1/posts?id=eq.123
```
**用 Supabase SDK 时你感知不到 REST**——SDK 把 HTTP 调用封装了：
```javascript
// 这行代码背后是 GET /rest/v1/posts?select=*&order=created_at.desc&limit=10
const { data } = await supabase
  .from('posts')
  .select('*')
  .order('created_at', { ascending: false })
  .limit(10)
```
---
## Q5：ORM 和 N+1 问题
### ORM 是什么
ORM（Object-Relational Mapping）是一个**把数据库表映射成编程语言对象**的工具，让你"不写 SQL 也能操作数据库"。
```python
# 没有 ORM：手写 SQL
cursor.execute("SELECT * FROM posts WHERE user_id = %s", [user_id])
# 有 ORM（以 Python SQLAlchemy 为例）：写对象方法
posts = session.query(Post).filter(Post.user_id == user_id).all()
# 有 ORM（以 JS Prisma 为例）：
const posts = await prisma.post.findMany({ where: { userId: userId } })
```
常见 ORM：Prisma（JS/TS）、SQLAlchemy（Python）、Hibernate（Java）、ActiveRecord（Ruby）。
### N+1 问题
假设你要获取 10 篇帖子以及每篇帖子的作者名字：
**ORM 的天真做法（N+1）**：
```
查询 1：SELECT * FROM posts LIMIT 10          → 获得 10 篇帖子
查询 2：SELECT * FROM profiles WHERE id = 'user-A'  → 帖子1 的作者
查询 3：SELECT * FROM profiles WHERE id = 'user-B'  → 帖子2 的作者
查询 4：SELECT * FROM profiles WHERE id = 'user-C'  → 帖子3 的作者
...
查询 11：SELECT * FROM profiles WHERE id = 'user-J' → 帖子10 的作者
总计：1 + 10 = 11 次数据库查询（即 N+1，N=10）
```
**PostgREST 的做法（1 次查询）**：
```
GET /rest/v1/posts?select=title,profiles(name)&limit=10
    ↓ 翻译为单条 SQL ↓
SELECT posts.title, profiles.name
FROM posts
JOIN profiles ON posts.user_id = profiles.id
LIMIT 10;
总计：1 次数据库查询
```
**这就是为什么 PostgREST 比传统 ORM 快得多**——它把每个 HTTP 请求解析为单条 SQL，利用 Postgres 的 JOIN 能力在数据库内部完成关联，而不是在应用层逐条查询。

---
## Q1：RLS 每张表都要自己写吗？
**是的，每张表独立控制。** 这是设计特性，不是缺陷。
### 为什么不能"全局设置一下"？
因为不同表的权限逻辑天然不同：
```sql
-- posts 表：用户能看自己的和公开的
CREATE POLICY "posts_select" ON posts FOR SELECT
USING (user_id = auth.uid() OR is_public = true);
-- profiles 表：所有登录用户都能看所有人的 profile
CREATE POLICY "profiles_select" ON profiles FOR SELECT
USING (auth.role() = 'authenticated');
-- private_messages 表：只有发送者和接收者能看
CREATE POLICY "messages_select" ON private_messages FOR SELECT
USING (sender_id = auth.uid() OR receiver_id = auth.uid());
```
### 实际操作流程
每张表需要两步：
```sql
-- 第一步：开启 RLS（不开启 = 所有人都能访问所有数据）
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;
-- 第二步：针对每种操作写策略
CREATE POLICY "名字随便起" ON posts FOR SELECT USING (...);
CREATE POLICY "名字随便起" ON posts FOR INSERT WITH CHECK (...);
CREATE POLICY "名字随便起" ON posts FOR UPDATE USING (...) WITH CHECK (...);
CREATE POLICY "名字随便起" ON posts FOR DELETE USING (...);
```
**`USING`** = 过滤已有行（"你能看到/改动/删除哪些行"）
**`WITH CHECK`** = 验证新数据（"你插入/更新后的数据是否合法"）
### 常见模式（直接复制用）
```sql
-- 模式 1：用户只能操作自己的数据（最常见）
CREATE POLICY "自己的数据" ON my_table
FOR ALL USING (user_id = auth.uid())
WITH CHECK (user_id = auth.uid());
-- 模式 2：所有登录用户可读，只有作者能写
CREATE POLICY "公开读" ON my_table FOR SELECT
USING (auth.role() = 'authenticated');
CREATE POLICY "作者写" ON my_table FOR INSERT
WITH CHECK (user_id = auth.uid());
-- 模式 3：完全公开（不需要登录就能读）
CREATE POLICY "匿名可读" ON my_table FOR SELECT
USING (true);  -- true = 无条件放行
```
**⚠️ 铁律**：开了 RLS 但没写任何 POLICY = **谁都访问不了**（默认拒绝）。这是安全设计——宁可断流也不泄露。

---
## Q2：`sub` 字段和 `current_setting` 是什么
逐段拆解：
```sql
current_setting('request.jwt.claims', true)::json->>'sub'
--     ↑①            ↑②                ↑③    ↑④    ↑⑤
```

| 部分 | 含义 |
|---|---|
| ① `current_setting()` | Postgres 内置函数，读取当前会话的配置变量（类似读环境变量） |
| ② `'request.jwt.claims'` | 变量名。PostgREST 在处理每个请求时，把 JWT 的完整 payload 写入这个变量 |
| ③ `true` | "如果变量不存在，返回 NULL 而不是报错"（容错） |
| ④ `::json` | 类型转换——`current_setting` 返回的是 TEXT，转成 JSON 才能用 JSON 操作符 |
| ⑤ `->>'sub'` | JSON 操作符，提取 `sub` 键的值（作为 TEXT 返回） |
**`sub` 是 JWT 标准字段**，全称 "subject"，在 Supabase 中 = **用户的 UUID**。
一个典型的 Supabase JWT payload 长这样：
```json
{
  "sub": "a1b2c3d4-e5f6-7890-abcd-ef1234567890",  ← 用户 ID
  "email": "user@example.com",
  "role": "authenticated",                          ← 角色
  "aud": "authenticated",
  "iat": 1711350000,                                ← 签发时间
  "exp": 1711353600                                 ← 过期时间
}
```
**`auth.uid()`** 就是对上面那行表达式的简写封装：
```sql
-- Supabase 预置的辅助函数，本质就是：
CREATE FUNCTION auth.uid() RETURNS UUID AS $$
  SELECT (current_setting('request.jwt.claims', true)::json->>'sub')::uuid;
$$ LANGUAGE sql STABLE;
```
所以 `auth.uid() = user_id` 和 `current_setting('request.jwt.claims', true)::json->>'sub' = user_id::text` 是等价的，前者只是更好读。

---
## Q3：如何给列加索引
### 为什么 RLS 需要索引
```sql
CREATE POLICY "自己的数据" ON posts FOR SELECT
USING (user_id = auth.uid());
```
这条策略让 Postgres 在每次查询 posts 时追加条件 `WHERE user_id = <当前用户UUID>`。如果 `user_id` 列**没有索引**：
```
无索引：逐行扫描整张表 → 100 万行就检查 100 万次 → 慢
有索引：直接跳到匹配的行 → 只访问属于你的那几十行 → 快
```
### 创建索引的语法
```sql
-- 基础索引（最常用，B-tree 类型）
CREATE INDEX idx_posts_user_id ON posts (user_id);
--           ↑索引名（随意但要有意义）  ↑表名   ↑列名
-- 复合索引（多列）
CREATE INDEX idx_posts_user_created ON posts (user_id, created_at DESC);
-- 当你经常按 user_id 过滤 + created_at 排序时使用
-- 唯一索引（保证列值不重复）
CREATE UNIQUE INDEX idx_profiles_email ON profiles (email);
-- 部分索引（只索引满足条件的行，更小更快）
CREATE INDEX idx_posts_public ON posts (created_at)
WHERE is_public = true;
-- 只索引公开帖子，如果你经常查 "所有公开帖子按时间排序"
```
### 在 Supabase Dashboard 中操作
不想写 SQL 也行——Dashboard 中表的编辑界面可以直接添加索引。但**推荐用 SQL**，因为可以版本控制。
### 索引类型速查
| 类型             | 适用场景                              |
| -------------- | --------------------------------- |
| **B-tree**（默认） | 等值查询 `=`、范围 `<` `>`、排序 `ORDER BY` |
| **GIN**        | JSONB 字段查询、全文搜索、数组包含              |
| **GiST**       | 地理空间（PostGIS）、范围类型                |
| **HNSW**       | 向量相似度搜索（pgvector）                 |
**经验法则**：RLS 策略中 `USING(...)` 里引用的每一列，都应该有索引。

---
## Q7：RPC 是什么
RPC（Remote Procedure Call，远程过程调用）在 Supabase 语境中指**通过 API 调用 Postgres 函数**。
### 为什么需要 RPC
PostgREST 自动生成的 CRUD API 覆盖 90% 的场景。但有些操作不是简单的 CRUD：
- 一次操作涉及多张表的事务
- 复杂的计算逻辑
- 需要数据库原子性保证的操作
这时你在 Postgres 中写一个函数，然后通过 RPC 调用它。
### 示例
```sql
-- 在 Postgres 中创建函数
CREATE FUNCTION get_user_stats(target_user_id UUID)
RETURNS JSON AS $$
  SELECT json_build_object(
    'post_count', (SELECT count(*) FROM posts WHERE user_id = target_user_id),
    'comment_count', (SELECT count(*) FROM comments WHERE user_id = target_user_id),
    'latest_post', (SELECT title FROM posts WHERE user_id = target_user_id
                    ORDER BY created_at DESC LIMIT 1)
  );
$$ LANGUAGE sql STABLE;
```
```javascript
// 在客户端通过 RPC 调用
const { data } = await supabase.rpc('get_user_stats', {
  target_user_id: 'uuid-123'
})
// data = { post_count: 42, comment_count: 128, latest_post: "..." }
```
**底层请求**：`POST /rest/v1/rpc/get_user_stats`
**RPC 也受 RLS 约束**——函数内部的每个查询都走 RLS（除非函数声明了 `SECURITY DEFINER`）。

---
## Q8：函数一般写在哪里
Postgres 函数存在**数据库内部**，不是文件系统中的文件。但在实际开发中，你通过以下方式管理它们：
### 方式一：Supabase Dashboard 的 SQL Editor
打开 Dashboard → SQL Editor → 写 SQL → 点 Run。适合快速实验。
### 方式二：Migration 文件（推荐生产使用）
```bash
# 用 Supabase CLI 创建 migration
supabase migration new create_user_stats_function
# 这会在 supabase/migrations/ 下生成一个时间戳文件：
# 20260325100000_create_user_stats_function.sql
```
在该文件中写你的函数：
```sql
-- supabase/migrations/20260325100000_create_user_stats_function.sql
CREATE OR REPLACE FUNCTION get_user_stats(target_user_id UUID)
RETURNS JSON AS $$
  ...
$$ LANGUAGE sql STABLE;
```
```bash
# 应用到本地数据库
supabase db reset    # 重置并重跑所有 migration
# 应用到远程（生产）数据库
supabase db push
```
### 文件结构
```
your-project/
├── supabase/
│   ├── migrations/                    ← 所有数据库变更都在这里
│   │   ├── 20260301_create_tables.sql
│   │   ├── 20260302_create_policies.sql
│   │   ├── 20260303_create_functions.sql
│   │   └── 20260325_create_user_stats.sql
│   ├── functions/                     ← Edge Functions（不同于 Postgres 函数！）
│   │   └── hello/
│   │       └── index.ts
│   └── config.toml
├── src/                               ← 你的前端代码
└── package.json
```
**⚠️ 区分两种"函数"**：
- **Postgres 函数**：写在 migration SQL 文件中，运行在数据库内部，通过 RPC 调用
- **Edge Functions**：写在 `supabase/functions/` 目录中的 TypeScript 文件，运行在 Deno 边缘节点
---
## Q9：如何开始创建第一个数据库
### 方案一：云端（最快，2 分钟）
```
1. 打开 https://supabase.com/dashboard
2. 点 "New Project"
3. 选地区（选离你用户最近的，如 Southeast Asia - Singapore）
4. 设置数据库密码
5. 等 ~60 秒
6. 完成 —— 你已经有了：
   ✓ 一个完整的 PostgreSQL 数据库
   ✓ REST API（PostgREST）
   ✓ 认证服务（GoTrue）
   ✓ 实时服务（Realtime）
   ✓ 文件存储（Storage）
   ✓ API Key（在 Settings → API 中查看）
```
### 方案二：本地开发（推荐日常开发用）
```bash
# 1. 安装 Supabase CLI
npm install -g supabase
# 2. 初始化项目
mkdir my-project && cd my-project
supabase init
# 3. 启动本地 Supabase（需要 Docker）
supabase start
# 输出：
#   API URL: http://127.0.0.1:54321
#   DB URL: postgresql://postgres:postgres@127.0.0.1:54322/postgres
#   Studio URL: http://127.0.0.1:54323   ← 本地 Dashboard
#   Anon key: eyJhbG...
#   Service role key: eyJhbG...
```
### 创建第一张表
在 Dashboard 的 SQL Editor（或本地 Studio）中：
```sql
-- 创建 profiles 表
CREATE TABLE profiles (
  id UUID PRIMARY KEY REFERENCES auth.users(id),
  name TEXT,
  avatar
测到 **研究/理解** 意图 — 用户继续对 Supabase/PostgreSQL 核心概念进行追问，从 RLS 实操、SQL 语法、到开发流程。所有问题基于前两轮已深入调研的知识体系，无需额外检索。
这是一个纯分析/解释类任务，所有概念在前两轮已充分覆盖。无需多模型协作，无需外部检索。直接综合输出。
---
# Supabase 进阶概念逐层拆解（第三轮）
按依赖顺序组织——先理解基础概念，后面的概念自然水到渠成。
---
``````
## Q4：RESTful API 是什么？
这个是后续所有问题的前置概念，先讲。
**REST**（Representational State Transfer）是一种 **API 设计风格**，不是协议，不是框架，只是一组约定。核心思想：**用 HTTP 动词 +
> 我检测到 **研究/理解** 意图 — 用户继续深入追问 Supabase/PostgreSQL 的核心概念，都是基于前两轮已充分调研的内容。我的方案：直接综合输出，无需额外检索或多模型协作。
这是纯解释类任务，所有问题的答案都在前两轮已掌握的知识范围内，无需再次检索或协作。
按依赖顺序逐一拆解。
---
# 深入追问：逐层解答
## Q4：RESTful API 是什么？
先解决这个，因为后面多个问题会引用它。
**REST**（Representational State Transfer）是一种 **API 设计风格**，核心思想：**用 HTTP 动词 + URL 路径描述对资源的操作**。
```
动词        含义        示例 URL              等价 SQL
────────────────────────────────────────────────────────
GET         读取        /posts                SELECT * FROM posts
GET         读单条      /posts?id=eq.5        SELECT * FROM posts WHERE id = 5
POST        创建        /posts                INSERT INTO posts ...
PATCH       部分更新    /posts?id=eq.5        UPDATE posts SET ... WHERE id = 5
DELETE      删除        /posts?id=eq.5        DELETE FROM posts WHERE id = 5
```
**"RESTful"** 就是遵循这套约定的 API。PostgREST 之所以叫这个名字，就是因为它把 **Postgres** 自动变成 **RESTful** API。
对比非 RESTful 的设计（反例）：
```
POST /api/getPostById        ← 用 POST 做查询，动词错误
POST /api/deletePost         ← URL 里放动词，不是资源名
GET  /api/doSomething?action=update  ← 混乱
```
---
## Q5：ORM 是什么？N+1 查询问题？
### ORM（Object-Relational Mapping）
ORM 是一个**翻译层**，让你用编程语言的对象操作数据库，而不用写 SQL。
```python
# 不用 ORM（直接写 SQL）
cursor.execute("SELECT * FROM posts WHERE user_id = 123")
# 用 ORM（Python SQLAlchemy 示例）
posts = session.query(Post).filter(Post.user_id == 123).all()
# 用 ORM（JavaScript Prisma 示例）
const posts = await prisma.post.findMany({ where: { userId: 123 } })
```
看起来很方便，但问题来了——
### N+1 查询问题
场景：你要获取 10 篇帖子，每篇帖子要显示作者名字。
**ORM 常见的笨办法**：
```
第 1 次查询：SELECT * FROM posts LIMIT 10          ← 得到 10 篇帖子
第 2 次查询：SELECT * FROM users WHERE id = 帖子1的作者id
第 3 次查询：SELECT * FROM users WHERE id = 帖子2的作者id
第 4 次查询：SELECT * FROM users WHERE id = 帖子3的作者id
...
第 11 次查询：SELECT * FROM users WHERE id = 帖子10的作者id
总计：1（查帖子）+ 10（查每个作者）= 11 次查询 → 这就是 "N+1"
```
**正确做法（一条 SQL）**：
```sql
SELECT posts.*, users.name
FROM posts
JOIN users ON posts.user_id = users.id
LIMIT 10;
-- 1 次查询搞定
```
**PostgREST 为什么没有 N+1 问题？** 因为它**不是 ORM**。它把每个 HTTP 请求直接翻译为**一条 SQL**，用 JOIN 处理关联：
```
GET /posts?select=*,users(name)&limit=10
→ 单条 SQL，一次往返，结束。
```
---
## Q6：SQL 语法详细解析
以 Supabase 中最常见的操作为例，逐句拆解：
### 建表
```sql
CREATE TABLE public.posts (          -- 在 public schema 中创建 posts 表
  id          UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  --          ^^^^                           ^^^^^^^^^^^
  --          数据类型：通用唯一标识符          主键约束（唯一且非空）
  --               ^^^^^^^^^^^^^^^^^^^^^^^^
  --               默认值：自动生成随机 UUID
  title       TEXT NOT NULL,          -- 文本类型，不允许为空
  content     TEXT,                   -- 文本类型，允许为空（NULL）
  user_id     UUID REFERENCES auth.users(id),
  --               ^^^^^^^^^^^^^^^^^^^^^^^^^^
  --               外键约束：user_id 必须是 auth.users 表中存在的 id
  is_public   BOOLEAN DEFAULT false,  -- 布尔类型，默认 false
  created_at  TIMESTAMPTZ DEFAULT now()
  --          ^^^^^^^^^^^            ^^^^^
  --          带时区的时间戳           默认值：当前时间
);
```
### 增删改查（CRUD）
```sql
-- ═══ INSERT（创建）═══
INSERT INTO posts (title, content, user_id)
VALUES ('Hello', 'World', 'some-uuid');
-- 不需要填 id、created_at，因为有默认值
-- ═══ SELECT（查询）═══
SELECT title, content           -- 要哪些列
FROM posts                      -- 从哪张表
WHERE is_public = true          -- 过滤条件
  AND created_at > '2025-01-01' -- 可以叠加多个条件
ORDER BY created_at DESC        -- 排序：最新的在前
LIMIT 20                        -- 只取 20 条
OFFSET 40;                      -- 跳过前 40 条（分页用）
-- ═══ JOIN（关联查询）═══
SELECT
  posts.title,
  users.email                   -- 从另一张表取数据
FROM posts
INNER JOIN auth.users AS users  -- INNER JOIN = 两边都要有匹配
  ON posts.user_id = users.id   -- 关联条件
WHERE posts.is_public = true;
-- ═══ UPDATE（更新）═══
UPDATE posts
SET title = 'New Title',        -- 要改的列和新值
    content = 'New Content'
WHERE id = 'some-uuid';         -- 一定要有 WHERE，否则更新全表！
-- ═══ DELETE（删除）═══
DELETE FROM posts
WHERE id = 'some-uuid';         -- 同样，没有 WHERE = 删全表
```
### 常见运算符
```sql
=           等于
!=  或 <>   不等于
>  <  >=  <=  比较
AND  OR  NOT  逻辑
IN (a, b, c)  在列表中
LIKE '%word%'  模糊匹配（% = 任意字符）
IS NULL       为空
IS NOT NULL   非空
BETWEEN a AND b  范围
```
---
## Q2：`sub` 字段和 `current_setting` 是什么意思？
### JWT 的 `sub` 字段
JWT（JSON Web Token）有一组标准字段（称为 **claims**），`sub` 是其中之一：
```json
{
  "sub": "d0714948-a2b5-4321-8a6c-e01234567890",  // Subject - 主体，即用户ID
  "email": "user@example.com",
  "role": "authenticated",
  "iat": 1735689600,    // Issued At - 签发时间
  "exp": 1735693200     // Expiration - 过期时间
}
```
`sub`（Subject）是 JWT 标准（RFC 7519）规定的字段名，表示"这个 token 代表谁"。在 Supabase 中，`sub` 的值就是 `auth.users` 表中的 `id`。
### `current_setting(...)` 语法逐层拆解
```sql
current_setting('request.jwt.claims', true)::json->>'sub'
```
从右往左读：
```
current_setting('request.jwt.claims', true)
^^^^^^^^^^^^^^^^                      ^^^^
Postgres 内置函数：                    true = 如果设置不存在，
读取当前会话的配置变量                   返回 NULL 而不是报错
返回值（字符串）：
'{"sub":"d071...","email":"user@example.com","role":"authenticated"}'
```
```
... ::json
    ^^^^^^
类型转换：把字符串转成 JSON 对象
```
```
... ->>'sub'
    ^^^^^^^^
JSON 操作符：提取 'sub' 键的值，返回文本
->>  返回文本（text）
->   返回 JSON 对象（如果值是嵌套 JSON）
```
**完整流程**：
```
PostgREST 收到请求
    → 解析 HTTP Header 中的 JWT
    → SET request.jwt.claims = '{"sub":"...","role":"..."}'  -- 写入会话变量
    → 执行 SQL 查询
    → RLS 策略中 auth.uid() 被调用
    → auth.uid() 内部执行 current_setting('request.jwt.claims',true)::json->>'sub'
    → 返回 'd0714948-a2b5-4321-...'
    → RLS 用这个值和每行的 user_id 比较
```
---
## Q1：RLS 每张表都要自己写吗？
**是的，每张表需要独立开启和编写策略。** 但没有想象中那么痛苦。
### 为什么不能"全局生效"？
因为每张表的权限逻辑不同：
```
posts 表    → 公开帖子谁都能看，私密帖子只有作者能看
profiles 表 → 所有人都能看，但只有本人能改
messages 表 → 只有发送者和接收者能看
admin_logs  → 只有 admin 角色能看
```
没有一条策略能通吃所有表。
### 但有快速模板
```sql
-- ═══ 模板 1：仅限本人（最常见）═══
ALTER TABLE my_table ENABLE ROW LEVEL SECURITY;
CREATE POLICY "本人可读"   ON my_table FOR SELECT USING (user_id = auth.uid());
CREATE POLICY "本人可写"   ON my_table FOR INSERT WITH CHECK (user_id = auth.uid());
CREATE POLICY "本人可改"   ON my_table FOR UPDATE USING (user_id = auth.uid());
CREATE POLICY "本人可删"   ON my_table FOR DELETE USING (user_id = auth.uid());
-- ═══ 模板 2：公开读，仅本人写 ═══
CREATE POLICY "公开可读"   ON my_table FOR SELECT USING (true);  -- true = 所有人
CREATE POLICY "本人可写"   ON my_table FOR INSERT WITH CHECK (user_id = auth.uid());
-- ═══ 模板 3：彻底锁死（仅服务端 service_role 能访问）═══
ALTER TABLE secrets ENABLE ROW LEVEL SECURITY;
-- 不创建任何策略 → 默认拒绝所有通过 anon/authenticated 角色的访问
-- service_role 绕过 RLS，所以你的后端仍可访问
```
### Supabase Dashboard 提供可视化编辑
你不必手写 SQL，Dashboard 的 **Authentication → Policies** 页面有图形界面让你点选创建策略。但理解底层 SQL 仍然重要——复杂策略终究要手写。

---
## Q3：如何为 RLS 引用的列加索引？
### 为什么需要？
```sql
CREATE POLICY "本人可读" ON posts
  FOR SELECT USING (user_id = auth.uid());
--                   ^^^^^^^
-- 每次 SELECT 都会对每一行执行这个比较
-- 如果 user_id 没有索引 → 全表扫描（O(n)）
-- 如果 user_id 有索引 → 索引查找（O(log n)）
```
### 加索引的语法
```sql
-- 最基本的索引（B-tree，默认类型）
CREATE INDEX idx_posts_user_id ON posts (user_id);
--           ^^^^^^^^^^^^^^^^^    ^^^^^  ^^^^^^^
--           索引名（自定义）      表名    列名
-- 验证索引是否生效
EXPLAIN ANALYZE SELECT * FROM posts WHERE user_id = 'some-uuid';
-- 输出中看到 "Index Scan" 就对了
-- 如果看到 "Seq Scan" 说明没用上索引
```
### 常见索引场景
```sql
-- 外键列（几乎总是需要索引）
CREATE INDEX idx_posts_user_id ON posts (user_id);
CREATE INDEX idx_comments_post_id ON comments (post_id);
-- 复合索引（多列联合查询时）
CREATE INDEX idx_posts_user_public ON posts (user_id, is_public);
-- 部分索引（只索引满足条件的行，更小更快）
CREATE INDEX idx_posts_public ON posts (created_at)
  WHERE is_public = true;
-- 只有公开帖子会被索引，私密帖子不占空间
-- 唯一索引（保证列值不重复）
CREATE UNIQUE INDEX idx_profiles_email ON profiles (email);
```
### 在 Supabase Dashboard 中
**Table Editor → 选择表 → 点击列名旁的索引图标**，或者直接在 **SQL Editor** 中执行上述语句。

---
## Q7：RPC 是什么？
**RPC**（Remote Procedure Call，远程过程调用）在 Supabase 语境中，指的是**通过 API 调用 PostgreSQL 函数**。
```sql
-- 第一步：在数据库中创建函数
CREATE FUNCTION get_popular_posts(min_likes INT DEFAULT 10)
RETURNS SETOF posts           -- 返回 posts 表结构的行集
LANGUAGE sql                  -- 纯 SQL 语法（也可以用 plpgsql）
AS $$
  SELECT * FROM posts
  WHERE likes >= min_likes
  ORDER BY likes DESC
  LIMIT 20;
$$;
```
```javascript
// 第二步：从客户端调用（supabase-js）
const { data, error } = await supabase
  .rpc('get_popular_posts', { min_likes: 50 })
// PostgREST 会翻译为：
// POST /rest/v1/rpc/get_popular_posts
// Body: { "min_likes": 50 }
```
**什么时候用 RPC？**

| 场景 | 用普通 CRUD 还是 RPC？ |
|---|---|
| 简单增删改查 | 普通 REST（`supabase.from('posts').select(...)` ） |
| 复杂计算/聚合 | **RPC** — 数据库内完成，减少网络传输 |
| 事务操作（多表原子写入） | **RPC** — 函数内天然是一个事务 |
| 无法用 REST 表达的查询 | **RPC** — 递归查询、CTE、窗口函数等 |
| 需要提权操作 | **RPC** + `SECURITY DEFINER` |

---
## Q8：函数一般写在哪里？
### 逻辑上：写在 PostgreSQL 的 `public` schema 中
PostgREST 会自动暴露 `public` schema 中的函数为 RPC 端点。
### 物理上：写在 Migration 文件中
```
your-project/
├── supabase/
│   ├── migrations/                    ← 所有 SQL 变更按时间排列
│   │   ├── 20250101000000_create_posts.sql
│   │   ├── 20250102000000_add_rls.sql
│   │   └── 20250103000000_add_functions.sql   ← 函数定义
│   ├── seed.sql                       ← 初始数据（开发用）
│   └── config.toml                    ← 项目配置
```
### 三种编写位置
```
方式 1：Supabase Dashboard → SQL Editor
        │  适合快速试验和一次性操作
        │  ⚠️ 不推荐用于生产——无版本控制
方式 2：Migration 文件（推荐）
        │  supabase migration new add_popular_posts_function
        │  → 自动创建 supabase/migrations/20250325_add_popular_posts_function.sql
        │  → 手动编辑这个文件，写入 CREATE FUNCTION ...
        │  → supabase db push  ← 推送到远程数据库
        │  ✅ 有版本控制、可回滚、可团队协作
方式 3：Dashboard → Database → Functions 页面
        │  图形化创建函数
        │  底层仍然是执行 SQL
```
---
## Q9：如何开始创建第一个数据库？
### 方式 A：云端（最快上手，2 分钟）
```
1. 访问 https://supabase.com/dashboard
2. 注册/登录 → 点击 "New Project"
3. 选择：
   - 组织名称
   - 项目名称
   - 数据库密码（⚠️ 记住它！这是 Postgres 直连密码）
   - 区域（选离你用户最近的：东京、新加坡、首尔等）
4. 等待 ~1 分钟部署完成
5. 你已经拥有了：
   - 一个完整的 PostgreSQL 数据库
   - 自动生成的 REST/GraphQL API
   - Auth 服务
   - Realtime 服务
   - Storage 服务
   - 一个 Dashboard 管理界面
```
### 方式 B：本地开发（推荐日常开发用）
```bash
# 前提：安装 Docker 和 Supabase CLI
npm install -g supabase    # 或 brew install supabase/tap/supabase
# 初始化项目
mkdir my-app && cd my-app
supabase init              # 创建 supabase/ 目录结构
# 启动本地环境（Docker 容器）
supabase start
# 输出：
#   API URL:    http://localhost:54321
#   DB URL:     postgresql://postgres:postgres@localhost:54322/postgres
#   Studio URL: http://localhost:54323    ← 本地 Dashboard
#   anon key:   eyJhbGciOi...
#   service_role key: eyJhbGciOi...
# 创建第一个 migration
supabase migration new create_posts_table
# → 生成 supabase/migrations/20250325..._create_posts_table.sql
# 编辑这个文件，写入建表 SQL（参考 Q6 的建表语法）
# 应用 migration
supabase db reset          # 重建数据库并应用所有 migration
# 推送到云端
supabase link --project-ref <your-project-id>
supabase db push
```
### 创建第一张表后连接它
```javascript
// 安装 SDK
// npm install @supabase/supabase-js
import { createClient } from '@supabase/supabase-js'
const supabase = createClient(
  'https://<project>.supabase.co',  // 或 http://localhost:54321
  '<anon-key>'
)
// 插入数据
await supabase.from('posts').insert({
  title: '我的第一篇帖子',
  content: 'Hello Supabase!'
})
// 查询数据
const { data } = await supabase
  .from('posts')
  .select('title, content')
  .order('created_at', { ascending: false })
  .limit(10)
console.log(data)
```
---
## Q10：没有传统管理面板如何管理数据？
实际上 Supabase **自带一个非常完善的管理面板**——**Supabase Studio**（Dashboard）。
### Studio 提供的能力
```
https://supabase.com/dashboard  或  http://localhost:54323（本地）
├── Table Editor        ← 像 Excel 一样直接看/编辑数据
│   - 点击单元格直接修改
│   - 支持过滤、排序、关联展开
│   - 新建表、加列、改类型——全部可视化
│
├── SQL Editor          ← 在线 SQL 编辑器
│   - 语法高亮、自动补全
│   - 可保存常用查询（Favorites）
│   - 直接执行任意 SQL
│
├── Auth → Users        ← 用户管理
│   - 查看所有注册用户
│   - 手动创建/删除/封禁用户
│   - 配置 OAuth 提供商
│
├── Auth → Policies     ← RLS 策略管理
│   - 可视化创建/编辑 RLS 策略
│
├── Storage             ← 文件管理
│   - 浏览/上传/删除文件
│   - 管理 Bucket 和权限
│
├── Edge Functions      ← 查看/部署函数
│   - 查看日志和调用记录
│
├── Database            ← 数据库管理
│   ├── Tables          展开看所有表
│   ├── Functions       管理函数
│   ├── Triggers        管理触发器
│   ├── Roles           管理角色权限
│   ├── Extensions      一键开关扩展
│   ├── Backups         备份管理
│   └── Migrations      查看 migration 历史
│
└── Settings → API      ← 查看 API URL 和 Keys
```
### 其他管理方式
| 方式 | 工具 | 场景 |
|---|---|---|
| **GUI** | Supabase Studio（自带） | 日常管理、快速查看 |
| **GUI** | pgAdmin / DBeaver / DataGrip | 专业数据库管理 |
| **CLI** | `psql`（Postgres 原生命令行） | 脚本化操作、CI/CD |
| **CLI** | `supabase` CLI | 项目管理、migration、部署 |
| **代码** | 任何 Postgres 驱动 | 程序化数据管理 |
```bash
# 用 psql 直连数据库（用 Dashboard 里的连接信息）
psql "postgresql://postgres:[密码]@db.<project>.supabase.co:5432/postgres"
# 进入后你拥有完整的 Postgres 权限
\dt              -- 列出所有表
\d posts         -- 查看 posts 表结构
SELECT * FROM posts LIMIT 5;  -- 查询
```
**总结**：Supabase 不是"没有管理面板"，而是自带了一个**比大多数传统 admin panel 更强大的 Dashboard**，同时保留了直连 Postgres 的完全控制权。你可以用 GUI 也可以用 CLI，两者操作的是同一个数据库。