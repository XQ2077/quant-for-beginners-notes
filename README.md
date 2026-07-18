# Quant-for-Beginners 学习笔记

Datawhale 第 82 期「Quant-for-Beginners 中文零基础量化金融」的持续学习记录，集中保存每个 Task 的笔记、运行结果、问题和配套图片。

- [课程项目](https://github.com/datawhalechina/quant-for-beginners)
- [Task1：环境配置学习笔记](tasks/task01-environment.md)
- [Task2：什么是量化金融学习笔记](tasks/task02-what-is-quant-finance.md)
- [Task3：你的第一个量化实验学习笔记](tasks/task03-first-quant-experiment.md)
- [Task4：移动平均线策略学习笔记](tasks/task04-moving-average-strategy.md)

## 学习进度

| Task | 主题 | 状态 | 完成日期 |
| --- | --- | --- | --- |
| Task1 | 环境配置 | 已完成 | 2026-07-13 |
| Task2 | 第一章：什么是量化金融 | 已完成 | 2026-07-14 |
| Task3 | 第二章：你的第一个量化实验 | 已完成 | 2026-07-16 |
| Task4 | 第三章：移动平均线策略 | 已完成 | 2026-07-18 |
| Task5–Task9 | 按课程公告逐步补充 | 待学习 | — |

## 仓库结构

```text
quant-for-beginners-notes/
├── README.md                    # 总目录、学习进度与使用说明
├── tasks/
│   ├── task01-environment.md    # Task1：环境配置
│   ├── task02-what-is-quant-finance.md
│   ├── task03-first-quant-experiment.md
│   ├── task04-moving-average-strategy.md
│   └── taskXX-topic.md          # 后续按两位编号持续添加
└── assets/
    └── README.md                # 截图、图表等附件的存放约定
```

每篇 Task 笔记统一使用以下六个模块，名称和顺序保持一致：

1. `今天学习的 Task`
2. `完成的课程要求`
3. `知识点总结`
4. `运行结果与学习记录`
5. `学习心得`
6. `还没完全懂的问题`

知识点总结用于沉淀可复用的概念、公式和代码方法。每篇笔记的“运行结果与学习记录”继续统一细分为：

1. `运行代码`：放置能够独立复现实验的完整代码或命令。
2. `运行输出`：保留本次实际执行的文字结果和图表截图。
3. `学习记录`：解释结果、记录观察和排障过程。

行情代码使用滚动时间窗口时，需要注明结果对应的完成日期以及未来重跑可能发生变化。图片统一放入 `assets/taskXX/`，并从对应笔记使用相对路径引用。

## 本地查看与同步

首次下载：

```bash
git clone https://github.com/XQ2077/quant-for-beginners-notes.git
cd quant-for-beginners-notes
```

每次开始编辑前先同步远端：

```bash
git pull --rebase
```

编辑 `tasks/taskXX-topic.md` 后，可以在 GitHub 网页或 VS Code 的 Markdown Preview 中预览。确认内容后提交并推送：

```bash
git add README.md tasks/ assets/
git commit -m "docs: add taskXX notes"
git push
```

如果终端尚未登录 GitHub，需要先运行 `gh auth login -h github.com -w` 完成认证。
