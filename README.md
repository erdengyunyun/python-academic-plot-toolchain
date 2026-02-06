# python-academic-plot-toolchain
配置驱动 + 上下文隔离的学术图表自动化生成工具链
# 面向学术出版的Python图表工具链

[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 项目简介

本项目构建了一套面向学术出版的Python图表工具链，旨在解决学术图表生成中的效率低、风格不统一、出版适配难等问题。工具链采用模块化架构，集成配置管理、样式渲染与多格式输出等核心模块，实现了从参数化规范到高质量矢量图的全流程自动生成。

## 核心特性

- **期刊模板化**：内置《计算机工程与应用》、IEEE Transactions、Nature/Science等期刊专用模板
- **配置驱动**：支持YAML/JSON配置文件，实现"一次配置，全局生效"
- **上下文管理**：PlotConfigContext实现配置原子化切换，避免跨图表配置污染
- **多格式输出**：支持PNG、PDF、SVG、EPS等多种格式，自动优化矢量图
- **合规性校验**：自动检查图表分辨率、字体、尺寸等是否符合期刊规范
- **异常处理**：完善的异常处理机制，支持错误阶段追踪

## 快速开始

### 安装

```bash
# 克隆仓库
git clone https://github.com/yourusername/academic_plots.git
cd academic_plots

# 安装依赖
pip install -r requirements.txt
```

### 基本使用

```python
import matplotlib.pyplot as plt
import numpy as np
from utils.plot_utils import setup_journal_style, save_multiformat

# 使用计算机工程与应用期刊样式
with setup_journal_style('cea'):
    # 使用选项而非具体数值，自动应用期刊默认尺寸
    fig, ax = plt.subplots(figsize=(3.15, 2.36), dpi=300)
    
    x = np.linspace(0, 10, 100)
    ax.plot(x, np.sin(x), label='sin(x)')
    ax.plot(x, np.cos(x), label='cos(x)', linestyle='--')
    
    # 自动应用期刊默认字号，无需手动指定
    ax.set_xlabel('时间 (s)')
    ax.set_ylabel('振幅')
    ax.set_title('三角函数')
    ax.legend()
    
    # 保存多格式
    save_multiformat(fig, 'output/figure', formats=['png', 'pdf'], dpi=300)
```

## 支持的出版物类型

本工具链支持多种学术出版物类型，不仅限于期刊论文：

> **跨平台说明**：本工具链基于Python和Matplotlib开发，支持Windows、Linux和macOS平台。
>
> **已适配的跨平台功能**：
> - ✅ 文件路径处理（使用`pathlib`和`os.path`，自动适配各平台路径分隔符）
> - ✅ 临时文件创建（使用`tempfile`模块，跨平台兼容）
> - ✅ 外部工具调用（Inkscape命令自动适配Windows `.exe`后缀）
> - ✅ 依赖库（所有依赖均为跨平台Python库）
>
> **需要注意的跨平台问题**：
> - ⚠️ **字体配置**：当前中文字体配置主要针对Windows系统（如SimHei、Microsoft YaHei等），Linux/macOS用户可能需要安装相应字体或手动配置
> - ⚠️ **外部工具**：SVG优化功能需要安装Inkscape软件（跨平台可用，但需手动安装）
>
> 工具内置了智能字体回退机制，会自动选择系统中可用的字体，确保在任何平台上都能正常工作。

### 期刊论文

| 出版物 | 代码 | 主要特点 |
|-------|------|---------|
| 计算机工程与应用 | `cea` | 6号宋体，矢量图，单栏8cm/双栏17cm |
| IEEE Transactions | `ieee` | Arial字体，300dpi，单栏3.5英寸/双栏7英寸 |
| Nature/Science | `nature` | Arial字体，RGB模式，内置色盲友好配色 |

### 学位论文

| 出版物 | 代码 | 主要特点 |
|-------|------|---------|
| 学位论文 | `thesis` | 博士/硕士论文，支持彩色和黑白打印 |
| 博士论文 | `博士论文` | 同上 |
| 硕士论文 | `硕士论文` | 同上 |

### 教材书籍

| 出版物 | 代码 | 主要特点 |
|-------|------|---------|
| 教材书籍 | `book` | 学术专著、教材，黑白印刷友好 |
| 教材 | `教材` | 同上 |
| 专著 | `专著` | 同上 |

## 项目结构

```
academic_plots/
├── utils/
│   └── plot_utils/              # 核心工具包
│       ├── __init__.py          # 包入口
│       ├── exceptions.py        # 异常处理
│       ├── config_manager.py    # 配置管理
│       ├── output_manager.py    # 输出管理
│       ├── base_styles.py       # 基础样式
│       ├── plot_formats.py      # 图表格式
│       ├── annotation_utils.py  # 标注工具
│       ├── multi_plot_layout.py # 多图布局
│       └── configs/             # 配置模块
│           ├── publication_config.py    # 出版物配置
│           ├── color_config.py          # 颜色配置
│           ├── font_config.py           # 字体配置
│           └── ...
├── examples/                    # 示例代码
│   └── journal_examples.py      # 期刊样式示例
├── docs/                        # 文档
│   ├── USAGE_GUIDE.md           # 使用指南
│   └── paper.md                 # 论文草稿
├── tests/                       # 测试代码
├── requirements.txt             # 依赖列表
└── README.md                    # 项目说明
```

## 文档

- [使用指南](docs/USAGE_GUIDE.md) - 详细的API文档和使用示例
- [论文草稿](docs/paper.md) - 投稿《计算机工程与应用》的论文

## 示例

运行示例代码：

```bash
python examples/journal_examples.py
```

示例输出：
- 计算机工程与应用期刊样式图表
- IEEE Transactions期刊样式图表
- Nature/Science期刊样式图表
- 多子图组合示例

## 核心模块说明

### 1. 配置管理模块 (config_manager.py)

提供YAML/JSON配置加载、验证和上下文管理功能：

```python
from utils.plot_utils import ConfigManager, PlotConfigContext

# 加载配置
manager = ConfigManager()
config = manager.load_config('config.yaml')

# 使用上下文管理器
with PlotConfigContext(config):
    fig, ax = plt.subplots()
    # 绘图代码...
```

### 2. 输出管理模块 (output_manager.py)

提供多格式输出和合规性校验：

```python
from utils.plot_utils import save_multiformat, check_compliance

# 保存多格式
paths = save_multiformat(fig, 'output/figure', 
                        formats=['png', 'pdf', 'eps'], dpi=300)

# 合规性检查
report = check_compliance(fig, config)
print(report)
```

### 3. 出版物配置模块 (publication_config.py)

内置多种学术出版物的图表规范：

```python
from utils.plot_utils import get_publication_config, list_supported_publications

# 获取出版物配置
config = get_publication_config('cea')

# 列出支持的出版物
publications = list_supported_publications()
```

## 依赖

- Python 3.8+
- matplotlib >= 3.5.0
- numpy >= 1.21.0
- pillow >= 8.0.0
- pyyaml >= 6.0 (可选，用于YAML配置)

## 贡献

欢迎提交Issue和Pull Request！

## 许可证

本项目采用 MIT 许可证 - 详见 [LICENSE](LICENSE) 文件

## 引用

如果您在研究中使用了本工具链，请引用：

```
@software{academic_plots_toolchain,
  title = {面向学术出版的Python图表工具链},
  author = {Your Name},
  year = {2025},
  url = {https://github.com/yourusername/academic_plots}
}
```

## 联系方式

- 邮箱: your.email@example.com
- GitHub Issues: https://github.com/yourusername/academic_plots/issues

---

**免责声明**：本工具链提供的期刊模板基于公开资料整理，具体投稿时请以期刊官方要求为准。
