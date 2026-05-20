# Cloudflare + Render全栈部署技术手册
> 🚀 无须昂贵的服务器，轻松上线你的全栈应用！

## 1. 全栈部署概览

本手册记录了基于 Vue.js 前端与 FastAPI 后端分离架构的云端部署全过程（以SmartLibv3.0.0为案例进行部署）：

- **前端托管**：Cloudflare Pages (全球 CDN 加速，自动 HTTPS)
- **后端托管**：Render (Web Service 免费实例)
- **数据库**：SQLite (可随后端项目已有部署，或在运行时动态生成)

***

## 2. 后端部署流程 (Render)

### 2.1 环境准备（本地）

1. **代码结构**：确保项目根目录包含 `requirements.txt` 和 `app/main.py`
2. **检查**（`requirements.txt`）：
   - 必须包含 `gunicorn` 和 `uvicorn` 用于生产环境启动
   - 必须包含项目依赖的库，如 `fastapi`等
   > 说明：可使用 `pipreqs ./ --encoding=utf-8 --force` 生成 `requirements.txt` 文件，包含所有项目依赖的库列表
3. **(可选) CORS 配置**（`app/main.py`）：
   - **配置allow\_origins**：
     ```python
     # 核心：允许你的 Cloudflare 前端域名访问
     app.add_middleware(
        CORSMiddleware,
        allow_origins=[
           "https://smartlib.pages.dev",  # 你的前端地址
           "http://localhost:3000",       # 你的本地开发地址
        ],
        allow_credentials=True,
        allow_methods=["*"],
        allow_headers=["*"],
     )
     ```
   - **说明**：以上修改可不进行，默认允许所有来源访问：`allow_origins=["*"]`
4. **(可选) 数据库同步策略**：
   - **行为**：启动时若检测到根目录无 `.db` 文件，FastAPI 会自动创建一个全新的空数据库
   - **持久化限制**：在 Render 免费版上，**手动录入的数据在下一次代码推送（Deploy）或服务器重启后会丢失**
   - **建议**：若需保留初始演示数据，请在本地 `.gitignore` 中删除 `*.db`，并将带数据的 `.db` 文件 `push` 到 GitHub

### 2.2 Render 配置步骤

1. **进入Render仪表盘**：
   - 打开 [Render Dashboard](https://dashboard.render.com/) 并登录
   - 点击右上角的按钮 `+ New`，下拉菜单中选择 `Web Service`
2. **关联仓库**：
   - 在 `New Web Service` 页面，使用 `Git Provider` 选择仓库，点击即可
   - 注：如果没看到仓库，检查 Render 的 GitHub App 授权范围：
     - 点击页面右上角的 `Credentials (1)` 旁边的下拉箭头 -> 点击 `>`
     - 点击 `Configure in GitHub`（这会跳转到 GitHub 设置页面）
     - 在 GitHub 的授权页面下拉，找到 `Repository access` 选项
     - 如果你想一劳永逸，选择 `All repositories`
     - 如果你只想增加某一个，点击 `Select repositories`，然后在搜索框里选中你想部署的项目仓库
     - 点击 `Save`。回到 Render 页面刷新一下，新仓库就会出现了
3. **基础设置**：
   - **Name**: 随便起，例如 `smartlib-backend`
   - **Project(可选)**: 随便起,例如 `SmartLibv3.0.0`
   - **Language**: `Python 3` (还支持Docker、Elixir、GO、Node、Ruby、Rust)
   - **Branch**: 选择需要部署的项目分支（如 `smartlib3.0`）
   - **Region**: 建议选 `Singapore` (离中国最近，延迟低)
   - **Root Directory(可选)**::
     - 如果你的 app 文件夹和 requirements.txt 都在仓库最外层，这里留空。
     - 如果它们在子文件夹里，请填写文件夹名，例如 `backend`
   - **Build Command(默认)**: `pip install -r requirements.
     txt`
   - **Start Command**: `gunicorn -k uvicorn.workers.UvicornWorker --workers 1 app.main:app`
4. **实例类型选择**：根据实际需求选择实例类型，建议选择免费版
   - **Instance Type**: `Free`
5. **环境变量配置**：以键值对的形式配置环境变量，需手动添加或者从.env添加（建议非.env的变量在此配置，如python版本等）
   - **Environment Variables**:
      - `PYTHON_VERSION`: 设置为 `3.11.0`
      - `API_PREFIX`: 设置为 `/api/v1` (确保与前端配置一致，VITE_API_BASE_URL=https://xxx.onrender.com/api/v1)
6. **高级设置**
   - **Secret Files**: 配置.env内的变量，上传 .env 文件到平台，相比于环境变量配置更方便、安全
     - 优先级说明：Environment Variables > Secret Files，前者会覆盖后者的同名变量
   - **Health Check Path**: 默认为空，不用管【未知功能~】
   - **Pre-Deploy Command**: 免费版无权限配置，不用管【未知功能~】
   - **Auto-Deploy**: 默认即可（`On Commit`）【未知功能~】
   - **Build Filters**: 默认为空，不用管【未知功能~】
7. **部署观察**：
   - 滚动到页面最下方，点击 `Deploy Web Service`
   - 页面会跳转到日志界面，你会看到 Render 正在 `Collecting packages...（下载依赖）`
   - 成功标志：看到日志输出 `Application startup complete.` 且左上角状态变为绿色的 `Live`
   - 验证：点击页面顶部的链接（如 `https://xxx.onrender.com/docs`），能看到 Swagger UI 界面就说明后端服务启动成功

## 3. 前端部署流程 (Cloudflare Pages)

### 3.1 在线配置（CF平台）

1. **访问Workers and Pages 页面**：[Cloudflare Pages](https://dash.cloudflare.com/6139b1695005f48de1c1d3c99407488a/workers-and-pages)
2. **创建 Pages 项目**：点击 `Create application` 按钮 -> 点击 `Looking to deploy Pages? Get started` 按钮

> 注意：千万不要直接连接 GitHub 仓库！！！否则会不是Pages项目，而是Worker项目

1. **关联 GitHub 仓库**：选择 `Import an existing Git repository` -> 选择需要部署的仓库和分支 -> 点击 `Begin import` 按钮
2. **构建设置**：
   - **Project name**: 随便起（或者默认即可）
   - Production branch：确定分支
   * **Framework preset**: `Vue` （或`Vite`，但未测试）
   * **Build command**: `npm run build`
   * **Build output directory**: `dist`
   * **Root directory（advanced）**: `frontend` (选择前端项目根目录，根据远程仓库实际路径调整，若前端所有代码都在仓库根目录，无需进行配置)
   * **Environment variables（advanced）**: 详见3.2
3. **开始部署**：点击 `Save and Deploy` 按钮，等待部署完成，见`Success xxxx`说明部署成功
4. **验证部署**：访问 `https://your-app.pages.dev`（your-app为实际项目名称，具体URL见CF平台控制台），即可查看前端部署结果

### 3.2 环境变量对接

1. 平台配置：
   - 在 Settings -> Environment variables 添加 `VITE_API_BASE_URL`
   - 值：`https://your-app.onrender.com/api/v1` ()
2. 本地配置：
   - 本地新建.env.production -> 输入`VITE_API_BASE_URL=https://your-app.onrender.com/api/v1` -> 配置好同步到仓库
3. **注意1**：确保后端服务服务已跑通，并且前缀一致，否则会报错
4. **注意2**：修改变量后必须重新触发部署 (Retry deployment) 才能注入到 JS 产物中，若是本地配置push后平台会自动重新部署，无需手动触发。

### 3.3 前端 API 实例配置

在 src/utils/api.js（主要是axios.create核心代码），使用以下模式确保生产与开发环境的自动切换，其中VITE\_API\_BASE\_URL见3.2

```javascript
import axios from 'axios';

const api = axios.create({
  // 生产环境必配 VITE_API_BASE_URL，本地开发则走代理
  // 优先读取环境变量，如果没有（比如本地开发），则回退到 '/api'
  baseURL: import.meta.env.VITE_API_BASE_URL || '/api',
  timeout: 30000
})
```

- 注意：如果本地开发使用此配置，请确保在 vite.config.js 中配置了对应的 proxy：

```javascript
export default {
proxy: {
   '/api': {
      target: 'http://localhost:8000',
      changeOrigin: true,
      secure: false,
      rewrite: (path) => path.replace(/^\/api/, ''),
   },
},
}
```

***

## 4. 核心踩坑点与解决方案 (必看)

| 核心问题        | 现象                               | 根源原因                               | 解决方案 (最佳实践)                                                                  |
| :---------- | :------------------------------- | :--------------------------------- | :--------------------------------------------------------------------------- |
| SQLite 并发锁死 | 报错 table already exists 或 500 错误 | Render 默认启动多个 Worker 进程导致数据库文件竞争写入 | 修改启动命令为：`gunicorn -k uvicorn.workers.UvicornWorker --workers 1 app.main:app` |
| 路由 404/405  | 本地正常，线上接口报错                      | 前端 URL 与后端挂载路径 (API\_PREFIX) 不匹配   | 在 Render 后台设置 API\_PREFIX 环境变量；通过 /docs 验证最终路径                               |
| Bcrypt 类型错误 | hashpw 报错需要 bytes 类型             | 不同环境对库输入校验严苛程度不一                   | 在加密函数中显式调用 `.encode('utf-8')`，确保输入为字节流                                       |
| Render 休眠机制 | 首次访问响应极慢 ( > 50s)                | 免费实例 15 分钟无请求后自动进入"冷启动"状态          | 使用 cron-job.org 每 10 分钟自动访问一次 /docs 接口进行保活                                   |
| CORS 跨域拦截   | 前端请求报 Method Not Allowed         | 后端未允许 Cloudflare 域名发起跨域请求          | 在 FastAPI CORSMiddleware 中将 allow\_origins 显式设置为你的 .pages.dev 域名             |
| 数据持久化缺失     | 部署后原有数据重置为空                      | Render 免费版非持久化存储，.db 未提交至 Git      | 方案 A（不推荐，每次push都会重新部署->重置数据库）：移除 .gitignore 对 .db 的限制并推送；方案 B(推荐)：使用云端数据库 (如 PostgreSQL)            |

***

## 5. 工作流建议

1. **本地开发**：使用 `.env` 管理敏感配置，数据库使用本地 SQLite。
2. **代码提交**：`git push` 到特定分支 (如 `smartlib3.0`)。
3. **线上验证**：
   - 先检查 `Render Logs` 是否出现 `Application startup complete`。
   - 访问 `/docs` 确认 API 路由表。
   - 检查 `Cron-job` 是否能成功戳中后端。

