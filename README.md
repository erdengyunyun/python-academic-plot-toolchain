# 学术出版图表自动化生成工具链

面向学术出版的Python图表自动化生成工具链，提供配置驱动架构与上下文隔离机制，支持多种学术出版场景模板。

## 功能特性

- **上下文隔离机制**：通过PlotConfigContext实现配置的原子化切换，解决matplotlib全局状态污染问题
- **语义尺寸表达系统**：创新性的`(width_mode, aspect_mode)`二元组语义尺寸表达，将图形尺寸从"数值驱动"转变为"语义驱动"
- **形式化约束模型**：建立页面约束的数学描述（PageConstraints四元组），实现版面布局的自动化计算
- **多场景模板支持**：内置CEA、IEEE、Nature、Thesis、Book等多种学术出版场景模板
- **多格式输出**：支持PNG、JPG、SVG、PDF、EPS、TIFF六种格式，按格式分子目录存放
- **自动合规性检查**：检查分辨率、字体、尺寸等指标，确保符合学术出版规范
- **跨平台兼容**：支持Windows、Linux、macOS三大平台，智能字体回退机制

## 快速开始

### 安装

```bash
# 克隆仓库
git clone https://github.com/yourusername/academic-plots.git
cd academic-plots

# 安装依赖
pip install -r requirements.txt
```

### 基本使用

```python
from utils.plot_utils import PlotTool
import numpy as np

# 生成数据
x = np.linspace(0, 10, 100)
y1 = np.sin(x)
y2 = np.cos(x)

# 使用CEA模板绘制图表
with PlotTool('cea') as tool:
    # 使用语义尺寸二元组：单栏 + 4:3比例
    fig, ax = tool.subplots(width_mode='single', aspect_mode='ratio_4_3')
    tool.plot(x, y1, label='正弦曲线')
    tool.plot(x, y2, label='余弦曲线', linestyle='--')
    tool.set_labels('时间 (s)', '振幅', '正弦和余弦函数')
    tool.legend()
    tool.grid()
    tool.save('output/example')  # 自动按格式分子目录存放
```

## 项目结构

```
python-academic-plot-toolchain/
├── utils/
│   └── plot_utils/
│       ├── __init__.py              # 模块导出
│       ├── layout_engine.py         # 版面布局引擎（形式化约束模型）
│       ├── quick_template.py        # 快速模板高层封装
│       ├── plot_tool.py             # 简化API（PlotTool类）
│       ├── config_manager.py        # 配置管理器
│       ├── output_manager.py        # 输出管理器
│       ├── font_manager.py          # 字体管理器
│       ├── exceptions.py            # 异常定义
│       └── configs/
│           ├── __init__.py
│           └── publication_config.py # 出版物配置
├── examples/                         # 示例代码
│   ├── paper_figures.py             # 论文图表示例
│   ├── journal_examples.py          # 期刊模板示例
│   ├── quick_template_demo.py       # 快速模板演示
│   └── color_demo.py                # 配色方案演示
├── docs/                            # 文档
│   ├── paper_v2.md                  # 学术论文
│   └── README.md                    # 本文件
├── output/                          # 输出目录
├── requirements.txt                 # 依赖列表
└── README.md                        # 项目说明
```

## 核心模块说明

### 1. LayoutEngine - 版面布局引擎

基于形式化约束模型，实现语义尺寸到物理尺寸的自动计算。

```python
from utils.plot_utils import LayoutEngine

# 创建布局引擎
engine = LayoutEngine.from_page_size('A4', columns=2)

# 计算图形尺寸
figsize = engine.calculate_figure_size('single', 'ratio_4_3')  # 单栏4:3
figsize = engine.calculate_figure_size('double', 'golden')     # 跨双栏黄金比例
```

**支持的宽度模式（width\_mode）**：

- `full`: 整页宽度
- `single`: 占满单栏
- `half`: 占半栏
- `double`: 跨双栏
- `third`: 占三分之一栏
- `two_thirds`: 占三分之二栏
- `quarter`: 占四分之一栏

**支持的比例模式（aspect\_mode）**：

- `square`: 方形（1.0）
- `ratio_4_3`: 常规横图（1.333）
- `ratio_3_4`: 常规竖图（0.75）
- `golden`: 黄金比例（1.618）
- `ratio_16_9`: 宽屏（1.778）
- `auto`: 自适应

### 2. QuickTemplate - 快速模板

高层封装，简化模板定义和使用流程。

```python
from utils.plot_utils import get_template

# 获取预定义模板
cea_template = get_template('cea')
ieee_template = get_template('ieee')
nature_template = get_template('nature')

# 使用快捷别名创建图表
with cea_template:
    fig, ax = cea_template.subplots(alias='single_normal')
    ax.plot(x, y)
    ax.set_title('单栏常规图')

# 使用语义尺寸二元组
with cea_template:
    fig, ax = cea_template.subplots(width_mode='double', aspect_mode='golden')
    ax.plot(x, y)
    ax.set_title('跨双栏黄金比例图')
```

**支持的快捷别名**：

- `single_normal`: 单栏常规图 (single, ratio\_4\_3)
- `single_square`: 单栏方形图 (single, square)
- `single_golden`: 单栏黄金比例图 (single, golden)
- `half_square`: 半栏方形图 (half, square)
- `half_normal`: 半栏常规图 (half, ratio\_4\_3)
- `double_normal`: 双栏常规图 (double, ratio\_4\_3)
- `double_golden`: 双栏黄金比例图 (double, golden)
- `full_auto`: 整页自适应 (full, auto)

### 3. PlotTool - 简化API

一键式学术图表生成工具。

```python
from utils.plot_utils import PlotTool

# 方式1: 使用上下文管理器
with PlotTool('cea') as tool:
    fig, ax = tool.subplots(width_mode='single', aspect_mode='ratio_4_3')
    tool.plot(x, y, label='数据')
    tool.set_labels('X轴', 'Y轴', '标题')
    tool.save('output/figure')

# 方式2: 一行代码快速绘图
PlotTool.quick_plot(
    x, y,
    publication='ieee',
    xlabel='Time (s)',
    ylabel='Amplitude',
    title='Damped Sine Wave',
    output='output/quick_plot'
)
```

**支持的出版物类型**：

- `cea`: 《计算机工程与应用》期刊
- `ieee`: IEEE Transactions
- `nature`: Nature/Science
- `thesis`: 学位论文
- `book`: 教材书籍

## 配置说明

### 配置文件结构

```yaml
font:
  family: ['FZShuSong-Z01', 'Times New Roman', 'serif']
  titlesize: 9.0
  labelsize: 7.5
  ticksize: 7.5

page_constraints:
  width: 20.14
  height: 29.7
  margin_left: 1.4
  margin_right: 1.4
  margin_top: 2.0
  margin_bottom: 2.0
  column_count: 2
  column_gutter: 0.67

default_width_mode: 'single'
default_aspect_mode: 'ratio_4_3'

color:
  cycle: ['#1F77B4', '#FF7F0E', '#2CA02C', '#D62728', '#9467BD', '#8C564B']

output:
  dpi: 300
  formats: ['png', 'svg', 'pdf', 'eps']

style:
  grid: True
  grid_alpha: 0.3
  tick_direction: 'in'
```

### 使用自定义配置

```python
from utils.plot_utils import PlotTool

# 加载自定义配置文件
with PlotTool(config_file='custom_config.yaml') as tool:
    fig, ax = tool.subplots(width_mode='full', aspect_mode='ratio_4_3')
    tool.plot(x, y)
    tool.save('output/custom')
```

## 输出说明

### 多格式输出

默认输出6种格式：PNG、JPG、SVG、PDF、EPS、TIFF

### 文件组织

按格式分子目录存放：

```
output/
└── example/
    ├── png/
    │   └── example.png
    ├── jpg/
    │   └── example.jpg
    ├── svg/
    │   └── example.svg
    ├── pdf/
    │   └── example.pdf
    ├── eps/
    │   └── example.eps
    └── tiff/
        └── example.tiff
```

## 配色方案

所有模板默认使用彩色配色方案：

- **学术彩色配色**（默认）：`#1F77B4`, `#FF7F0E`, `#2CA02C`, `#D62728`, `#9467BD`, `#8C564B`
- **色盲友好配色**（Nature）：`#0072B2`, `#D55E00`, `#009E73`, `#CC79A7`, `#F0E442`, `#56B4E9`
- **IEEE风格配色**：`#0066CC`, `#CC0000`, `#009900`, `#FF9900`, `#9900CC`, `#00CCCC`

## 依赖要求

- Python >= 3.8
- matplotlib >= 3.5.0
- numpy >= 1.20.0
- PyYAML >= 6.0
- cycler >= 0.11.0

## 许可证

MIT License

## 贡献

欢迎提交Issue和Pull Request！

## 联系方式

- 项目主页：<https://github.com/yourusername/academic-plots>
- 问题反馈：<https://github.com/yourusername/academic-plots/issues>

