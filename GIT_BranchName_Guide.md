## 🏆 专家级 Git 分支命名标准

### 一、 核心命名结构

遵循 **`类型(Type)/[关联编号(ID)]-描述(Description)`** 的模式。

* **全小写**：避免跨平台协作时的路径冲突。
* **连字符 (`-`)**：单词间禁止空格或下划线。
* **单数前缀**：保持正则匹配的简洁性。

---

### 二、 分支类型定义矩阵

| 类型 (Type) | 适用场景 | 命名示例 | 存活周期 |
| --- | --- | --- | --- |
| **`main`** | **生产环境代码**。仅存放经过严苛测试的稳定版本。 | `main` | 永久 |
| **`develop`** | **开发主干**。所有功能开发的目标合并分支。 | `develop` | 永久 |
| **`feature/`** | **新功能开发**。从 `develop` 拉取。 | `feature/library-map-v3` | 临时 (合并后删除) |
| **`fix/`** | **常规修复**。修复测试阶段或开发中发现的问题。 | `fix/seat-timer-overlap` | 临时 |
| **`hotfix/`** | **紧急热修复**。直接从 `main` 拉取，修复线上崩溃。 | `hotfix/login-auth-v3.3` | 临时 |
| **`refactor/`** | **代码重构**。不改功能，仅优化架构或清理 3.2 遗留。 | `refactor/api-response-wrapper` | 临时 |
| **`docs/`** | **文档更新**。仅修改 README 或 API 说明。 | `docs/update-install-guide` | 临时 |
| **`release/`** | **发布预备**。进行版本号冻结、最后测试。 | `release/v3.3.0` | 临时 |

---

### 三、 针对 SmartLib 3.3 的特定命名方案

既然你刚刚完成了“洗白”，目前的开发建议采用以下具体策略：

#### 1. 功能分支 (Feature)

命名建议包含具体的模块名，方便日后通过 `git branch` 快速检索。

* `feature/booking-engine` (预订引擎)
* `feature/admin-dashboard` (管理后台)

#### 2. 重构分支 (Refactor) —— 针对你现在的需求

由于你正从 3.2 迁移到 3.3，大量的操作其实属于重构：

* `refactor/db-schema-v3` (重构数据库表结构)
* `refactor/remove-legacy-json` (清理旧的坐标 JSON 文件)

#### 3. 实验分支 (Experiment) —— 新算法尝试

如果你要尝试 YOLOv8 或新的姿态估计模型，但不确定是否能行：

* `exp/yolov8-pose-integration`

---

### 四、 进阶约束与自动化建议

1. **禁止项**：
* ❌ `feature/add_new_seat` (使用了下划线)
* ❌ `Fix/Bug123` (首字母大写)
* ❌ `feat/smartlib3.3` (版本号不应出现在 feature 前缀后，应在 release/)


2. **关联性**：如果你的项目有需求管理工具（如 Jira/Trello/Issue），建议带上 ID：
* `feature/SL-101-seat-layout`


3. **分支清理**：合并到主干后，务必删除本地和远程的临时分支。
* `git push origin --delete feature/old-one`
