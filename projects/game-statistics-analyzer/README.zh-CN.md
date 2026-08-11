# 游戏数据统计分析器 v1

[English Version](README.md)

## 项目目的

游戏数据统计分析器 v1（Game Statistics Analyzer v1）是 Jerry AI 学习计划第 1 周的实践项目。它读取一个小型游戏场次 CSV 数据集，在终端显示统计结果，并生成 JSON 汇总文件。

这是一个 starter project。仓库会提供数据集和目录结构，但不会提供完整的 Python 解答。Jerry 需要按照[第 1 周任务](../../weeks/zh-CN/week01.md)，在五天内亲自编写这些脚本。

## 第 1 周学习目标

- 验证 Python、Git、VS Code 和虚拟环境的开发流程。
- 使用 list、dict、循环和条件语句分析内存中的数据。
- 把逻辑重构为职责清晰、包含参数和返回值的函数。
- 使用 Python 标准库读取 CSV、写入 JSON。
- 有意识地处理除以零、无效输入和一个缺失的 accuracy 值。
- 构建、测试、讲解并提交一个完整的命令行程序。

## 项目结构

```text
game-statistics-analyzer/
├── README.md
├── README.zh-CN.md
├── data/
│   └── game_sessions.csv
├── src/
│   ├── hello_ai.py       # Jerry 在第 1 天创建
│   ├── player_stats.py   # Jerry 在第 2 天创建
│   ├── csv_analyzer.py   # Jerry 在第 4 天创建
│   └── analyzer.py       # Jerry 在第 5 天创建
└── output/
    └── summary.json      # 由学生程序生成
```

在 Jerry 创建脚本和输出文件之前，仓库使用已提交的 `.gitkeep` 文件保留空的 `src/` 和 `output/` 目录。

## 运行第 4 天脚本

Jerry 创建 `csv_analyzer.py` 后，可以从仓库根目录运行：

```bash
python projects/game-statistics-analyzer/src/csv_analyzer.py
```

也可以进入本项目目录后运行：

```bash
python src/csv_analyzer.py
```

脚本应读取 `data/game_sessions.csv`，并写入 `output/summary.json`。文件路径应相对于脚本或项目目录进行定位，确保以上两条命令都能正常工作。

## 运行第 5 天脚本

从仓库根目录运行：

```bash
python projects/game-statistics-analyzer/src/analyzer.py
```

或者从本项目目录运行：

```bash
python src/analyzer.py
```

## 预期结果

最终终端输出应为：

```text
Games analyzed: 15
Average kills: 14.00
Average deaths: 8.60
Average accuracy: 41.57%
Best game: G008
Worst game: G014
Missing accuracy values: 1
```

生成的 `output/summary.json` 必须包含以下键：

```text
total_games
average_kills
average_deaths
average_accuracy
missing_accuracy_count
```

Bonus 结果：`Win rate: 60%`。

## 学生自主完成规则

starter files 不包含最终实现。AI 可以解释概念、提供提示、协助调试学生已经尝试过的代码，并进行代码审查，但不能代替 Jerry 完成项目。任何 Jerry 无法解释的重要代码都不算完成。
