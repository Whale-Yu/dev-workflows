# 🚀 Git 常用操作命令

## 一、 基础工作流 (Daily Workflow)
| 命令 | 作用 | 备注 |
| :--- | :--- | :--- |
| `git status` | 查看当前状态 | 提交前的必选动作 |
| `git add .` | 暂存所有更改 | 将改动加入待提交列表 |
| `git commit -m "feat: message"` | 提交到本地仓库 | 记录一次快照 |
| `git push origin <branch>` | 推送到远程 | 同步本地成果 |
| `git pull origin <branch>` | 拉取远程更新 | 合并远程代码到本地 |

说明：以上操作可使用pycharm或者IDE自带的GIT工具！

## 二、 分支管理 (Branching)
```bash
# 查看分支
git branch -a              # 查看本地和远程所有分支

# 创建与切换
git checkout -b <branch-name>       # 创建并切换到新分支（b为branch，两条独立命令git branch <new-branch-name>（创建分支）和git checkout <new-branch-name>（切换命令）的组合命令）
git checkout <branch-name>            # 快速切换分支
git switch -c <new-branch-name>        # 创建并切换到新分支（新版Git 2.23+，c为create意思）
git switch <branch-name>            # 快速切换分支（新版Git 2.23+）

# 重命名与删除
git branch -m <old> <new>    # 重命名本地分支
git branch -d <name>         # 删除本地分支 (需先切走)
git push origin --delete <name> # 删除远程分支

# 幽灵分支清理 (必会)
git fetch --prune            # 同步远程已删除的分支，清理本地 Remote 列表
```

## 三、 忽略规则管理 (.gitignore)
当修改了 .gitignore 但文件依然被追踪时，需要刷新缓存：

```bash
# 1. 刷新特定文件的缓存 (如 .gitignore 本身)
git rm -r --cached .\.gitignore

# 2. 全局刷新所有忽略规则 (最常用)
git rm -r --cached .         # 移除所有文件的索引记录
git add .                    # 重新根据 .gitignore 建立索引
git commit -m "fix: refresh ignore rules"
```

## 四、 后悔药与撤销 (Undoing)
```bash
# 1. 撤销工作区的修改 (未 add)
git checkout -- <file>       # 丢弃某个文件的本地改动

# 2. 撤销暂存区文件 (已 add 未 commit)
git reset HEAD <file>        # 移回工作区

# 3. 撤销最后一次提交 (已 commit 未 push)
git reset --soft HEAD~1      # 撤销 commit，保留改动的代码
git reset --hard HEAD~1      # 彻底回滚，代码也会消失 (慎用)

# 4. 修改最后一次提交信息
git commit --amend -m "new message"
```

## 五、 暂存与历史 (Stash & Log)
```bash
# 代码写一半要去修 Bug
git stash                    # 暂存当前未提交的改动
git stash pop                # 还原并删除暂存记录

# 查看历史
git log --oneline --graph    # 树状简洁版日志
git blame <file>             # 逐行查看代码是谁写的 (背锅位定位)
```

## 六、 仓库维护 (Maintenance)
```bash
# 1. 查看/修改远程地址
git remote -v
git remote set-url origin <new-url>

# 2. 清理幽灵分支 (核心)
git fetch --prune            # 同步远程已删除的分支，修剪本地列表

# 3. 解除已消失的远程关联 (针对 upstream is gone)
git branch --unset-upstream <branch-name>

# 4. 仓库体检
git gc                       # 压缩仓库，提升性能 (Garbage Collection)
git prune                    # 清理无用的孤立对象

```

## 七、 Commit 提交规范建议
见[GIT_COMMIT_GUIDE.md](GIT_COMMIT_GUIDE.md)
