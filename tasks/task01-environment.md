# Quant-for-Beginners Task1：环境配置学习笔记

日期：2026-07-13

## 1. 今天学习的 Task

本次完成 Quant-for-Beginners 的 Task1：环境配置。目标是确认课程项目、Python 虚拟环境、JupyterLab 和课程依赖均可正常使用，为后续运行量化金融 Notebook 做准备。

## 2. 完成的课程要求

- 已获取课程项目仓库并确认项目目录结构。
- 已使用项目独立虚拟环境 `.venv`，避免课程依赖污染系统 Python。
- 已检查 `requirements.txt` 中的主要依赖。
- 已确认 JupyterLab 可以通过项目虚拟环境运行。
- 已注册并在 VS Code 中选择 `Python (quant-for-beginners)` Notebook 内核。
- 已成功导入 `numpy`、`pandas`、`matplotlib`、`seaborn`、`yfinance`、`akshare` 和 `scikit-learn`。
- 已修复 8 个课程 Notebook 在 macOS 上的中文字体兼容问题。

## 3. 知识点总结

### 3.1 虚拟环境与依赖隔离

Python 虚拟环境为单个项目提供独立的解释器入口和第三方包目录。课程使用 `.venv` 后，安装 `pandas`、`yfinance` 等依赖不会污染系统 Python，也能避免不同项目要求不同版本时互相冲突。激活虚拟环境主要是修改终端的 `PATH`；需要明确指定环境时，直接使用 `.venv/bin/python` 最可靠。

### 3.2 Python 解释器与 Jupyter Kernel

终端解释器和 Notebook Kernel 是两个可以独立选择的运行入口：终端激活 `.venv`，不代表已经打开的 Notebook 会自动切换 Kernel。Jupyter Kernel 本质上是一个长期运行的 Python 进程，其 kernelspec 会记录启动命令和解释器位置。项目环境需要安装 `ipykernel` 并注册专用 Kernel，Notebook 中可以通过 `sys.executable` 检查实际使用的解释器。

### 3.3 JupyterLab 的作用

JupyterLab 负责提供 Notebook 编辑界面并把代码发送给 Kernel 执行，真正决定依赖是否可用的是 Kernel 背后的 Python 环境。使用 `.venv/bin/python -m jupyter lab`，可以确保从项目环境启动 Jupyter，减少误用系统安装版本的可能。

### 3.4 课程依赖与图表字体

- `numpy`、`pandas`：数值计算和表格数据处理。
- `matplotlib`、`seaborn`：行情与统计图表可视化。
- `yfinance`、`akshare`：获取海外和国内市场数据。
- `scikit-learn`：后续机器学习实验。

Matplotlib 字体属于系统资源，不会因为安装 Python 包而自动具备。只指定 Windows 常见的 `SimHei`，在 macOS 上可能出现字体缺失；配置 `Heiti SC`、`PingFang SC` 等候选字体并保留跨平台回退，可以让同一 Notebook 在不同系统上正常显示中文。

### 3.5 关键函数、命令与环境诊断算法

#### 函数与接口速查

| 函数或接口 | 关键参数 | 返回值 | 本 Task 中的用途 |
| --- | --- | --- | --- |
| `sys.executable` | 无；这是只读属性 | 当前进程使用的 Python 可执行文件绝对路径 | 判断 Notebook 或脚本究竟运行在哪个解释器中 |
| `importlib.util.find_spec(name)` | `name` 是导入模块名，如 `sklearn` | 找到时返回 `ModuleSpec`，找不到时返回 `None` | 在不真正导入模块的情况下检查当前环境能否定位依赖 |
| `importlib.metadata.version(name)` | `name` 是安装分发名，如 `scikit-learn` | 已安装版本字符串；未安装时抛出 `PackageNotFoundError` | 检查当前虚拟环境里的包版本 |
| `module.__version__` | 无；模块可能不提供该属性 | 模块声明的版本字符串 | 导入成功后快速查看 `jupyterlab`、`numpy` 等版本 |
| `plt.rcParams[key]` | Matplotlib 配置项名称 | 读取时返回当前配置，赋值时修改后续图表的全局默认值 | 设置中文字体候选列表和负号显示方式 |
| `python -m module` | `module` 是当前解释器环境中的模块 | 启动该模块的命令行入口 | 确保 `jupyter`、`pip` 等工具来自指定 Python 环境 |

分发名和导入名不一定相同。例如安装与查询版本时使用 `scikit-learn`，代码导入和 `find_spec()` 检查时使用 `sklearn`。如果混用这两个名称，可能出现“包明明安装了却查询不到”的误判。

#### 环境诊断流程

1. 用 `sys.executable` 或 `.venv/bin/python` 确认解释器路径，避免先在错误环境里反复安装包。
2. 检查 Python 版本是否满足课程要求，再用 `version()` 或导入测试核对依赖。
3. 在 Notebook 中再次打印 `sys.executable`，确认 Kernel 与终端使用同一个 `.venv`。
4. 运行最小绘图测试；依赖正常但中文缺字时，再检查 `plt.rcParams` 和系统字体。
5. 每修复一层就重新验证，不同时改动解释器、Kernel、依赖和字体，以免无法判断问题来源。

#### 复杂度与边界情况

假设要检查 $m$ 个依赖、模块搜索路径有 $p$ 个位置，逐个调用 `find_spec()` 的查找成本可近似写为 $O(mp)$；实际环境规模很小时，主要耗时通常来自磁盘访问和真正导入大型包。版本查询只读取已安装包的元数据，通常比完整导入更快。

- 终端激活 `.venv` 不会自动改变已经启动的 Notebook Kernel。
- `find_spec()` 能找到模块只说明“可以定位”，不保证导入时不会因二进制库或子依赖问题失败。
- 并非所有第三方模块都有 `__version__`，更通用的做法是用 `importlib.metadata.version()`。
- 字体名称存在平台差异，应提供候选列表；候选字体全部不存在时，Matplotlib 仍会回退并可能出现缺字警告。

#### 最小示例

```python
import sys
from importlib.metadata import PackageNotFoundError, version
from importlib.util import find_spec

import matplotlib.pyplot as plt

packages = {
    'numpy': 'numpy',
    'pandas': 'pandas',
    'scikit-learn': 'sklearn',
}

print('解释器：', sys.executable)
for distribution_name, import_name in packages.items():
    spec = find_spec(import_name)
    try:
        installed_version = version(distribution_name)
    except PackageNotFoundError:
        installed_version = '未安装'
    print(import_name, '可定位：', spec is not None, '版本：', installed_version)

plt.rcParams['font.sans-serif'] = [
    'Heiti SC', 'PingFang SC', 'Microsoft YaHei', 'SimHei',
    'DejaVu Sans',
]
plt.rcParams['axes.unicode_minus'] = False
```

## 4. 运行结果与学习记录

### 4.1 运行代码

```bash
.venv/bin/python --version
.venv/bin/python -c "import jupyterlab; print(f'JupyterLab {jupyterlab.__version__}')"
.venv/bin/python -c "import numpy, pandas, matplotlib, seaborn, yfinance, akshare, sklearn; print('imports-ok')"
```

### 4.2 运行输出

```text
Python 3.11.15
JupyterLab 4.6.1
imports-ok
```

项目虚拟环境满足仓库要求的 Python 3.10+，课程所需的主要数据分析、绘图、行情获取和机器学习库均可正常导入。

### 4.3 学习记录

排障过程中先后解决了两个问题：

- 系统 `python3` 仍为低于课程要求的 3.9.6，VS Code 最初选择的另一套 Python 3.11 又缺少 `ipykernel` 和课程依赖，导致 Notebook 无法运行。最终在项目中创建 `.venv`、安装完整依赖、注册专用内核，并切换到 `Python (quant-for-beginners)`。
- 环境就绪后，Matplotlib 因 Notebook 写死 Windows 字体 `SimHei` 而无法正常显示中文。随后为全部 8 个 Notebook 增加 `Heiti SC`、`PingFang SC` 等跨平台字体回退，并验证中文图表不再出现缺字警告。

## 5. 学习心得

这次经历让我认识到，Notebook 能否运行不仅取决于是否安装 Python，还取决于 VS Code 选择的解释器、Jupyter Kernel、项目依赖和系统字体是否彼此匹配。后续运行课程前应先激活 `.venv`，并确认 Notebook 使用项目专用内核。

## 6. 还没完全懂的问题

我还想进一步理解虚拟环境、Python 解释器与 Jupyter Kernel 之间的对应关系：为什么终端里激活了某个虚拟环境，Notebook 有时仍可能选到另一个 Kernel，以及应该如何稳定地检查和切换。
