# Git Commit Guide

团队统一 Git Commit 规范，用于提升提交记录可读性、方便代码追踪、自动生成 CHANGELOG，以及提高 Code Review 效率。

---


# 目录

- [1. Commit Message 格式](#1-commit-message-格式)
- [2. Commit Type 说明](#2-commit-type-说明)
- [3. Scope 规范](#3-scope-规范)
- [4. Subject 编写规范](#4-subject-编写规范)
- [5. Commit 示例](#5-commit-示例)
- [6. 推荐工作流](#6-推荐工作流)
- [7. 推荐工具](#7-推荐工具)
- [8. 团队规范建议](#8-团队规范建议)
- [9. 官方规范](#9-官方规范)

---

# 1. Commit Message 格式

推荐格式：

```bash
<type>(<scope>): <subject>
```

示例：

```bash
feat(user): add login api
fix(order): resolve duplicate payment issue
docs(readme): update installation guide
refactor(auth): simplify token validation
```

---

# 2. Commit Type 说明

| Type | 说明 | 示例 |
|---|---|---|
| feat | 新增功能 | `feat(user): add login api` |
| perf | 性能优化 | `perf(cache): improve redis query speed` |
| fix | 修复 Bug | `fix(order): fix payment issue` |
| refactor | 代码重构 | `refactor(auth): simplify jwt logic` |
| style | 代码格式调整 | `style(ui): format modal component` |
| docs | 文档更新 | `docs(readme): update install guide` |
| test | 测试相关 | `test(user): add login tests` |
| chore | 构建/依赖/工具修改（琐事） | `chore(deps): upgrade axios` |
| build | 构建系统修改 | `build(vite): optimize config` |
| ci | CI/CD 配置修改 | `ci(actions): add deploy workflow` |
| revert | 回滚提交 | `revert: revert payment changes` |

---

# 3. Scope 规范

`scope` 用于标识影响模块。

推荐格式：

```bash
feat(user): add oauth login
fix(payment): fix duplicate callback
```

常见 Scope：

| Scope | 说明 |
|---|---|
| user | 用户模块 |
| auth | 登录认证 |
| payment | 支付 |
| order | 订单 |
| admin | 后台管理 |
| api | 接口 |
| ui | UI 界面 |
| deps | 依赖 |
| config | 配置 |
| ci | CI/CD |

---

# 4. Subject 编写规范

## 推荐

- 使用英文
- 小写开头
- 使用动词现在时
- 简洁明确
- 不加句号

推荐示例：

```bash
feat(user): add email login support
fix(api): handle null response
refactor(order): split status service
```

---

## 不推荐

```bash
fix: bug fixed
update code
修改代码
最终版本
test
```

---

# 5. Commit 示例

## 新功能

```bash
feat(order): add refund api
```

## 修复问题

```bash
fix(payment): prevent duplicate callback
```

## 重构代码

```bash
refactor(auth): split jwt service
```

## 文档更新

```bash
docs(readme): add docker setup guide
```

## 性能优化

```bash
perf(search): optimize query speed
```

## 更新依赖

```bash
chore(deps): upgrade react to v19
```

---

# 6. 推荐工作流

## 提交前检查

```bash
npm run lint
npm run test
```

---

## 小步提交

推荐：

- 一个 commit 只做一件事
- 不混合无关修改
- 保持 commit 可回滚
- 避免超大 commit

---

## 推荐提交节奏

| 场景 | 是否推荐提交 |
|---|---|
| 完成一个功能点 | ✅ |
| 修复一个独立 Bug | ✅ |
| 重构完成一个模块 | ✅ |
| 写了一半功能 | ❌ |
| 多个无关修改混合 | ❌ |

---

# 7. 推荐工具

## Commitlint

用于校验 commit message 是否符合规范。

安装：

```bash
npm install -D @commitlint/cli @commitlint/config-conventional
```

配置：

```js
module.exports = {
  extends: ['@commitlint/config-conventional']
}
```

---

## Husky

用于 Git Hook。

安装：

```bash
npm install -D husky
```

初始化：

```bash
npx husky init
```

---

## Commitizen

交互式生成 commit message。

安装：

```bash
npm install -D commitizen cz-git
```

使用：

```bash
npx cz
```

---

# 8. 团队规范建议

推荐团队统一：

| 工具/规范 | 是否推荐 |
|---|---|
| Conventional Commits | ✅ |
| Commitlint | ✅ |
| Husky | ✅ |
| 自动生成 CHANGELOG | ✅ |
| 强制 PR Review | ✅ |
| Squash Merge | 推荐 |

---

# 9. 官方规范

## Conventional Commits

https://www.conventionalcommits.org/

---

# 10. 推荐 Commit 示例合集

```bash
feat(user): add oauth login
fix(api): handle timeout retry
docs(readme): update quick start
style(ui): format modal component
refactor(order): simplify status logic
perf(search): optimize query speed
test(payment): add refund tests
chore(deps): upgrade react
build(vite): improve bundle splitting
ci(actions): add release pipeline
```

---

# 11. 推荐团队提交规范（最佳实践）

## 推荐

```bash
feat(auth): add github login
fix(order): handle refund exception
```

特点：

- 单一职责
- 可回滚
- 可追踪
- 可生成 CHANGELOG

---

## 不推荐

```bash
update
fix bug
final
test
```

问题：

- 无法追踪修改内容
- 不利于 Code Review
- 无法自动生成日志
- 可读性差

---

# 12. 推荐 VSCode 插件

| 插件 | 用途 |
|---|---|
| GitLens | Git 历史查看 |
| Conventional Commits | Commit 提示 |
| Git Graph | Git 图谱 |
| Error Lens | 错误高亮 |

---

# 13. CHANGELOG 自动生成（推荐）

推荐工具：

- semantic-release
- standard-version
- release-it

示例：

```bash
npm install -D standard-version
```

生成版本日志：

```bash
npm run release
```

---

# 14. 最终建议

推荐团队统一：

- Commit Message 规范
- 分支命名规范
- PR 规范
- Code Review 流程
- 自动化 CI/CD

推荐目标：

> Commit 可读、可追踪、可回滚、可自动化。
