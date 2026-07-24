# Quant-for-Beginners 学习笔记

Datawhale 第 82 期「Quant-for-Beginners 中文零基础量化金融」的持续学习记录。本仓库集中保存每个 Task 的 Markdown 笔记、可复现代码、实际运行结果、学习问题和配套图表。

## 项目说明

- [课程项目](https://github.com/datawhalechina/quant-for-beginners)：保存课程 Notebook、章节代码和原始教学材料。
- 当前笔记仓库：保存个人学习记录和从 Notebook 实际运行结果中提取的图片，不复制或发布课程 Notebook。

## 快速导航

- [Task1：环境配置学习笔记](tasks/task01-environment.md)
- [Task2：什么是量化金融学习笔记](tasks/task02-what-is-quant-finance.md)
- [Task3：你的第一个量化实验学习笔记](tasks/task03-first-quant-experiment.md)
- [Task4：移动平均线策略学习笔记](tasks/task04-moving-average-strategy.md)
- [Task5：策略回测学习笔记](tasks/task05-strategy-backtest.md)
- [Task6：理解波动率学习笔记](tasks/task06-understanding-volatility.md)
- [Task7：夏普比率与 Beta 学习笔记](tasks/task07-sharpe-ratio-and-beta.md)

## 学习进度

Task1 用于准备课程环境；Task2–Task9 依次对应课程第 1–8 章。

| Task | 对应内容 | 状态 | 完成日期 |
| --- | --- | --- | --- |
| Task1 | 环境配置 | 已完成 | 2026-07-13 |
| Task2 | 第一章：什么是量化金融 | 已完成 | 2026-07-14 |
| Task3 | 第二章：你的第一个量化实验 | 已完成 | 2026-07-16 |
| Task4 | 第三章：移动平均线策略 | 已完成 | 2026-07-18 |
| Task5 | 第四章：策略回测 | 已完成 | 2026-07-20 |
| Task6 | 第五章：理解波动率 | 已完成 | 2026-07-22 |
| Task7 | 第六章：夏普比率与 Beta | 已完成 | 2026-07-24 |
| Task8 | 第七章：最大回撤与仓位管理 | 待学习 | — |
| Task9 | 第八章：多标的组合与相关性 | 待学习 | — |

## 仓库结构

以下目录树与当前仓库中的实际文件一致：

```text
quant-for-beginners-notes/
├── .gitignore
├── README.md
├── tasks/
│   ├── task01-environment.md
│   ├── task02-what-is-quant-finance.md
│   ├── task03-first-quant-experiment.md
│   ├── task04-moving-average-strategy.md
│   ├── task05-strategy-backtest.md
│   ├── task06-understanding-volatility.md
│   └── task07-sharpe-ratio-and-beta.md
└── assets/
    ├── README.md
    ├── task02/
    │   └── tsla-price-volume.png
    ├── task03/
    │   ├── mu-tsla-nvda-return-histogram.png
    │   └── mu-tsla-nvda-return-volatility-comparison.png
    ├── task04/
    │   └── tsla-ma-crossover-comparison.png
    ├── task05/
    │   └── nvda-backtest-vs-benchmarks.png
    ├── task06/
    │   └── msft-return-volatility-challenge.png
    └── task07/
        └── sharpe-beta-challenge.png
```

当前只为已经完成的 Task 保存笔记和附件。后续 Task 完成后再创建对应文件，不预先提交空目录或占位笔记。

## 笔记与附件规范

每篇 Task 笔记统一使用以下六个二级模块，名称和顺序保持一致：

1. `今天学习的 Task`
2. `完成的课程要求`
3. `知识点总结`
4. `运行结果与学习记录`
5. `学习心得`
6. `还没完全懂的问题`

“知识点总结”除概念与公式外，还应包含与本 Task 实验直接相关的“关键函数与算法”：

1. 用速查表记录函数或方法、关键参数、返回值和实际用途。
2. 用编号步骤说明从输入数据到输出结果的算法流程。
3. 说明主要时间与空间复杂度，以及空数据、缺失值、索引、参数和未来函数等边界情况。
4. 提供一段与当前实验一致、能够独立阅读和通过语法检查的最小代码示例。

“运行结果与学习记录”继续细分为三个三级模块：

1. `运行代码`：保存能够独立复现实验的完整代码或命令。
2. `运行输出`：保存本次实际执行得到的文字结果和图表截图。
3. `学习记录`：解释运行结果，记录观察、踩坑和排障过程。

维护约定：

- 笔记使用 `tasks/taskXX-topic.md` 格式命名，Task 编号固定为两位，主题使用小写英文和连字符。
- 图片放在对应的 `assets/taskXX/` 中，文件名使用小写英文和连字符，并从笔记使用相对路径引用。
- 行情代码使用 `period='6mo'`、`'1y'` 等滚动窗口时，需要注明结果对应的完成日期；未来重新运行时，交易日、价格和统计值可能随行情更新。
- 课程 Notebook 保留在课程项目中，本仓库只同步整理后的笔记和必要的实际输出图片。

## 本地查看与同步

首次下载笔记仓库：

```bash
git clone https://github.com/XQ2077/quant-for-beginners-notes.git
cd quant-for-beginners-notes
```

每次开始编辑前先同步远端：

```bash
git pull --rebase
```

编辑 `tasks/taskXX-topic.md` 后，可以使用 GitHub 网页预览，或在 VS Code 中打开 Markdown Preview 检查标题、公式、代码块和图片。

提交时只暂存本次实际修改的文件。以下命令按修改内容选择执行，不需要全部运行：

```bash
git add tasks/taskXX-topic.md  # 修改或新增学习笔记时
git add assets/taskXX/        # 新增配套图片时
git add README.md             # 更新进度或目录时

git status --short
git diff --cached --check
git commit -m "docs: add taskXX notes"
git push origin main
```

如果终端尚未登录 GitHub，可以运行以下命令并在浏览器中完成认证：

```bash
gh auth login -h github.com -w
```
