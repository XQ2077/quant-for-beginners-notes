# Assets

按 Task 保存学习笔记使用的截图、图表和其他附件：

```text
assets/
├── task01/
├── task02/
└── taskXX/
```

Git 不记录空目录，因此只有在某个 Task 首次产生附件时才创建对应目录。文件名使用小写英文和连字符，例如 `jupyter-version.png`，并在笔记中使用相对路径引用：

```markdown
![Jupyter 版本](../assets/task01/jupyter-version.png)
```
