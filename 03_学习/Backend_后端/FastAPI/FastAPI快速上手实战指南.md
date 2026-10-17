# FastAPI Python 后端快速上手与项目实战指南

这份文档旨在帮助有 Python 基础的新手快速掌握 FastAPI 开发，并熟悉公司的项目架构与开发流程。

## 1. 环境搭建 (Environment Setup)

作为 Python 开发者，管理 Python 版本和虚拟环境是基本功。

### 1.1 使用 pyenv 管理 Python 版本
`pyenv` 允许你在同一台机器上安装多个 Python 版本（如 3.8, 3.10, 3.12）并轻松切换。

*   **安装 (macOS)**: `brew install pyenv`
*   **常用命令**:
    ```bash
    pyenv install 3.10.12          # 安装特定版本
    pyenv global 3.10.12           # 设置全局版本
    pyenv local 3.10.12            # 设置当前目录版本 (生成 .python-version 文件)
    pyenv versions                 # 查看已安装版本
    ```

### 1.2 使用 venv 构建虚拟环境
为了隔离项目依赖，每个项目都应有独立的虚拟环境。

*   **创建虚拟环境**:
    ```bash
    python -m venv venv            # 在当前目录下创建名为 venv 的虚拟环境
    ```
*   **激活环境**:
    ```bash
    source venv/bin/activate       # macOS/Linux
    # venv\Scripts\activate        # Windows
    ```
*   **安装依赖**:
    ```bash
    pip install -r requirements.txt
    ```

---

## 2. FastAPI 核心要素速成

FastAPI 非常现代且高效，你只需要掌握以下核心概念即可开始工作：

1.  **Pydantic (数据模型)**:
    *   用于定义请求体（Request Body）和响应体（Response Model）的结构。
    *   自动进行类型检查和数据验证。
    ```python
    from pydantic import BaseModel
    class User(BaseModel):
        username: str
        age: int
    ```
2.  **路径参数与查询参数**:
    *   路径参数: `/items/{item_id}`
    *   查询参数: `/items/?skip=0&limit=10` (函数参数中未声明为路径参数的默认即为查询参数)。
3.  **依赖注入 (Dependency Injection)**:
    *   FastAPI 的杀手级特性。使用 `Depends` 处理鉴权、数据库连接等通用逻辑。
    *   例如: `db: AsyncSession = Depends(get_db)`。
4.  **异步编程 (Async/Await)**:
    *   FastAPI 原生支持异步。数据库操作（如 SQLAlchemy）通常使用 `await`。
    ```python
    @app.get("/")
    async def read_root():
        return {"Hello": "World"}
    ```

---

## 3. 开源框架 RuoYi-Vue3-FastAPI 概览

公司的项目基于 `RuoYi-Vue3-FastAPI` 二次开发。了解其结构有助于快速定位代码。

*   **项目地址**: [RuoYi-Vue3-FastAPI](https://github.com/insistence/RuoYi-Vue3-FastAPI)
*   **架构特点**: 前后端分离。
    *   **前端**: Vue3 + Element Plus。
    *   **后端**: FastAPI + SQLAlchemy (ORM) + Redis。
*   **核心目录结构 (后端)**:
    *   `app.py`: 程序入口。
    *   `config/`: 配置文件（数据库、Redis、常量）。
    *   `module_admin/`: 系统管理模块（用户、角色、菜单等核心功能）。
    *   `utils/`: 通用工具类（响应封装、分页、时间处理）。
    *   `middlewares/`: 中间件（跨域、权限拦截、国际化）。

---

## 4. 实战流程

### 4.1 基本开发流程
1.  **启动后端**:
    ```bash
    # 进入后端目录
    cd backend
    # 安装依赖
    pip install -r requirements.txt
    # 配置环境 (参考 .env.example 创建 .env.dev)
    # 运行
    python app.py --env=dev
    ```
2.  **新增业务模块**:
    *   通常在 `module_business/` 下新建业务包。
    *   遵循 `Controller` -> `Service` -> `Dao` -> `Model` (Entity/VO) 的分层架构。

### 4.2 代码生成器 (Module Generator)
项目中内置了代码生成功能，能极大提高效率。
*   **原理**: 读取数据库表结构 -> 映射为 Python/Vue 模板 -> 生成代码文件。
*   **使用方法**:
    1.  登录系统管理后台。
    2.  进入 **"系统工具" -> "代码生成"** 菜单。
    3.  导入数据库表，点击 **"生成代码"**。
    4.  下载生成的压缩包，解压后将 Python 代码放入 `module_business/` 下对应目录，Vue 代码放入前端项目。

### 4.3 国际化 (I18n) 实现机制
项目实现了接口响应的国际化，核心逻辑如下：
*   **方案**: `gettext` + `Babel`。
*   **流程**:
    1.  **中间件 (`middlewares/locale_middleware.py`)**: 解析请求头 `Accept-Language`，确定当前语言，存入 `contextvars`。
    2.  **响应封装 (`utils/response_util.py`)**:
        *   在 `ResponseUtil.success` 或 `error` 中，通过 `i18n.translator.t(msg)` 包裹消息文本。
        *   系统根据当前上下文语言，自动将中文 `msgid` 翻译为对应语言。
    3.  **资源文件**: 位于 `i18n/locale/` 下 (`.po` 和 `.mo` 文件)。
*   **学习点**: 阅读 `utils/response_util.py` 第 42 行 `msg: t(msg)` 的调用方式。

### 4.4 分页工具 PageUtil 详解
`utils.page_util.PageUtil.paginate` 是项目中查询列表的核心函数。

**函数签名**:
```python
async def paginate(cls, db: AsyncSession, query: Select, page_num: int, page_size: int, is_page: bool = False, is_camel = True)
```

**参数与返回值解析**:

| 参数 | 类型 | 说明 |
| :--- | :--- | :--- |
| `is_page` | `bool` | **True**: 开启分页。<br>**False**: 不分页（查询所有）。 |
| `is_camel` | `bool` | **True**: 返回结果的 Key 转换为**小驼峰** (camelCase)，适合前端使用。<br>**False**: 保持数据库字段原样 (通常是下划线 snake_case)。 |

**返回值结构**:

1.  **当 `is_page=True` 时**: 返回 `PageResponseModel` 对象（类似字典结构）。
    ```json
    {
      "rows": [...],      // 数据列表
      "pageNum": 1,       // 当前页
      "pageSize": 10,     // 页大小
      "total": 100,       // 总记录数
      "hasNext": true     // 是否有下一页
    }
    ```
2.  **当 `is_page=False` 时**: 直接返回 **数据列表** `List`。
    ```json
    [
      { "id": 1, "userName": "admin" },
      ...
    ]
    ```

### 4.5 进阶：多表关联查询与结果解析

在实际业务中，经常需要关联查询（Join），例如查询用户时同时获取部门信息。`RuoYi-Vue3-FastAPI` 采用了一种特定的处理模式。

**案例代码**: `module_admin/service/user_service.py` -> `get_user_list_services`

1.  **DAO 层 (Data Access Object)**:
    使用 SQLAlchemy 的 `select` 同时选择多个实体。
    ```python
    # module_admin/dao/user_dao.py
    query = select(SysUser, SysDept).join(SysDept, SysUser.dept_id == SysDept.dept_id)
    ```

2.  **Service 层 (业务逻辑)**:
    当 `PageUtil` 返回多表结果时，`rows` 中的每一项不再是单个对象，而是一个包含多个对象的元组/列表（经过 `CamelCaseUtil` 处理后通常是字典列表）。需要手动拼接结构。

    ```python
    # module_admin/service/user_service.py
    query_result = await UserDao.get_user_list(query_db, query_object, data_scope_sql, is_page)

    if is_page:
        user_list_result = PageResponseModel(
            **{
                **query_result.model_dump(by_alias=True), # 复制分页元数据 (total, pageNum 等)
                # 核心逻辑：row[0] 是用户字典，row[1] 是部门字典
                # 将用户字典解包 (**row[0])，并将部门字典作为 'dept' 字段挂载进去
                'rows': [{**row[0], 'dept': row[1]} for row in query_result.rows],
            }
        )
    else:
        # 不分页时的处理逻辑类似
        user_list_result = []
        if query_result:
            user_list_result = [{**row[0], 'dept': row[1]} for row in query_result]

    return user_list_result
    ```

**学习重点**:
*   **`select(EntityA, EntityB)`**: 返回的结果集中，每一行都会包含 A 和 B 两个对象。
*   **结果重组**: `PageUtil` 不会自动帮你把 EntityB 塞进 EntityA 的某个属性里，你需要通过列表推导式（List Comprehension）手动重组数据结构，以符合前端预期的 JSON 格式（例如 `{ ...user_fields, dept: { ...dept_fields } }`）。

---

## 5. AI 辅助快速上手技巧

建议充分利用 AI (如 Cursor, Trae, Copilot) 来加速学习，而不是死记硬背语法。

1.  **"解释这段代码"**: 遇到复杂的 `SQLAlchemy` 查询或 `Decorator`，直接选中代码问 AI "Explain logic step-by-step"。
2.  **"生成 Pydantic 模型"**: 把数据库表结构或 JSON 响应扔给 AI，让它生成对应的 Pydantic Class (`VO` 或 `DO`)。
3.  **"写个 CRUD 模版"**: 只需要告诉 AI "我要做一个产品管理模块，字段有 name, price"，让它参照项目现有的 `User` 模块生成 Controller/Service/Dao 代码。
4.  **Debug**: 遇到报错直接把 Traceback 贴给 AI，它通常能立马定位是环境问题还是代码逻辑错误。
