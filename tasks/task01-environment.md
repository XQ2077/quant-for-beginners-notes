# Quant-for-Beginners Task1：环境配置学习笔记

日期：2026-07-13

## 1. 今天学习的 Task

本次完成 Quant-for-Beginners 的 Task1：环境配置。目标是确认课程项目、Python 虚拟环境、JupyterLab 和课程依赖均可正常使用，为后续运行量化金融 Notebook 做准备。

## 2. 完成的课程要求

- 已获取课程项目仓库并确认项目目录结构。
- 已使用项目独立虚拟环境 `.venv`，避免课程依赖污染系统 Python。
- 已检查 `requirements.txt` 中的主要依赖。
- 已确认 JupyterLab 可以通过项目虚拟环境运行。
- 已成功导入 `numpy`、`pandas`、`matplotlib`、`seaborn`、`yfinance`、`akshare` 和 `scikit-learn`。

## 3. 运行结果

```text
$ .venv/bin/python --version
Python 3.11.15

$ .venv/bin/python -m jupyter --version
JupyterLab 4.6.1

$ .venv/bin/python -c "import numpy, pandas, matplotlib, seaborn, yfinance, akshare, sklearn"
imports-ok
```

项目虚拟环境满足仓库要求的 Python 3.10+，课程所需的主要数据分析、绘图、行情获取和机器学习库均可正常导入。

## 4. 踩坑与学习记录

直接调用系统 `python3` 时，版本是 Python 3.9.6，而且没有安装 Jupyter。这说明“电脑上有 Python”并不代表当前终端使用的是课程项目的 Python。后续运行课程内容前，需要先激活 `.venv`，或者明确使用 `.venv/bin/python` 和 `.venv/bin/jupyter`。

这次配置让我理解到，虚拟环境的作用不仅是隔离依赖，也能让同一台电脑上的不同项目使用不同的 Python 和软件包版本，减少版本冲突。

## 5. 还没完全懂的问题

我还想进一步理解虚拟环境、Python 解释器与 Jupyter Kernel 之间的对应关系：为什么终端里激活了某个虚拟环境，Notebook 有时仍可能选到另一个 Kernel，以及应该如何稳定地检查和切换。
