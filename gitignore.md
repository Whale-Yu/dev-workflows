# 🚀 Ultimate Full-Stack & AI Engineer `.gitignore`

> 适用于：
>
> * Python / AI / 深度学习
> * FastAPI / Flask / Django
> * Vue / Node.js / 前端
> * MySQL / SQLite
> * VSCode / PyCharm / Trae
> * Docker / Linux / Windows / macOS
> * YOLO / OpenMMLab / CUDA
> * 全栈开发 & 工程化项目

---

# ！！！ Git 分支切换与文件追踪隔离机制
在项目的持续迭代与多版本并发开发（如独立的前后端版本或重构版本）过程中，跨分支切换时经常会遇到文件冲突、本地脏数据报红或远程仓库被污染的问题。深入理解 Git 的文件追踪状态与 .gitignore 的管辖本质，是维护团队代码仓库规整、干净的关键。

1. **.gitignore 的核心管辖本质**

.gitignore 文件的金科玉律是：它只能管辖那些从未被 Git 追踪过（Untracked）的纯本地文件。

在 Git 的生命周期管理中，本地物理目录下的文件存在于不同的状态象限，而 .gitignore 的过滤能力并不是万能的：

    - 未追踪（Untracked）且在 ignore 中：Git 会彻底无视该文件或文件夹。运行 git status 时不报红，执行 git add . 时也不会将其捕获。这是本地临时缓存、运行日志最理想的状态。

    - 已追踪（Tracked）但在 ignore 中（典型误区）：如果一个文件或文件夹在写入 .gitignore 之前，就已经被执行过 commit 提交到了版本历史中，那么 Git 将强行继续追踪该文件的改动。此时无论怎么修改 .gitignore，对其都完全失效。

2. **跨分支切换时的“本地物理留宿”现象**

Git 在进行分支切换（git switch 或 git checkout）时，对不同状态文件的搬运原则完全不同，这也是导致跨版本开发时分支相互污染的底层原因：

    1. 对“已追踪文件”的资产搬运：
    若某个核心代码文件夹在 A 分支中是已提交、被追踪的资产，而在 B 分支的历史中根本不存在。当从 A 切换到 B 时，Git 会自动、彻底地将该文件夹从本地物理硬盘上移走并暂存入版本库。因此，B 分支的 .gitignore 根本不需要配置该文件夹的忽略，因为在物理硬盘上它已不复存在。

    2. 对“未追踪文件”的本地留宿：
    若本地运行代码时在物理硬盘上产生了一些临时日志文件夹（例如旧版本生成 log/，新版本重构为 logs/）。由于它们从未入库，Git 的原则是“不负责搬运非版本控制的私人杂物”。切换分支时，这些文件夹会原封不动地留在本地物理硬盘上。

3. **分支防御性配置：构建“互相包容”的忽略规则**

当从生成旧日志命名的分支切换到新分支时，留在硬盘上的旧日志文件夹会由于不符合新分支的忽略规则，被新分支的 Git 误认为是新产生的文件而导致工作区报红。

为了实现分支间切换的绝对丝滑，技术文档规范要求各分支的 .gitignore 应当建立“互相包容”的防御性配置，双向拦截所有可能产生的临时文件命名：

🔹 前期/基础分支 .gitignore 规范配置示例
    ```gitignore
    # IDE / 开发工具缓存
    .idea/
    .trae/
    __pycache__/

    # 业务日志：同时忽略当前分支与重构分支残留的日志命名，防止跨分支切换报红
    log/
    logs/
    
🔹 后期/重构分支 .gitignore 规范配置示例
    ```gitignore
    # IDE / 开发工具缓存
    .idea/
    .trae/
    __pycache__/

    # 核心资产与日志：包容新版本自身的 logs 规则及旧版本遗留的 log 文件夹
    *logs/
    log/
    */本地临时数据库.db
    

4. **历史遗留脏数据的“洗白”手术**

若在代码审查（Code Review）中发现本该忽略的临时文件夹已经被错误地推送到远程中央仓库，必须将其从 Git 的追踪列表里踢出（物理降级为 Untracked 状态），同时完整保留本地开发者的硬盘文件。标准清理三件套命令如下：

```bash
# 1. 仅从 Git 暂存区中强制解除对该文件夹的追踪（绝不删除本地物理文件）
git rm -r --cached 目标文件夹/




# 2. 提交这次“解除追踪”的改动到本地仓库
git commit -m "fix: 从远程仓库移除历史遗留脏数据并激活忽略规则"

# 3. 推送并同步至远程分支
git push origin <当前分支名>
⚡ 总结小结：分支环境的绝对纯净，依赖于“被追踪代码随分支自动隐退，未追踪垃圾靠多分支规则双重拦截”。保持工作区 working tree clean 是避免本地编译器文件死锁和 Git 冲突的核心铁律。
```

---

# 🔥 System Files / 系统文件

```gitignore
# macOS
.DS_Store
.AppleDouble
.LSOverride

# Windows
Thumbs.db
ehthumbs.db
Desktop.ini

# Linux backup files / Linux 临时备份文件
*~

# Temporary files / 临时文件
*.tmp
*.temp
*.bak
*.old
*.swp
*.swo
```

---

# 🧠 Python / Python相关

```gitignore
# Python cache / Python缓存
__pycache__/
*.py[cod]
*$py.class

# Packaging / 打包文件
.Python
build/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
share/python-wheels/
wheels/
pip-wheel-metadata/
*.egg-info/
.installed.cfg
*.egg

# Virtual Environment / 虚拟环境
.env
.venv
venv/
ENV/
env/
virtualenv/

# pyenv
.python-version

# pipenv
Pipfile.lock

# poetry
poetry.lock

# pdm
.pdm.toml
.pdm-python
.pdm-build/

# Hatch
.hatch/

# uv
.uv/

# Type checking cache / 类型检查缓存
.mypy_cache/
.dmypy.json
dmypy.json
.pyre/
.pytype/

# Testing cache / 测试缓存
.pytest_cache/
.tox/
.nox/

# Ruff cache
.ruff_cache/

# Coverage reports / 测试覆盖率
.coverage
.coverage.*
htmlcov/

# Profiling files / 性能分析文件
.prof

# Logs / 日志
*.log

# Environment variables / 环境变量文件
.env.*
!.env.example

# IPython
.ipython/

# Jupyter Notebook cache
.ipynb_checkpoints/

# Spyder IDE
.spyproject/

# Rope project
.ropeproject/
```

---

# 🤖 AI / Machine Learning / 深度学习

```gitignore
# PyTorch models
*.pt
*.pth
*.ckpt

# TensorFlow / Keras
*.h5
*.pb
*.tflite

# ONNX / TensorRT
*.onnx
*.engine
*.trt

# Safetensors
*.safetensors

# HuggingFace / Diffusers
*.bin

# HuggingFace cache
.cache/huggingface/
huggingface/
hf_cache/

# Model weights / 模型权重
weights/
checkpoints/
ckpts/
models/
pretrained/
pretrained_models/

# YOLO outputs / YOLO训练输出
runs/
ultralytics/runs/

# OpenMMLab outputs
work_dirs/

# WandB / MLFlow
wandb/
mlruns/

# TensorBoard
tensorboard/
tb_logs/
events.out.tfevents.*

# Dataset cache / 数据集缓存
.cache/
cache/

# Datasets / 数据集
datasets/
dataset/
data/
VOCdevkit/
coco/

# Numpy / Pickle data
*.npy
*.npz
*.pkl
*.pickle
*.joblib

# CUDA compiled files
*.cubin
```

---

# 🎥 Media Files / 媒体文件

```gitignore
# Videos / 视频
*.mp4
*.avi
*.mov
*.mkv
*.flv
*.webm

# Images / 图片
*.jpg
*.jpeg
*.png
*.bmp
*.gif
*.webp
```

---

# 🌐 Node.js / Frontend / 前端

```gitignore
# Node modules
node_modules/

# pnpm
.pnpm-store/

# npm
.npm/

# yarn
.yarn/
.yarn/cache/
.yarn/unplugged/
.yarn/build-state.yml
.yarn/install-state.gz

# Vite
.vite/

# Vue
dist/
.vue/
.nuxt/

# Next.js
.next/

# React
build/

# Svelte
.svelte-kit/

# Parcel
.parcel-cache/

# Coverage / 前端测试覆盖率
coverage/

# ESLint cache
.eslintcache

# Lock files（按需保留）
# 锁文件（团队开发建议保留）
package-lock.json
yarn.lock
pnpm-lock.yaml
```

---

# ⚡ IDE / Editor / 开发工具

```gitignore
# VSCode
.vscode/
.history/

# PyCharm / IntelliJ
.idea/
*.iml

# Cursor
.cursor/

# Trae
.trae/

# Sublime
*.sublime-project
*.sublime-workspace

# Vim
*.swp
*.swo

# JetBrains output
out/
```

---

# 🐳 Docker / 容器

```gitignore
# Docker override
docker-compose.override.yml

# Docker volumes
volumes/

# Docker environment
docker.env
.docker/
```

---

# 🌍 Django

```gitignore
# SQLite database
db.sqlite3
*.sqlite3

# Django local config
local_settings.py

# Static / media files
media/
staticfiles/

# Migrations（可选）
# 数据库迁移文件（可按需开启）
# */migrations/*
# !*/migrations/__init__.py
```

---

# ⚡ FastAPI / Flask

```gitignore
# Flask instance
instance/

# FastAPI cache
fastapi_cache/

# Flask assets cache
.webassets-cache/
```

---

# 🗄 Database / 数据库

```gitignore
# SQLite
*.sqlite
*.sqlite3
*.db

# MySQL dump
*.sql

# Redis dump
dump.rdb
```

---

# 📦 Build / Release / 编译输出

```gitignore
release/
releases/
debug/
tmp/
temp/

# Binary files / 二进制文件
*.exe
*.dll
*.so
*.dylib

# Archives / 压缩包
*.zip
*.tar
*.tar.gz
*.7z
*.rar
```

---

# 🔐 Secrets / Certificates / 密钥证书

```gitignore
# API keys
apikeys/
secrets/

# Certificates
*.pem
*.key
*.crt
*.p12

# SSH keys
.id_rsa
.id_dsa

# Firebase
google-services.json

# AWS
.aws/

# Azure
.azure/
```

---

# ☁️ Cloud / Deployment / 云部署

```gitignore
# Serverless
.serverless/

# Terraform
.terraform/
terraform.tfstate*
crash.log

# Kubernetes
*.kubeconfig

# Vercel
.vercel/

# Netlify
.netlify/

# Railway
.railway/

# Render
.render/
```

---

# 📊 Data Science / 数据科学

```gitignore
# Exported results
exports/
results/

# Excel temp files
~$*.xlsx

# Data formats
*.parquet
*.feather
```

---

# 🧪 Testing / 测试

```gitignore
nosetests.xml
coverage.xml
*.cover

# Selenium
.selenium/

# Playwright
playwright-report/
test-results/
```

---

# 🛠 C/C++ / CUDA / 编译文件

```gitignore
*.o
*.obj
*.a
*.lib
*.out

cmake-build-*/
CMakeFiles/
CMakeCache.txt
compile_commands.json

Makefile
```

---

# 🧾 Documentation / 文档构建

```gitignore
# Sphinx
docs/_build/

# MkDocs
site/
```

---

# 🔥 Git

```gitignore
# Git patch files
*.patch

# Merge conflict backups
*.orig
```

---

# 🧹 Custom Outputs / 自定义输出目录

```gitignore
# General outputs
outputs/
output/

# Logs
logs/
cache_logs/
runtime/

# Screenshots
screenshots/

# Generated files
generated/

# Upload temp
uploads/

# Experiments
experiments/

# Benchmarks
benchmarks/

# Visualization
vis/
visualizations/

# OCR / Detection results
pred/
predictions/

# Tracking results
tracks/
tracking_results/
```

---

# ✅ Keep Placeholder Files / 保留占位文件

```gitignore
!.gitkeep
!.keep
```
