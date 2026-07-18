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
