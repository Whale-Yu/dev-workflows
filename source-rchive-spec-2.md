# 项目初始化：源码、代码文件与目录命名规范手册 (V1.0)

本手册旨在规范项目从**零开始建立**时的外部物理文件、目录结构、内部模块文件夹以及配套文档的命名标准。通过统一的规范，确保项目具备高可读性、跨平台技术安全（Git/Linux）以及工业级工程素养。

---

## 一、 顶层：项目根目录文件夹命名

在初始化一个新项目或归档旧项目时，根目录必须具备**可追溯性**与**属性分类**。

### 1. 核心公式

$$\text{[ 8位启动日期 ]} \_ \text{[ 4位分类前缀 ]} \_ \text{[ 全小写烤肉串项目名 ]}$$

### 2. 分类前缀 (`Type_`) 黄金标准

* **`Learn_`** (Education)：新技术 Demo、语法练习、LeetCode 算法刷题。
* **`Proj_`** (Project)：个人独立发起、具备完整闭环功能的实战全栈/算法应用。
* **`Cmp_`** (Competition)：各类黑客马拉松、AI 算法赛、机器人大赛等竞赛代码。
* **`Grad_`** (Graduation)：结业核心项目、毕业设计及配套实验。
* **`Work_`** (Work/Biz)：公司实习、导师横向课题、商业外包等**具商业机密属性**的代码（**严禁公开**）。
* **`Tool_`** (Utility)：日常提效脚本、自动预约工具、批量处理小工具。
* **`Misc_`** (Miscellaneous)：期末大作业碎文件、临时想法沙盒。

> **正确示例**：`20260524_Proj_crawler-leafly-data`
> **错误示例**：`202605_proj_Crawler_LeaflyData`

---

## 二、 中层：项目内部模块文件夹命名 (Package/Module Folders)

在项目源码目录内部，为了让代码结构不杂乱，通常会按照功能模块划分文件夹。这些文件夹在代码引入时会直接参与导包（`import`），因此**绝对不能包含横线 `-**`。

### 1. 命名公式

* **优先选择**：全小写、极其精炼的**单个**复数或单数名词。
* **多词组合**：一律使用**下划线 `_**` 拼接蛇形拼写 (`snake_case`)。

### 2. 工业级模块文件夹命名“常用词表”

| 模块文件夹名 | 专门存放什么代码？ | 实际代码中的导入效果展示 (`import`) |
| --- | --- | --- |
| **`utils`** | 通用的、不涉及业务的纯工具函数（如时间转换、日志配置） | `from utils.log_helper import init_logger` |
| **`core`** | 项目的核心算法逻辑、主体业务流 | `import core.matrix_calc` |
| **`models`** | 深度学习网络结构定义（如 YOLO 配置、自定义神经网络） | `from models.yolo_pose import CustomHead` |
| **`crawlers`** | 各种不同目标网站的爬虫具体实现模块 | `from crawlers.leafly import Spider` |
| **`config`** | 读取和解析配置文件的代码脚本 | `from config.loader import settings` |
| **`api`** | 后端框架（FastAPI/Flask）的路由和接口定义文件夹 | `from api.v1 import user_router` |
| **`data_process`** | 多个单词拼写示例：负责数据清洗、特征工程的模块 | `from data_process.cleaner import remove_noise` |

---

## 三、 底层：代码源文件命名 (Source Code Files)

代码源文件（`.py`, `.go`, `.cpp` 等）在被其他模块调用时同样会参与 `import` 语句。为防止编译器将其误认为减号（Minus 运算符）而报错，**代码源文件一律禁止使用横线**。

### 1. 命名公式

$$\text{[ 核心业务/模块名 ]} \_ \text{[ 功能属性 ]} . \text{[ 扩展名 ]}$$

* **规则**：全小写 + 下划线蛇形拼写 (`snake_case`)。
* **行业例外**：Java 类文件（`.java`）与 Vue 视图组件（`.vue`）根据行业强规则习惯，保持大驼峰拼写 (`PascalCase`)。

### 2. 常见代码文件命名样板

* **Python (`.py`)**：`leafly_data_crawler.py`、`train_yolo_pose.py`、`data_utils.py`
* **Go (`.go`)**：`main_server.go`、`auth_middleware.go`、`user_test.go`
* **C++ (`.cpp`/`.h`)**：`image_process.cpp`、`motor_control.h`
* **前端组件**：`UserCard.vue`

---

## 四、 旁支：非代码/技术文档与数据文件命名 (Documentation & Data)

技术文档、部署指南、导出的数据报表不参与代码编译，不存在导包报错风险。为了对网页、GitHub 仓库及搜索引擎（SEO）最友好，**非代码文件一律使用横线**。

### 1. 命名公式

$$\text{[ 技术主体/业务 ]} - \text{[ 文档类型 ]} . \text{[ 扩展名 ]}$$

* **规则**：全小写 + 横线烤肉串拼写 (`kebab-case`)。

### 2. 常见文档与数据命名样板

* **`-spec`** (Specification 标准规范)：`source-archive-spec.md`
* **`-deploy`** (Deployment 部署指南)：`tunnel-beta-deploy.md`
* **`-guide`** (Guide 使用教程)：`yolov8-pose-guide.md`
* **`-api`** (API 说明)：`backend-api-routes.md`
* **数据导出** (`.csv` / `.xlsx` / `.json`)：`leafly-raw-data.csv`、`model-config.json`

---

## 五、 全景工程目录结构演练

当你新建一个标准的实战/工作项目时，项目内外部的完整视觉呈现如下：

```text
20260524_Proj_crawler-leafly-data/        # 1. 根目录：时间_前缀_全小写横线
├── .git/
├── .gitignore                            # 2. 配置文件：全小写
├── README.md                             # 3. 顶层说明：全大写
├── docs/                                 # 4. 文档目录：全小写单单词
│   ├── tunnel-beta-deploy.md             # 5. 技术文档：全小写横线
│   └── leafly-api-spec.md                # 5. 技术文档：全小写横线
├── data/                                 # 4. 数据目录：全小写单单词
│   └── leafly-raw-data.csv               # 6. 数据文件：全小写横线
└── src/                                  # 7. 源码主目录：全小写单单词
    ├── __init__.py
    ├── main.py                           # 8. 启动入口：极简单单词
    │
    ├── utils/                            # 9. 模块文件夹：全小写单单词
    │   ├── __init__.py
    │   └── log_helper.py                 # 10. 代码源文件：全小写下划线！
    │
    └── data_process/                     # 9. 模块文件夹：多个单词用下划线！
        ├── __init__.py
        └── leafly_cleaner.py             # 10. 代码源文件：全小写下划线！

```

---

## 六、 终极避坑红线

1. **禁止空格与中文**：物理路径中一旦出现空格或中文，在 Linux 服务器部署、Shell 脚本自动化执行或 Docker 容器化时，大概率引发路径解析截断报错。
2. **不给内部文件加时间戳**：不要命名为 `leafly_crawler_20260524.py`。文件的演进应当交由 **Git 版本控制系统**。频繁修改文件名会破坏 Git 的历史追踪线。
3. **强弱分隔分工明确**：
* **下划线 `_**` 只用于项目根目录的“三段式强隔离轴”、内部模块文件夹、以及代码源文件名。
* **横线 `-**` 只用于项目根目录的项目名本身、技术文档、以及导出的非代码数据文件。