# rembg - AI 图片去背景工具

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![rembg](https://img.shields.io/badge/rembg-2.0.72-green.svg)](https://github.com/danielgatis/rembg)

使用 rembg（基于深度学习的背景去除工具）为图片去除背景，生成透明背景的 PNG 图片。

---

## 🧭 导航 / Navigation

**中文** | [English](#-english-version)

- [✨ 功能特点](#-功能特点)
- [🚀 快速开始](#-快速开始)
- [📝 使用方法](#-使用方法)
- [🎨 模型选项](#-模型选项)
- [🔧 高级用法](#-高级用法)
- [📁 目录结构](#-目录结构)
- [❓ 常见问题](#-常见问题)
- [🔗 References](#-references)

---

## ✨ 功能特点

- 🤖 **AI 驱动** - 基于 U-2-Net 深度学习模型，精准识别前景物体
- 🎯 **多种模型** - 支持通用、人像、衣物等多种分割场景
- ⚡ **自动环境** - 首次使用自动检测并安装依赖
- 📦 **批量处理** - 支持单张和批量两种处理模式
- 🌍 **中英双语** - 所有输出均为中英双语，友好易用
- 🔒 **本地运行** - 所有处理在本地完成，保护隐私

---

## 🚀 快速开始

### 🎉 自动安装，开箱即用！

首次使用时，Skill 会**自动检测并安装环境**，无需手动操作！

首次运行会自动完成：
- ✅ 创建虚拟环境（`~/.venv/rembg`）
- ✅ 安装依赖（rembg 等）
- ✅ 下载 AI 模型（约 176MB，仅首次）

### 📌 使用方式

**方式 1：Shell 脚本（推荐）**

```bash
# 单张图片去背景
~/.openclaw/skills/rembg/scripts/remove-bg.sh <输入图片路径>

# 示例
~/.openclaw/skills/rembg/scripts/remove-bg.sh photo.jpg
~/.openclaw/skills/rembg/scripts/remove-bg.sh ~/Pictures/portrait.png
```

**方式 2：Python 脚本**

```bash
# 单张图片
python3 ~/.openclaw/skills/rembg/scripts/remove_bg.py <输入图片路径>

# 批量处理
python3 ~/.openclaw/skills/rembg/scripts/batch_remove_bg.py <输入目录> [输出目录]
```

**输出位置：** `~/.openclaw/skills/rembg/image_output/{日期}/`

---

## 📝 使用方法

### 单张图片处理

```bash
# 推荐：使用 Shell 脚本（自动处理输出路径）
~/.openclaw/skills/rembg/scripts/remove-bg.sh photo.jpg

# 指定输出目录
~/.openclaw/skills/rembg/scripts/remove-bg.sh photo.jpg /path/to/output/

# 或使用 Python 脚本（支持更多参数）
python3 ~/.openclaw/skills/rembg/scripts/remove_bg.py photo.jpg -m u2net_human_seg
```

**输出位置：** `~/.openclaw/skills/rembg/image_output/{日期}/`

### 批量处理

```bash
# 处理整个文件夹
python3 ~/.openclaw/skills/rembg/scripts/batch_remove_bg.py ~/Pictures/to_remove/

# 指定输出目录
python3 ~/.openclaw/skills/rembg/scripts/batch_remove_bg.py ~/Pictures/input/ ~/Pictures/output/
```

**输出位置：** `~/.openclaw/skills/rembg/image_output/{日期}/`

---

## 🎨 模型选项

| 模型 | 命令 | 描述 | 大小 | 适用场景 |
|------|------|------|------|----------|
| **u2net** | `-m u2net` | 通用模型（默认） | ~176MB | 日常通用 |
| **u2netp** | `-m u2netp` | 轻量版 u2net | ~45MB | 快速处理 |
| **u2net_human_seg** | `-m u2net_human_seg` | 人像分割 | ~176MB | 人物照片 |
| **u2net_cloth_seg** | `-m u2net_cloth_seg` | 衣物分割 | ~176MB | 服装图片 |
| **isnet** | `-m isnet` | 高精度通用 | ~176MB | 高质量需求 |
| **birefnet-general** | `-m birefnet-general` | BiRefNet 通用 | ~973MB | 极致精度 |

---

## 🔧 高级用法

### Python API

```python
from rembg import remove
from PIL import Image

# 基础去背景
input_image = Image.open("input.png")
output = remove(input_image)
output.save("output.png")

# 开启 alpha matting（更好的边缘处理）
output = remove(
    input_image,
    alpha_matting=True,
    alpha_matting_foreground_threshold=240,
    alpha_matting_background_threshold=10,
    alpha_matting_erode_size=10
)

# 仅返回 mask
mask = remove(input_image, only_mask=True)

# 使用特定模型
from rembg import new_session
session = new_session("u2net_human_seg")
output = remove(input_image, session=session)
```

### 参数说明

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `model` | str | `'u2net'` | 模型选择 |
| `alpha_matting` | bool | `False` | 开启 alpha 抠图 |
| `alpha_matting_foreground_threshold` | int | `240` | 前景阈值 |
| `alpha_matting_background_threshold` | int | `10` | 背景阈值 |
| `alpha_matting_erode_size` | int | `10` | 腐蚀大小 |
| `only_mask` | bool | `False` | 仅返回 mask |

---

## 📁 目录结构

```
rembg/
├── SKILL.md                    # Skill 元数据与指令
├── README.md                   # 本文件
├── _meta.json                  # 元数据配置
├── setup/                      # 环境初始化（自动运行）
│   ├── install.py              # 自动安装脚本
│   ├── requirements.txt        # 依赖声明
│   └── check_env.py            # 环境检查
├── scripts/                    # 业务脚本
│   ├── remove_bg.py            # 单张处理（Python）
│   ├── remove-bg.sh            # 单张处理（Shell）
│   └── batch_remove_bg.py      # 批量处理
├── references/                 # 参考文档
│   └── rembg-guide.md          # 详细指南
└── image_output/               # 输出目录
    ├── single/                 # 单张结果
    │   └── {日期}/             # 按日期分类
    └── batch/                  # 批量结果
        └── {日期}/             # 按日期分类
```

---

## ❓ 常见问题

### Q: 处理速度慢？

**A:** 可以使用轻量模型 `u2netp`，速度更快但精度略低：

```bash
python3 scripts/remove_bg.py image.png -m u2netp
```

### Q: 内存不足？

**A:** 建议：
1. 使用 `u2netp` 模型（仅 45MB）
2. 减少批量处理数量
3. 关闭其他占用内存的应用

### Q: 模型下载失败？

**A:** 手动下载模型：

```bash
python3 -c "from rembg import new_session; new_session('u2net')"
```

或检查网络连接，模型下载地址：
- https://github.com/danielgatis/rembg/releases

### Q: 输出图片边缘有白边？

**A:** 开启 alpha matting 改善边缘：

```python
output = remove(input_image, alpha_matting=True)
```

### Q: 首次运行卡住？

**A:** 首次运行会自动下载模型（约 176MB），请耐心等待。模型保存在 `~/.u2net/`，后续无需重复下载。

### Q: 需要手动安装环境吗？

**A:** 不需要！首次使用时 Skill 会**自动检测并安装环境**。如果自动安装失败，才需要手动运行：
```bash
python3 ~/.openclaw/skills/rembg/setup/install.py
```

---

## 🔗 References

- **rembg 官方仓库**: https://github.com/danielgatis/rembg
- **U-2-Net 论文**: https://arxiv.org/abs/2005.09007
- **PIL/Pillow 文档**: https://pillow.readthedocs.io/

---

## 📄 License

rembg 使用 MIT License。详见：https://github.com/danielgatis/rembg/blob/main/LICENSE

---

<div align="center">

**Made with ❤️ for OpenClaw**

[Report Issues](https://github.com/openclaw/openclaw/issues) · [Request Features](https://github.com/openclaw/openclaw/discussions)

</div>

---

---

## 🌍 English Version

[Top / 返回顶部](#rembg---ai-图片去背景工具)

### 🧭 Quick Navigation

- [✨ Features](#-features)
- [🚀 Quick Start](#-quick-start)
- [📝 Usage](#-usage)
- [🎨 Model Options](#-model-options)
- [🔧 Advanced Usage](#-advanced-usage)
- [📁 Directory Structure](#-directory-structure)
- [❓ FAQ](#-faq)
- [🔗 References](#-references)

---

# rembg - AI Background Removal Tool

Remove backgrounds from images using rembg (a deep learning-based background removal tool) and generate transparent PNG images.

---

## ✨ Features

- 🤖 **AI-Powered** - Based on U-2-Net deep learning model for accurate foreground detection
- 🎯 **Multiple Models** - Supports general, portrait, clothing, and various segmentation scenarios
- ⚡ **Auto Environment** - Automatically detects and installs dependencies on first use
- 📦 **Batch Processing** - Supports both single image and batch processing modes
- 🌍 **Bilingual** - All output in Chinese and English for user-friendly experience
- 🔒 **Local Processing** - All processing done locally, protecting your privacy

---

## 🚀 Quick Start

### 🎉 Auto-Install, Ready to Use!

On first use, the Skill will **automatically detect and install the environment** — no manual setup needed!

First run will automatically:
- ✅ Create virtual environment (`~/.venv/rembg`)
- ✅ Install dependencies (rembg, etc.)
- ✅ Download AI model (~176MB, first time only)

### 📌 Usage

**Method 1: Shell Script (Recommended)**

```bash
# Single image background removal
~/.openclaw/skills/rembg/scripts/remove-bg.sh <input_image_path>

# Examples
~/.openclaw/skills/rembg/scripts/remove-bg.sh photo.jpg
~/.openclaw/skills/rembg/scripts/remove-bg.sh ~/Pictures/portrait.png
```

**Method 2: Python Script**

```bash
# Single image
python3 ~/.openclaw/skills/rembg/scripts/remove_bg.py <input_image_path>

# Batch processing
python3 ~/.openclaw/skills/rembg/scripts/batch_remove_bg.py <input_directory> [output_directory]
```

**Output Location:** `~/.openclaw/skills/rembg/image_output/{date}/`

---

## 📝 Usage

### Single Image Processing

```bash
# Recommended: Shell script (auto-generates output path)
~/.openclaw/skills/rembg/scripts/remove-bg.sh photo.jpg

# Specify output directory
~/.openclaw/skills/rembg/scripts/remove-bg.sh photo.jpg /path/to/output/

# Or use Python script (more options)
python3 ~/.openclaw/skills/rembg/scripts/remove_bg.py photo.jpg -m u2net_human_seg
```

**Output Location:** `~/.openclaw/skills/rembg/image_output/{date}/`

### Batch Processing

```bash
# Process entire folder
python3 ~/.openclaw/skills/rembg/scripts/batch_remove_bg.py ~/Pictures/to_remove/

# Specify output directory
python3 ~/.openclaw/skills/rembg/scripts/batch_remove_bg.py ~/Pictures/input/ ~/Pictures/output/
```

**Output Location:** `~/.openclaw/skills/rembg/image_output/{date}/`

---

## 🎨 Model Options

| Model | Command | Description | Size | Use Case |
|-------|---------|-------------|------|----------|
| **u2net** | `-m u2net` | General model (default) | ~176MB | General purpose |
| **u2netp** | `-m u2netp` | Lightweight u2net | ~45MB | Fast processing |
| **u2net_human_seg** | `-m u2net_human_seg` | Portrait segmentation | ~176MB | Portrait photos |
| **u2net_cloth_seg** | `-m u2net_cloth_seg` | Clothing segmentation | ~176MB | Fashion images |
| **isnet** | `-m isnet` | High-precision general | ~176MB | High quality needs |
| **birefnet-general** | `-m birefnet-general` | BiRefNet general | ~973MB | Ultimate precision |

---

## 🔧 Advanced Usage

### Python API

```python
from rembg import remove
from PIL import Image

# Basic background removal
input_image = Image.open("input.png")
output = remove(input_image)
output.save("output.png")

# Enable alpha matting (better edge handling)
output = remove(
    input_image,
    alpha_matting=True,
    alpha_matting_foreground_threshold=240,
    alpha_matting_background_threshold=10,
    alpha_matting_erode_size=10
)

# Return only mask
mask = remove(input_image, only_mask=True)

# Use specific model
from rembg import new_session
session = new_session("u2net_human_seg")
output = remove(input_image, session=session)
```

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `model` | str | `'u2net'` | Model selection |
| `alpha_matting` | bool | `False` | Enable alpha matting |
| `alpha_matting_foreground_threshold` | int | `240` | Foreground threshold |
| `alpha_matting_background_threshold` | int | `10` | Background threshold |
| `alpha_matting_erode_size` | int | `10` | Erosion size |
| `only_mask` | bool | `False` | Return only mask |

---

## 📁 Directory Structure

```
rembg/
├── SKILL.md                    # Skill metadata and instructions
├── README.md                   # This file
├── _meta.json                  # Metadata configuration
├── setup/                      # Environment initialization (auto-run)
│   ├── install.py              # Auto-install script
│   ├── requirements.txt        # Dependency declaration
│   └── check_env.py            # Environment check
├── scripts/                    # Business scripts
│   ├── remove_bg.py            # Single image (Python)
│   ├── remove-bg.sh            # Single image (Shell)
│   └── batch_remove_bg.py      # Batch processing
├── references/                 # Reference documentation
│   └── rembg-guide.md          # Detailed guide
└── image_output/               # Output directory
    ├── single/                 # Single image results
    │   └── {date}/             # Organized by date
    └── batch/                  # Batch results
        └── {date}/             # Organized by date
```

---

## ❓ FAQ

### Q: Processing is slow?

**A:** Use the lightweight model `u2netp` for faster processing with slightly lower accuracy:

```bash
python3 scripts/remove_bg.py image.png -m u2netp
```

### Q: Out of memory?

**A:** Recommendations:
1. Use `u2netp` model (only 45MB)
2. Reduce batch processing quantity
3. Close other memory-intensive applications

### Q: Model download failed?

**A:** Manually download the model:

```bash
python3 -c "from rembg import new_session; new_session('u2net')"
```

Or check your network connection. Model download URL:
- https://github.com/danielgatis/rembg/releases

### Q: White edges on output images?

**A:** Enable alpha matting to improve edges:

```python
output = remove(input_image, alpha_matting=True)
```

### Q: First run hangs?

**A:** The first run automatically downloads the model (~176MB), please be patient. Models are saved in `~/.u2net/` and won't need to be downloaded again.

### Q: Do I need to manually install the environment?

**A:** No! The Skill will **automatically detect and install the environment** on first use. Only run manually if auto-install fails:
```bash
python3 ~/.openclaw/skills/rembg/setup/install.py
```

---

## 🔗 References

- **rembg Official Repo**: https://github.com/danielgatis/rembg
- **U-2-Net Paper**: https://arxiv.org/abs/2005.09007
- **PIL/Pillow Docs**: https://pillow.readthedocs.io/

---

## 📄 License

rembg is licensed under the MIT License. See: https://github.com/danielgatis/rembg/blob/main/LICENSE

---

<div align="center">

**Made with ❤️ for OpenClaw**

[Report Issues](https://github.com/openclaw/openclaw/issues) · [Request Features](https://github.com/openclaw/openclaw/discussions)

</div>
