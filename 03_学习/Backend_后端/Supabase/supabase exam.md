> 我检测到 **评估/生成** 意图 — 用户想要一份覆盖前三轮所有 Supabase/PostgreSQL 知识点的测试卷。纯输出任务，无需检索或协作。
---
# Supabase 全栈知识检验
> **规则**：先独立作答，再展开答案对照。选择题可能有多选，会标注。
---
## 第一部分：基础概念（10 题）
### 1. Supabase 的核心设计理念是什么？   B
- A. 在 PostgreSQL 之上构建一层完整的抽象，屏蔽底层细节
- B. 让 PostgreSQL 的每一项能力都变得易用，不做多余抽象
- C. 用 NoSQL 文档模型替代传统关系型数据库
- D. 提供专有协议绑定用户，用体验换留存
<details>
<summary>答案</summary>
**B**
Supabase 的官方原则："不要在 PostgreSQL 之上建抽象层，而是让 PostgreSQL 的每一项能力都变得易用。" 你学的是 PostgreSQL + 开放标准（REST/JWT/S3/WebSocket），不是私有 API。
</details>
---
### 2. 在 Supabase 架构中，PostgreSQL 扮演什么角色？ B
- A. 仅作为数据存储层，业务逻辑在中间件处理
- B. 整个系统的控制平面——Auth 数据、RLS 策略、Storage 元数据全部存在其中
- C. 一个可选组件，可以替换为 MongoDB
- D. 只用于开发环境，生产环境使用专有存储引擎
<details>
<summary>答案</summary>
**B**
PostgreSQL 不只是"数据库"，它是整个系统的控制平面。Auth 数据存在 `auth` schema、RLS 策略写在 Postgres 里、Storage 元数据存在 `storage` schema。一切围绕 Postgres 旋转。
</details>
---
### 3. Supabase 项目中的 Schema 是什么？ B
- A. 数据库的表结构定义文件（类似 JSON Schema）
- B. PostgreSQL 内部的命名空间，用于逻辑分组
- C. API 的路由配置
- D. 前端表单验证规则
<details>
<summary>答案</summary>
**B**
Schema 是 PostgreSQL 内部的命名空间。`public` 存放你的业务表，`auth` 存放 GoTrue 的用户数据，`storage` 存放文件元数据。PostgREST 默认只暴露 `public` schema 的内容作为 API。
</details>
---
### 4. 以下哪些是 Supabase 的四条设计铁律？（多选） ABDE
- A. 一切可独立运行
- B. 一切可组合
- C. 一切使用专有协议以保证性能
- D. 一切可迁移
- E. 一切可扩展
<details>
<summary>答案</summary>
**A、B、D、E**
四条铁律：可独立运行、可组合、可扩展、可迁移。Supabase 明确拒绝专有协议，使用 `pg_dump`、CSV、S3 等开放标准确保零锁定。C 与 Supabase 的设计哲学完全相反。
</details>
---
### 5. RESTful API 中，`PATCH /posts?id=eq.5` 对应什么 SQL 操作？C
- A. `SELECT * FROM posts WHERE id = 5`
- B. `INSERT INTO posts VALUES (...)`
- C. `UPDATE posts SET ... WHERE id = 5`
- D. `DELETE FROM posts WHERE id = 5`
<details>
<summary>答案</summary>
**C**
REST 动词映射：GET=SELECT, POST=INSERT, **PATCH=UPDATE**, DELETE=DELETE。PATCH 表示部分更新。
</details>
---
### 6. 什么是 ORM 的 N+1 查询问题？B
- A. 查询了 N 张表但只需要 1 张
- B. 查 N 条主记录后，对每条分别发起 1 次关联查询，共 N+1 次数据库往返
- C. 一次查询返回了 N+1 条冗余记录
- D. 索引层级超过 N+1 层导致性能下降
<details>
<summary>答案</summary>
**B**
例如获取 10 篇帖子及其作者：先 1 次查帖子，再 10 次分别查每个作者 = 11 次查询。PostgREST 不存在此问题，因为每个请求翻译为单条 SQL JOIN。
</details>
---
### 7. PostgREST 声称比 Firebase 快 300%+ 的核心原因是？B
- A. 使用了更快的编程语言 Haskell
- B. 每个 HTTP 请求解析为单条 SQL 语句，Postgres 做所有重活
- C. 使用了内存缓存加速
- D. 数据存储在客户端本地
<details>
<summary>答案</summary>
**B**
PostgREST 是 Postgres 之上的一层极薄代理。每个请求 → 一条 SQL → Postgres 执行 → 返回。没有 ORM 翻译层、没有 N+1、没有中间状态管理。
</details>
---
### 8. 在 Supabase 中，RPC 指的是什么？A
- A. 远程过程调用——通过 API 调用 PostgreSQL 中定义的函数
- B. 一种数据库复制协议
- C. Real-time Push Communication 的缩写
- D. 客户端之间的点对点通信
<details>
<summary>答案</summary>
**A**
RPC（Remote Procedure Call）在 Supabase 中指通过 `supabase.rpc('function_name', { params })` 调用数据库函数。PostgREST 将其映射为 `POST /rest/v1/rpc/function_name`。适用于复杂计算、事务操作、无法用 REST CRUD 表达的查询。
</details>
---
### 9. Supabase Realtime 服务为什么选择 Elixir 语言？B
- A. 因为 Elixir 的类型系统最安全
- B. 因为 BEAM 虚拟机天生为高并发、低延迟通信设计，单节点可扛数十万连接
- C. 因为 Elixir 和 PostgreSQL 用的是同一种语言
- D. 因为 Elixir 是 Google 推荐的 WebSocket 语言
<details>
<summary>答案</summary>
**B**
Elixir 运行在 BEAM（Erlang 虚拟机）上，每个 WebSocket 连接是一个轻量级进程（不是系统线程）。WhatsApp（Erlang 系）的高并发能力就来源于此。
</details>
---
### 10. TUS 协议解决的核心问题是什么？ B
- A. 文件加密传输
- B. 大文件上传的断点续传——网络断开后可从中断处继续
- C. 文件跨区域复制
- D. 文件格式自动转换
<details>
<summary>答案</summary>
**B**
TUS 协议让客户端可以先 HEAD 查询已上传进度，再 PATCH 从断点继续。传统上传如果中断需要完全重来，TUS 解决了大文件（如视频）上传的可靠性问题。
</details>
---
## 第二部分：安全模型（8 题）C
### 11. Supabase 安全模型的三道防线，按请求流经顺序排列正确的是？
- A. RLS → GoTrue → Kong
- B. Kong → PostgREST → GoTrue
- C. Kong（验证 API Key）→ GoTrue（JWT 验证）→ PostgreSQL RLS（行级过滤）
- D. GoTrue → Kong → RLS
<details>
<summary>答案</summary>
**C**
请求先到 Kong（验证 API Key + 路由），然后 PostgREST 解析 JWT（由 GoTrue 签发），最后 PostgreSQL 执行查询时 RLS 策略基于 JWT 中的用户身份逐行过滤。
</details>
---
### 12. 以下关于 `anon` key 的说法，哪个是**错误的**？C
- A. 可以安全地暴露在前端 JavaScript 代码中
- B. 持有 anon key 的请求受 RLS 策略约束
- C. anon key 可以绕过 RLS 直接访问所有数据
- D. anon key 是 Supabase 项目创建后自动生成的
<details>
<summary>答案</summary>
**C**
C 描述的是 `service_role` key 的行为。`anon` key 的权限完全由 RLS 策略决定。如果 RLS 写的是"未登录用户什么都不能干"，那 anon key 就等于一张白纸。
</details>
---
### 13. 一张表开启了 RLS 但没有创建任何策略，会发生什么？C
- A. 所有人都能访问所有数据（等于没开 RLS）
- B. 只有表的创建者能访问
- C. 通过 anon/authenticated 角色的所有访问都被拒绝，service_role 不受影响
- D. 数据库会报错，不允许这种配置
<details>
<summary>答案</summary>
**C**
RLS 的默认行为是"白名单模式"——没有策略 = 拒绝所有。这是一种安全默认值。`service_role` key 绕过 RLS，所以服务端仍可访问。这也是"锁死表"的标准模式。
</details>
---
### 14. `auth.uid()` 函数的底层实现等价于哪个 SQL 表达式？B
- A. `SELECT id FROM auth.users LIMIT 1`
- B. `current_setting('request.jwt.claims', true)::json->>'sub'`
- C. `current_user`
- D. `session_user`
<details>
<summary>答案</summary>
**B**
`auth.uid()` 不是查数据库，而是从当前 PostgreSQL 会话变量中提取 JWT 的 `sub`（Subject）字段。PostgREST 在处理请求时将 JWT claims 设置到会话变量中。
</details>
---
### 15. 以下 RLS 策略的含义是什么？ B
```sql
CREATE POLICY "可见性" ON posts FOR SELECT
USING (
  auth.jwt()->>'role' = 'admin'
  OR user_id = auth.uid()
  OR is_public = true
);
```
- A. 只有管理员能看帖子
- B. 管理员看所有帖子，普通用户只能看自己的帖子和公开帖子
- C. 所有人只能看公开帖子
- D. 帖子作者和管理员都不能看私密帖子
<details>
<summary>答案</summary>
**B**
三个条件用 OR 连接：① admin 角色 → 看全部 ② 当前用户是作者 → 看自己的 ③ 帖子是公开的 → 谁都能看。满足任一条件即可见。
</details>
---
### 16. `SECURITY DEFINER` 关键字在函数中的作用是什么？B
- A. 定义函数的安全级别为最高
- B. 函数以**创建者**的权限执行，而非调用者的权限
- C. 函数只能被安全管理员调用
- D. 启用函数的加密传输
<details>
<summary>答案</summary>
**B**
类似 Linux 的 `setuid`。普通用户可能没有 `profiles` 表的写权限，但 `SECURITY DEFINER` 函数以创建者（通常是 database owner）身份执行，可以执行提权操作。这是一种受控的权限提升。
</details>
---
### 17. 为什么 RLS 策略中引用的列需要加索引？B
- A. 不加索引会导致 RLS 策略语法报错
- B. 索引让策略中的条件比较可以用 O(log n) 索引查找而非 O(n) 全表扫描
- C. 索引是 RLS 的必要组件，没有索引 RLS 不生效
- D. 加索引是为了让 RLS 策略能被缓存
<details>
<summary>答案</summary>
**B**
RLS 策略的 `USING` 子句本质是 `WHERE` 条件，每次查询都会执行。如果 `WHERE user_id = auth.uid()` 中的 `user_id` 没有索引，Postgres 必须扫描全表的每一行来判断是否匹配。可以用 `EXPLAIN ANALYZE` 验证是否命中索引。
</details>
---
### 18. 以下关于 JWT 中 `sub` 字段的说法，正确的是？B
- A. `sub` 是 Supabase 自定义的非标准字段
- B. `sub` 是 JWT 标准（RFC 7519）定义的 Subject 字段，在 Supabase 中存储用户 ID
- C. `sub` 存储的是用户密码的哈希值
- D. `sub` 是可选的，可以不存在于 JWT 中
<details>
<summary>答案</summary>
**B**
`sub`（Subject）是 JWT 规范定义的标准字段，表示"这个 token 代表谁"。在 Supabase 中，其值等于 `auth.users` 表中的 `id`（UUID）。
</details>
---
## 第三部分：架构组件（7 题）
### 19. PostgREST 在 schema 变更时的行为是？B
- A. 需要手动重启服务才能识别新表/列
- B. 自动内省并刷新——新建表/列后 API 立即可用
- C. 需要重新部署整个 Supabase 项目
- D. 需要在配置文件中手动注册新表
<details>
<summary>答案</summary>
**B**
PostgREST 会自动检测 schema 变更。你加一列 → API 立即暴露该列；你删一张表 → API 立即移除对应端点。这就是"instant and auto-generated"。
</details>
---
### 20. Kong 在 Supabase 中的职责不包括以下哪项？C
- A. 验证 API Key
- B. 将请求路由到 PostgREST/GoTrue/Realtime 等服务
- C. 执行 SQL 查询
- D. 限流（Rate Limiting）
<details>
<summary>答案</summary>
**C**
Kong 是 API 网关，负责认证、路由、限流。SQL 执行是 PostgreSQL 的职责。Kong 根据 URL 路径分发：`/rest/v1/*` → PostgREST，`/auth/v1/*` → GoTrue，以此类推。
</details>
---
### 21. Supabase Realtime 的 "Postgres Changes" 功能底层依赖什么技术？ B
- A. 定时轮询（Polling）数据库
- B. PostgreSQL 的逻辑复制（Logical Replication），监听 WAL
- C. 在每张表上安装触发器
- D. 客户端定时发起 HTTP 请求
<details>
<summary>答案</summary>
**B**
Postgres Changes 使用 PostgreSQL 原生的逻辑复制功能，通过 WAL（Write-Ahead Log）捕获数据变更事件，再由 Realtime 服务解析后推送给订阅的客户端。并且推送受 RLS 约束——只推送用户有权看到的变更。
</details>
---
### 22. Supabase Storage 的"S3 兼容"意味着什么？（多选）ABD
- A. 可以用 AWS SDK 直接连接 Supabase Storage
- B. 从 AWS S3 迁移到 Supabase 只需改 endpoint URL
- C. Supabase Storage 的数据实际存储在 AWS S3 上
- D. 大量 S3 生态工具（备份、CDN、数据管道）可直接使用
<details>
<summary>答案</summary>
**A、B、D**
"S3 兼容"指的是 API 协议兼容，不代表底层存储在 AWS 上。你可以用任何 S3 客户端连接、迁移成本极低、生态工具直接可用。
</details>
---
### 23. Edge Functions 和传统后端服务器的核心区别是？B
- A. Edge Functions 只能用 JavaScript
- B. Edge Functions 部署在全球边缘节点，请求路由到离用户最近的节点执行
- C. Edge Functions 可以持久化保存状态
- D. Edge Functions 不能访问数据库
<details>
<summary>答案</summary>
**B**
Edge Functions 基于 Deno 运行时（TypeScript 优先），全球分布式部署。核心优势是低延迟——代码离用户近。它们是无状态、短生命周期的，可以通过 Supabase SDK 访问数据库。
</details>
---
### 24. Supavisor 替代 PgBouncer 的主要优势是什么？B
- A. Supavisor 用 JavaScript 编写，更容易维护
- B. Supavisor 是云原生多租户连接池，用 Elixir 构建，更适合 Serverless 场景
- C. Supavisor 直接绕过了 PostgreSQL 的连接限制
- D. Supavisor 可以把任何数据库变成 PostgreSQL
<details>
<summary>答案</summary>
**B**
Supavisor 是 Supabase 自研的云原生连接池，解决 Serverless/Edge Functions 场景下大量短连接耗尽 PostgreSQL 进程的问题。提供 Transaction mode（端口 6543，适合 Serverless）和 Session mode（端口 5432，适合 ORM）。
</details>
---
### 25. 在 Supabase 中，以下哪种方式管理数据库 Schema 变更最适合生产环境？B
- A. 直接在 Dashboard SQL Editor 中执行 DDL
- B. 使用 Supabase CLI 的 Migration 文件，纳入版本控制
- C. 通过 Table Editor 图形界面手动修改
- D. 直接 psql 连上去改
<details>
<summary>答案</summary>
**B**
Migration 文件（`supabase migration new xxx` → 编辑 SQL → `supabase db push`）有版本控制、可回滚、可团队协作。Dashboard 和 psql 适合开发/调试，但生产变更必须走 Migration。
</details>
---
## 第四部分：SQL 实战（5 题）
### 26. 以下建表语句中，`UUID DEFAULT gen_random_uuid() PRIMARY KEY` 的含义是？C
- A. 一个文本类型的主键，需要手动填入
- B. 一个自增整数主键
- C. UUID 类型主键，如果不手动传值则自动生成随机 UUID
- D. 一个可为空的唯一标识符
<details>
<summary>答案</summary>
**C**
`UUID` = 数据类型（通用唯一标识符），`DEFAULT gen_random_uuid()` = 未指定时自动生成，`PRIMARY KEY` = 唯一且非空。INSERT 时不传 id 字段，数据库会自动生成一个类似 `d0714948-a2b5-4321-8a6c-e01234567890` 的值。
</details>
---
### 27. 以下 SQL 的作用是什么？B
```sql
CREATE INDEX idx_posts_public ON posts (created_at)
WHERE is_public = true;
```
- A. 为所有帖子的 `created_at` 列创建索引
- B. 只为 `is_public = true` 的帖子创建 `created_at` 索引（部分索引）
- C. 创建一个名为 `idx_posts_public` 的表
- D. 将 `is_public` 列改为索引列
<details>
<summary>答案</summary>
**B**
这是一个**部分索引**（Partial Index）。只有满足 `WHERE is_public = true` 条件的行才会被索引。好处：索引更小、维护成本更低、查询公开帖子时更快。私密帖子不占索引空间。
</details>
---
### 28. 以下触发器定义中，`FOR EACH ROW` 和 `FOR EACH STATEMENT` 的区别是？B
```sql
CREATE TRIGGER on_auth_user_created
  AFTER INSERT ON auth.users
  FOR EACH ROW
  EXECUTE PROCEDURE public.handle_new_user();
```
- A. 没有区别，只是语法变体
- B. `FOR EACH ROW` = 每插入一行触发一次；`FOR EACH STATEMENT` = 一条 INSERT 语句只触发一次（不管影响多少行）
- C. `FOR EACH ROW` 只触发一次，`FOR EACH STATEMENT` 触发多次
- D. `FOR EACH ROW` 是异步的，`FOR EACH STATEMENT` 是同步的
<details>
<summary>答案</summary>
**B**
如果一条 `INSERT INTO auth.users VALUES (...), (...), (...)` 插入 3 行：`FOR EACH ROW` 触发 3 次（每行一次，`new` 变量分别指向每行），`FOR EACH STATEMENT` 只触发 1 次。用户注册场景是单行插入，但 `FOR EACH ROW` 是更安全的写法。
</details>
---
### 29. `->>`  和 `->` 在 PostgreSQL JSON 操作中的区别是？A
- A. 完全相同
- B. `->>` 返回 **text 文本**，`->` 返回 **JSON 对象**
- C. `->` 用于数组，`->>` 用于对象
- D. `->>` 是旧语法，`->` 是新语法
<details>
<summary>答案</summary>
**B**
```sql
-- 假设 jwt = '{"sub":"abc","meta":{"role":"admin"}}'
jwt->>'sub'       -- 返回 text: 'abc'
jwt->'meta'       -- 返回 JSON: {"role":"admin"}
jwt->'meta'->>'role'  -- 返回 text: 'admin'
```
`->>` 取出来是字符串可以直接比较，`->` 取出来还是 JSON 对象可以继续深入访问。
</details>
---
### 30. 以下哪条 SQL 最危险？A
- A. `SELECT * FROM posts;`
- B. `UPDATE posts SET is_public = true;`
- C. `DELETE FROM posts WHERE id = 'abc';`
- D. `INSERT INTO posts (title) VALUES ('hello');`
<details>
<summary>答案</summary>
**B**
`UPDATE posts SET is_public = true;` **没有 WHERE 子句**——这会把整张表的每一行的 `is_public` 都改成 `true`。A 只是读取（安全），C 有 WHERE 限定了范围，D 是插入新行。永远检查 UPDATE 和 DELETE 是否带了 WHERE！
</details>
---
## 第五部分：综合场景（5 题）
### 31. 你正在开发一个博客应用，需要实现"帖子作者可以编辑自己的帖子，所有人可以阅读公开帖子"。以下哪组 RLS 策略是正确的？B
**选项 A：**
```sql
CREATE POLICY "读" ON posts FOR SELECT USING (true);
CREATE POLICY "写" ON posts FOR UPDATE USING (user_id = auth.uid());
```
**选项 B：**
```sql
CREATE POLICY "读" ON posts FOR SELECT USING (is_public = true OR user_id = auth.uid());
CREATE POLICY "写" ON posts FOR UPDATE USING (user_id = auth.uid());
```
**选项 C：**
```sql
CREATE POLICY "读" ON posts FOR SELECT USING (is_public = true);
CREATE POLICY "写" ON posts FOR ALL USING (user_id = auth.uid());
```
<details>
<summary>答案</summary>
**B**
需求拆解：① 所有人可读公开帖子 ② 作者可以看自己的所有帖子（包括非公开的）③ 作者可以编辑自己的帖子。
- A 错误：读策略用 `true` 意味着所有帖子对所有人可见（包括私密帖子）
- B 正确：读策略 = 公开帖子谁都能看 OR 自己的帖子自己能看；写策略 = 只有作者能改
- C 错误：读策略缺少"作者能看自己的私密帖子"；写策略用 `FOR ALL` 会影响 INSERT/DELETE/UPDATE 所有操作，过于宽泛
</details>
---
### 32. 你的 Edge Function 需要执行一个数据库查询。应该使用哪种连接方式？A
- A. 直接连接 PostgreSQL 主库（端口 5432）
- B. 通过 Supavisor Transaction mode（端口 6543）连接
- C. 在 Edge Function 中维护一个长连接池
- D. 每次请求都创建并销毁一个新的数据库连接
<details>
<summary>答案</summary>
**B**
Edge Functions 是无状态、短生命周期的 Serverless 函数。直接连主库会快速耗尽连接数（D 同理）。长连接池在 Serverless 场景不可行（C 排除）。Supavisor Transaction mode 专门为 Serverless 设计——每个事务临时分配连接，事务结束立即回收。
</details>
---
### 33. 用户注册后，你需要在 `profiles` 表中自动创建一条记录。最佳实践是？B
- A. 在前端注册成功的回调中调用 API 插入 profiles
- B. 使用 PostgreSQL 触发器监听 `auth.users` 的 INSERT，用 `SECURITY DEFINER` 函数写入 profiles
- C. 使用 cron job 定期同步 auth.users 和 profiles
- D. 在 GoTrue 的配置中添加 webhook
<details>
<summary>答案</summary>
**B**
A 不可靠（前端可能失败、被跳过）。C 有延迟且复杂。D GoTrue 没有此配置项。B 是 Supabase 官方推荐的标准模式——触发器在数据库层保证原子性，`SECURITY DEFINER` 解决权限问题。就是我们在 Q9 中解析的那段代码。
</details>
---
### 34. 你用 `EXPLAIN ANALYZE` 检查一条查询，输出中看到 `Seq Scan on posts`。这意味着什么？应该怎么做？不知道
- A. 说明查询性能很好，Sequential Scan 是最优方式
- B. 说明查询正在全表扫描，应该为 WHERE/RLS 中引用的列添加索引
- C. 说明数据库正在执行索引扫描
- D. 这是正常行为，不需要任何操作
<details>
<summary>答案</summary>
**B**
`Seq Scan` = Sequential Scan = 全表扫描（O(n)）。对于大表来说性能很差。应该为查询条件中引用的列创建索引，然后重新 `EXPLAIN ANALYZE` 确认变为 `Index Scan`（O(log n)）。
**例外**：小表（几百行）做 Seq Scan 可能反而比 Index Scan 快，Postgres 的查询优化器会自动判断。
</details>
---
### 35. 以下哪种做法违反了 Supabase 的安全最佳实践？（多选） BCE
- A. 在前端 JavaScript 中使用 `anon` key
- B. 在前端 JavaScript 中使用 `service_role` key
- C. 创建了表但没有开启 RLS
- D. 使用 Migration 文件管理 schema 变更
- E. 在 `SECURITY DEFINER` 函数中不做输入验证
<details>
<summary>答案</summary>
**B、C、E**
- B：`service_role` key 绕过所有 RLS，暴露在客户端意味着任何人都能直接操作你的全部数据
- C：不开 RLS 的表对所有持有 API Key 的人完全可见——即使只有 `anon` key
- E：`SECURITY DEFINER` 函数以创建者权限执行，如果不验证输入，可能被用于注入攻击或越权操作
- A 是安全的（受 RLS 约束）
- D 是最佳实践
</details>
---
