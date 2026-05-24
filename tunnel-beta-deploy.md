# 🌐 基于内网穿透的轻量化项目公网内测部署技术文档

## 📝 💡 核心策略概述

在前后端分离的开发模式中，常规部署需要分别配置 Nginx 和后端服务。为了实现**最快速度的客户公网内测与联调**，本项目采用了“前端打包嵌入后端 + 后端一体化运行 + 内网穿透服务”的轻量化策略：

1. **前端（Vue）**：通过 `npm run build` 编译成纯静态资源（HTML/JS/CSS），直接托付给后端，不再单独占用单独的 Web 服务器端口（如 `3001`）。
2. **后端（FastAPI）**：作为一体化核心，既通过核心路由吐出前端的静态网页，又挂载 `/api` 提供业务接口，统一运行在 `8000` 端口。
3. **穿透层（ngrok/cpolar）**：只需要暴露后端的 `8000` 端口，即可实现前端界面、后端接口、MySQL 数据库全链路的公网闭环访问。

---

## 🛠️ 第一部分：详细部署步骤

### 步骤一：前端静态打包与归档

1. 在前端 Vue 项目根目录下执行打包命令：
```bash
npm run build

```



```
2. 打包完成后会生成一个 `dist` 文件夹。
3. **关键操作**：将 `dist` 文件夹**内部**的所有文件（包含 `index.html` 和 `assets` 文件夹），完整复制并粘贴到后端的静态资源目录中，保持如下的严格路径结构：
   ```text
   backend/
   ├── app/
   │   └── main.py          # 后端核心业务代码
   ├── static/              # 存放前端资源的文件夹
   │   ├── index.html       # 必须直接在 static 目录下
   │   └── assets/          # 存放前端打包后的 js/css
   ├── main.py              # Uvicorn 引导脚本
   └── .env                 # 环境变量配置文件

```

### 步骤二：后端代码适配（Vue History 模式路由支持）

为了让 FastAPI 能够同时正确渲染前端、处理 API，并在用户刷新浏览器时前端路由不丢失（Vue Router History 模式），必须在 `app/main.py` 的**最底部**追加全局通配托管逻辑：

1. **文件顶部引入必要的响应类**：
```python
from fastapi.staticfiles import StaticFiles
from fastapi.responses import FileResponse, HTMLResponse
from fastapi.middleware.gzip import GZipMiddleware  # 用于性能优化

```



```
2. **配置网络与性能中间件**：
   ```python
   # 在跨域中间件（CORSMiddleware）下方，加入 Gzip 压缩，大幅提升内网穿透时的资源加载速度
   app.add_middleware(GZipMiddleware, minimum_size=1000)

```

3. **在文件最底部追加托管逻辑（严格注意路径向外跳一级 `..`）**：
```python
# 1. 挂载 Vue 编译出来的 assets 静态文件夹
current_dir = os.path.dirname(__file__)  # 此时定位在 backend/app
vue_static_dir = os.path.abspath(os.path.join(current_dir, "..", "static")) # 向外跳一级，精准定位到 backend/static
vue_assets_dir = os.path.join(vue_static_dir, "assets")

if os.path.exists(vue_assets_dir):
    app.mount("/assets", StaticFiles(directory=vue_assets_dir), name="vue_assets")

# 2. 全局通配路由：接管首页渲染以及前端刷新防 404
@app.get("/{catchall:path}")
async def serve_vue_app(request: Request, catchall: str):
    # 如果请求包含 /api 前缀，说明是拼写错误的后端接口请求，返回标准 404，防止被前端拦截
    if catchall.startswith("api") or request.url.path.startswith(settings.API_PREFIX):
        return {"detail": "Not Found"}, 404

    # 其他所有路径（如 /, /login, /dashboard）统一返回 Vue 的 index.html
    index_file_path = os.path.join(vue_static_dir, "index.html")
    if os.path.exists(index_file_path):
        return FileResponse(index_file_path)

    # 友好提示
    return HTMLResponse(
        content="<h1>未找到前端静态资源</h1><p>请确保已将 Vue 打包生成的 dist 内部文件放入了最外层的 static/ 目录下。</p>", 
        status_code=404
    )

```



```

### 步骤三：启动后端一体化服务
在 `backend` 根目录下，运行最外层的 Uvicorn 引导脚本：
```bash
python3 main.py

```

此时，访问本地的 `http://localhost:8000` 应当能同时打开前端网页并成功调用接口。

### 步骤四：配置公网内网穿透

由于需要发给客户进行内测，使用穿透工具将本地的 `8000` 端口推向公网。

* **选择一：cpolar（推荐，国内直连，响应速度极快）**
```bash
# 1. 下载并安装
curl -L https://www.cpolar.com/static/downloads/install-release-cpolar.sh | sudo bash
# 2. 认证 token（去 cpolar 官网后台复制）
cpolar authtoken <您的cpolar_token>
# 3. 启动 8000 端口穿透
cpolar http 8000

```



```
* **选择二：ngrok（全球通用）**
  ```bash
  ngrok http 8000

```

运行后复制控制台吐出的 `[https://xxxx.xxxx.app](https://xxxx.xxxx.app)` 或 `.top` 专属公网链接，即可直接发送给客户在手机端、PC端进行内测联调。

---

## ⚠️ 第二部分：部署踩坑合集（重点避坑指南）

### 🚨 坑点一：MySQL 8.0 默认 `auth_socket` 认证拒绝密码连接

* **现象**：后端启动时抛出连接异常 `sqlalchemy.exc.OperationalError: (pymysql.err.OperationalError) (1698, "Access denied for user 'root'@'localhost'")`。
* **原因**：Ubuntu 系统安装的 MySQL 8.0 默认对 `root` 用户使用系统的 `auth_socket` 插件验证，此时后端代码中配置的密码是不生效的，会被数据库直接拒之门外。
* **解决办法**：需要进入 MySQL 终端，将 root 用户的认证方式强行修改为传统的密码认证：
```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '您的硬核复杂密码';
FLUSH PRIVILEGES;

```



```

### 🚨 坑点二：密码强度策略（validate_password）导致修改密码失败
* **现象**：修改密码时报错 `ERROR 1819 (HY000): Your password does not satisfy the current policy requirements`。
* **原因**：MySQL 开启了 `MEDIUM` 级别的密码策略，要求密码必须同时包含 **“大小写字母 + 数字 + 特殊字符”** 且长度不小于 8 位。
* **解决办法**：要么顺从策略设置符合复杂度的密码；要么在执行修改前，临时在 MySQL 终端中调低策略：
  ```sql
  SET GLOBAL validate_password.policy = 0; -- 降低到 LOW 级别（只检查长度）

```

### 🚨 坑点三：数据库连接字符串（DATABASE_URL）中特殊字符 `@` 导致解析歧义

* **现象**：后端抛出找不到服务器的错误：`Can't connect to MySQL server on '2026@localhost' (Name or service not known)`。
* **原因**：若设置的数据库密码中带有 `@` 符号（例如 `Smartlib@2026`），`pymysql` 解析时会误将密码中的 `@` 当作分隔用户名密码与主机地址的控制字符，导致把后面的 `2026@localhost` 误判为服务器主机名。
* **解决办法**：对密码中的特殊字符进行 **URL 编码**。将 `@` 替换为 **`%40`**：
```ini
DATABASE_URL="mysql+pymysql://root:Smartlib%402026@localhost:3306/smartlib3?charset=utf8mb4"

```



```

### 🚨 坑点四：多层同名脚本 (`main.py`) 导致路径硬编码错乱
* **现象**：修改完前端路径后依然报错 `404` 或 `Internal Server Error`，甚至提示静态资源未找到。
* **原因**：项目结构中存在两层 `main.py`（外层 `/backend/main.py` 为启动壳，内层 `/backend/app/main.py` 为实际业务）。当在内层 `app/main.py` 中直接使用 `os.path.dirname(__file__)` 时，定位的是 `backend/app/`。如果代码写死寻找同级 `static`，将无法匹配到外层真实的 `/backend/static` 目录。
* **解决办法**：在内层业务代码中动态获取路径时，必须加 `..` 向上跳一级返回根目录：
  ```python
  vue_static_dir = os.path.abspath(os.path.join(os.path.dirname(__file__), "..", "static"))

```

### 🚨 坑点五：忘记导入依赖组件引发 500 内部服务器错误

* **现象**：浏览器公网访问时直接显示 `Internal Server Error`，后端终端抛出 `NameError: name 'FileResponse' is not defined`。
* **原因**：在新增通配路由截获并返回 HTML 页面时，调用了 FastAPI 的高级响应组件 `FileResponse` 和 `HTMLResponse`，但文件顶部漏掉了对其的 `import`。
* **解决办法**：务必检查并确保在 `app/main.py` 头部进行了完整导入。

### 🚨 坑点六：ngrok 免费版因代理检测触发 `ERR_NGROK_9009` 阻断

* **现象**：执行穿透时报错：`Running the agent with an http/s proxy is a Pay-as-you-go feature... ERR_NGROK_9009`。
* **原因**：国内部分云服务器环境网关自带或手动配置了全局 HTTP 代理环境变量，ngrok 免费版检测到后会判定为商业越权行为并直接拒绝服务。
* **解决办法**：在运行命令前，通过 `unset` 临时清空当前终端的代理环境变量，或直接改用对国内网络支持更友好的 **cpolar**。
```bash

```



unset http_proxy && unset https_proxy

```

---

## 📈 第三部分：上线总结与效果调优
通过本套流程部署后，FastAPI 的 **Gzip 压缩**极大地抵消了内网穿透链路带来的带宽限制（资源体积压缩约 70%）。配合 **cpolar 国内边缘节点**，内测网页的资源加载速度能够从原先的数秒甚至卡顿，优化至**毫秒级无感秒开**，完美满足了向客户演示、高频内测及闭环数据联调的商业要求。

```