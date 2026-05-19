# =========================================================
# 🚀 Ultimate Full-Stack & AI Engineer .gitignore
# 🚀 全栈 & AI 工程师通用 .gitignore
#
# Covers / 涵盖：
# - Python / AI / Deep Learning
# - FastAPI / Flask / Django
# - Vue / Node.js / Frontend
# - MySQL / SQLite
# - VSCode / PyCharm / Trae
# - Docker / Linux / Windows / macOS
# - Jupyter / CUDA / YOLO / OpenMMLab
# =========================================================



# =========================================================
# 🔥 SYSTEM FILES / 系统文件
# =========================================================

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



# =========================================================
# 🧠 PYTHON / Python相关
# =========================================================

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



# =========================================================
# 🤖 AI / MACHINE LEARNING / 深度学习
# =========================================================

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



# =========================================================
# 🎥 MEDIA FILES / 媒体文件
# =========================================================

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



# =========================================================
# 🌐 NODE.JS / FRONTEND / 前端
# =========================================================

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



# =========================================================
# ⚡ IDE / EDITOR / 开发工具
# =========================================================

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



# =========================================================
# 🐳 DOCKER / 容器
# =========================================================

# Docker override
docker-compose.override.yml

# Docker volumes
volumes/

# Docker environment
docker.env
.docker/



# =========================================================
# 🌍 DJANGO
# =========================================================

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



# =========================================================
# ⚡ FASTAPI / FLASK
# =========================================================

# Flask instance
instance/

# FastAPI cache
fastapi_cache/

# Flask assets cache
.webassets-cache/



# =========================================================
# 🗄 DATABASE / 数据库
# =========================================================

# SQLite
*.sqlite
*.sqlite3
*.db

# MySQL dump
*.sql

# Redis dump
dump.rdb



# =========================================================
# 📦 BUILD / RELEASE / 编译输出
# =========================================================

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



# =========================================================
# 🔐 SECRETS / CERTIFICATES / 密钥证书
# =========================================================

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



# =========================================================
# ☁️ CLOUD / DEPLOYMENT / 云部署
# =========================================================

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



# =========================================================
# 📊 DATA SCIENCE / 数据科学
# =========================================================

# Exported results
exports/
results/

# Excel temp files
~$*.xlsx

# Data formats
*.parquet
*.feather



# =========================================================
# 🧪 TESTING / 测试
# =========================================================

nosetests.xml
coverage.xml
*.cover

# Selenium
.selenium/

# Playwright
playwright-report/
test-results/



# =========================================================
# 🛠 C/C++ / CUDA / 编译文件
# =========================================================

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



# =========================================================
# 🧾 DOCUMENTATION / 文档构建
# =========================================================

# Sphinx
docs/_build/

# MkDocs
site/



# =========================================================
# 🔥 GIT
# =========================================================

# Git patch files
*.patch

# Merge conflict backups
*.orig



# =========================================================
# 🧹 CUSTOM OUTPUTS / 自定义输出目录
# =========================================================

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



# =========================================================
# ✅ KEEP PLACEHOLDER FILES / 保留占位文件
# =========================================================

!.gitkeep
!.keep