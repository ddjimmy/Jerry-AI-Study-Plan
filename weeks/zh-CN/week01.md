# 第 1 周：面向 AI 的 Python — 游戏数据统计分析器

[English Version](../en/week01.md)

## 本周任务

构建**游戏数据统计分析器 v1（Game Statistics Analyzer v1）**：这个命令行程序需要从 CSV 读取游戏场次数据，计算有用的统计指标，在终端打印清晰的报告，并写入一份 JSON 汇总文件。

每个工作日投入 60–90 分钟，大约 30% 的时间学习，70% 的时间动手构建、测试、调试并提交具体成果。AI 可以解释、提示、协助调试学生已经尝试过的代码，也可以进行代码审查，但不能代写完整答案。无法解释的重要代码不算完成。

先阅读[项目 README](../../projects/game-statistics-analyzer/README.zh-CN.md)，并在以下目录中完成任务：

```text
projects/game-statistics-analyzer/
```

本周结束时，项目结构应大致如下：

```text
projects/game-statistics-analyzer/
├── README.md
├── README.zh-CN.md
├── data/
│   └── game_sessions.csv
├── src/
│   ├── hello_ai.py
│   ├── player_stats.py
│   ├── csv_analyzer.py
│   └── analyzer.py
└── output/
    └── summary.json
```

不要从 AI 或其他来源复制一个完成版 analyzer。把程序拆成小步骤，频繁运行，并记录遇到的 bug 和作出的决定。

## 第 1 天：开发环境与 Git

### 任务

验证 Python、Git、VS Code、仓库和本地虚拟环境能够配合工作，并创建、运行第一个小型 Python 文件。

### 建议时间安排

- 15–20 分钟：检查工具并理解命令。
- 35–50 分钟：创建环境和脚本，然后运行和调试。
- 10–15 分钟：讲解工作流程、检查 diff 并 commit。

### 学习

- Git 是版本控制工具，GitHub 是托管代码仓库的服务，两者有什么区别。
- Python 运行一个 `.py` 文件时会发生什么。
- 为什么虚拟环境可以隔离不同项目使用的 Python 包。

### 动手构建

1. 打开终端并检查工具：

   ```bash
   python --version
   git --version
   ```

2. 如果电脑上还没有本仓库，先克隆：

   ```bash
   git clone https://github.com/ddjimmy/Jerry-AI-Study-Plan.git
   cd Jerry-AI-Study-Plan
   ```

3. 在仓库根目录创建虚拟环境：

   ```bash
   python -m venv .venv
   ```

4. 在 PowerShell 中激活：

   ```powershell
   .\.venv\Scripts\Activate.ps1
   ```

   如果使用 macOS 或 Linux：

   ```bash
   source .venv/bin/activate
   ```

5. 创建 `projects/game-statistics-analyzer/src/hello_ai.py`。文件只需要打印一条简短消息，用来证明 Python 成功运行了它。

6. 从仓库根目录运行：

   ```bash
   python projects/game-statistics-analyzer/src/hello_ai.py
   ```

7. Commit 之前检查改动：

   ```bash
   git status
   git diff
   ```

### 必需文件

```text
projects/game-statistics-analyzer/src/hello_ai.py
```

### 预期证据

- 两条版本命令都显示已安装的版本。
- 终端提示符表明 `.venv` 已激活。
- 运行 `hello_ai.py` 后，消息只打印一次且没有报错。
- `git status` 显示新增脚本。

### 验收标准

- [ ] `python --version` 和 `git --version` 成功执行。
- [ ] `.venv` 存在于本地，而且没有被提交。
- [ ] `hello_ai.py` 包含 Jerry 自己写的一条简单 `print(...)` 语句。
- [ ] 脚本可以从仓库根目录运行，退出码为 0。
- [ ] Jerry 不看准备好的答案，也能解释下面三个概念。

### 讲解检查

1. Git 和 GitHub 有什么区别？
2. `python path/to/file.py` 要求电脑做什么？
3. 为什么开发人员会为项目使用独立的虚拟环境？

### 建议 Git Commit

```text
week1 day1: verify Python development environment
```

### 可选挑战

激活虚拟环境前后分别运行 `python -c "import sys; print(sys.executable)"`，并解释输出路径为什么不同。

## 第 2 天：Python 核心数据结构

### 任务

使用 Python 核心数据结构和控制流程，构建一个处理内存数据的玩家统计分析器。

### 建议时间安排

- 20 分钟：复习 list、dict、循环、条件语句和除法。
- 45–55 分钟：创建数据、计算统计结果并调试边界情况。
- 10–15 分钟：解释设计选择并 commit。

### 学习

- Dictionary 使用有名称的字段描述一名玩家。
- List 按顺序保存多名玩家的 dictionary。
- `for` 循环为每名玩家重复执行操作。
- `if` 条件决定某种情况下执行什么操作。
- 计算 K/D 比率之前必须先处理除以零的情况。

### 动手构建

创建 `projects/game-statistics-analyzer/src/player_stats.py`。

在脚本中：

1. 创建一个 list，其中至少包含 10 个玩家 dictionary。
2. 每名玩家都包含以下字段：

   ```text
   name
   kills
   deaths
   accuracy
   ```

3. 至少设置一名玩家的 `deaths = 0`。
4. 使用循环和 `if` 条件计算每名玩家的 K/D 比率，程序不能崩溃。
5. 为零死亡选择并记录一条清晰规则，例如显示 `Perfect`，或者在排行榜中把比率视为无穷大。
6. 计算并打印：
   - 每名玩家的 K/D 比率；
   - K/D 最高的玩家；
   - K/D 最低的玩家；
   - 平均击杀数；
   - 按 K/D 从高到低排列的排行榜。
7. 从仓库根目录运行：

   ```bash
   python projects/game-statistics-analyzer/src/player_stats.py
   ```

### 必需文件

```text
projects/game-statistics-analyzer/src/player_stats.py
```

### 必须使用的 Python 功能

- `list`
- `dict`
- `for` 循环
- `if` 条件

### 验收标准

- [ ] 至少分析 10 名玩家。
- [ ] 每名玩家都有 `name`、`kills`、`deaths` 和 `accuracy` 数据。
- [ ] 至少一名玩家的死亡数为零，程序不会抛出 `ZeroDivisionError`。
- [ ] 所有要求的统计结果和按 K/D 降序排列的排行榜都会打印。
- [ ] 平均值由数据计算得出，而不是直接把答案写入程序。
- [ ] 对零死亡的处理规则记录在简短代码注释或项目笔记中。
- [ ] Jerry 能解释所用的数据结构和控制流程。

### 讲解检查

1. 为什么 dictionary 适合表示一名玩家？
2. 为什么 list 适合保存多名玩家？
3. 为什么计算 K/D 之前必须处理除以零？
4. `for` 和 `if` 有什么区别？

### 建议 Git Commit

```text
week1 day2: build player statistics analyzer
```

### 可选挑战

再按 accuracy 为玩家排名，并解释 accuracy 排行榜和 K/D 排行榜为什么回答的是不同问题。

## 第 3 天：函数与重构

### 任务

重构第 2 天的程序，把计算、选择、求平均值和显示结果分别放入可复用的函数。

### 建议时间安排

- 15–20 分钟：学习参数、返回值和局部变量。
- 45–55 分钟：每次重构并测试一个函数。
- 10–15 分钟：比较重构前后的代码、讲解并 commit。

### 学习

- 参数（parameter）为函数提供输入。
- 返回值（return value）把结果交还给调用方。
- 局部变量（local variable）只属于某一次函数调用。
- 一个职责清晰的函数应该只负责一件明确的事情。
- 返回数据和打印数据是两种不同的工作。

### 动手构建

使用至少四个 Jerry 自己编写的函数重构 `player_stats.py`。函数职责应类似于：

```text
calculate_kd(...)
find_best_player(...)
find_worst_player(...)
calculate_average_kills(...)
print_leaderboard(...)
```

这些名称只是设计提示，不是实现代码。如果每个函数的用途仍然清楚，你可以改进名称。

另外还要：

1. 保留第 2 天对零死亡的处理方式。
2. 至少处理一种无效输入，例如负数 kills、负数 deaths 或非数字值。
3. 在适合的情况下，让计算函数通过 `return` 返回结果。
4. 用单独的显示函数处理终端输出格式。
5. 每完成一个小步骤就运行脚本：

   ```bash
   python projects/game-statistics-analyzer/src/player_stats.py
   ```

### 必需文件

```text
projects/game-statistics-analyzer/src/player_stats.py
```

### 验收测试

在代码注释、学习记录或终端输出中记录以下三种测试结果：

| 情况 | 输入 | 预期行为 |
|---|---|---|
| 普通玩家 | kills 和 deaths 都是正数 | 返回计算后的 K/D 比率 |
| 零死亡玩家 | `deaths = 0` | 按记录的规则处理，并且不崩溃 |
| 无效玩家 | 一个字段值无效 | 明确拒绝或报告该值，不产生误导性统计结果 |

### 验收标准

- [ ] 脚本至少包含四个 Jerry 自己编写的函数。
- [ ] 函数名称和参数能表达各自职责。
- [ ] 计算函数返回可复用的结果，而不是只负责打印。
- [ ] 普通、零死亡和无效输入三种情况都已运行并检查。
- [ ] 重构后排行榜和统计汇总仍能正常工作。
- [ ] Jerry 能指出与第 2 天版本相比，哪些部分变得更简单。

### 讲解检查

1. `return` 为什么有用？
2. 为什么几个职责清晰的函数比一个很长的脚本更容易维护？
3. 重构后哪些工作变得更简单？
4. 你选择了哪种无效输入，程序如何处理它？

### 建议 Git Commit

```text
week1 day3: refactor statistics logic into functions
```

### 可选挑战

为三种验收情况添加几个 Jerry 自己编写的 `assert` 检查，并解释 assertion 能证明什么。

## 第 4 天：读取 CSV 与输出 JSON

### 任务

使用真实 CSV 数据集替换硬编码数据，计算汇总统计结果，并且只使用 Python 标准库把结果保存为 JSON。

### 建议时间安排

- 15–20 分钟：检查 CSV，学习 `csv` 和 `json` 基础。
- 50–55 分钟：解析、转换、计算、处理缺失数据并写入 JSON。
- 10–15 分钟：检查输出、讲解并 commit。

### 学习

- CSV 使用一行表头和每条记录一行数据来保存表格文本。
- 从 CSV 读入的值最初都是字符串，进行计算前需要主动转换类型。
- JSON 保存其他程序可以读取的结构化数据。
- 应先识别缺失值，再根据明确规则处理。

第 1 周不要使用 Pandas，只使用：

```python
import csv
import json
```

### 动手构建

创建：

```text
projects/game-statistics-analyzer/src/csv_analyzer.py
```

仓库提供的数据集位于：

```text
projects/game-statistics-analyzer/data/game_sessions.csv
```

它正好包含 15 个游戏场次和以下字段：

```text
game_id,date,duration_minutes,kills,deaths,accuracy,result,reaction_time_ms
```

脚本必须：

1. 使用标准库 `csv` 模块读取所有行。
2. 先转换数字字段，再进行计算。
3. 统计游戏场数。
4. 使用全部 15 场数据计算平均击杀数和平均死亡数。
5. 找出一个空白的 `accuracy` 值。
6. 只在 accuracy 平均值中排除这个空白值。
7. 必要时创建 `output/` 目录。
8. 写入 `projects/game-statistics-analyzer/output/summary.json`，其中包含：

   ```text
   total_games
   average_kills
   average_deaths
   average_accuracy
   missing_accuracy_count
   ```

9. 从仓库根目录运行：

   ```bash
   python projects/game-statistics-analyzer/src/csv_analyzer.py
   ```

10. 再确认程序可以从项目目录运行：

    ```bash
    cd projects/game-statistics-analyzer
    python src/csv_analyzer.py
    ```

文件路径应相对于脚本或项目目录进行定位，确保两种运行方式都能找到同一份输入和输出文件。

### 必需文件

```text
projects/game-statistics-analyzer/src/csv_analyzer.py
projects/game-statistics-analyzer/output/summary.json
```

### 预期结果

```text
Total games: 15
Average kills: 14.00
Average deaths: 8.60
Average accuracy: 41.57%
Missing accuracy values: 1
```

程序必须从 CSV 计算这些值，不能硬编码答案。

### 验收标准

- [ ] 脚本使用 `csv` 和 `json`，不使用 Pandas。
- [ ] 从提供的文件中正好读取 15 行数据。
- [ ] 计算前已经把数字字符串转换成数值类型。
- [ ] 缺失的 accuracy 只计数一次，而且不会进入 accuracy 平均值的分母。
- [ ] 五个预期汇总结果全部匹配。
- [ ] `summary.json` 是有效 JSON，并包含所有必需键。
- [ ] 文档中的两个运行位置都能正常工作。
- [ ] Jerry 能解释数据流程和缺失值处理规则。

### 讲解检查

1. 为什么 CSV 中的数字读入 Python 后最初是字符串？
2. 为什么 accuracy 平均值除以 14 而不是 15？
3. CSV 输入和 JSON 输出有什么区别？
4. 你如何让文件路径在两个运行位置都能工作？

### 建议 Git Commit

```text
week1 day4: add CSV input and JSON summary output
```

### 可选挑战

计算胜率。数据集包含 9 场胜利和 6 场失败，因此结果应为：

```text
Win rate: 60%
```

## 第 5 天：游戏数据统计分析器 v1

### 任务

创建最终整合版分析器，验证所有必需结果，完善项目文档，并准备可运行的演示。

### 建议时间安排

- 10–15 分钟：复习第 3 天和第 4 天的代码。
- 50–60 分钟：整合函数、加入最佳/最差场次选择并测试输出。
- 15 分钟：更新 README 笔记、检查 Git 历史并 commit。

### 学习

- 集成（integration）会把较小的可运行部分组合成一个程序。
- 验收标准让“完成”变成可以观察和验证的结果。
- Tie-breaker 让选择结果保持确定性。
- README 应让其他人能够运行并理解项目。

### 动手构建

创建：

```text
projects/game-statistics-analyzer/src/analyzer.py
```

最终脚本必须读取：

```text
projects/game-statistics-analyzer/data/game_sessions.csv
```

并生成：

```text
projects/game-statistics-analyzer/output/summary.json
```

它必须从 CSV 计算全部结果，使用职责清晰的函数，处理缺失的 accuracy，并且完全按照以下标签和值打印：

```text
Games analyzed: 15
Average kills: 14.00
Average deaths: 8.60
Average accuracy: 41.57%
Best game: G008
Worst game: G014
Missing accuracy values: 1
```

定义：

- **最佳场次：** kills 最高的游戏。
- **最差场次：** kills 最低的游戏。
- 如果多场游戏 kills 相同，而且双方都有 accuracy，则用更高的 accuracy 作为 tie-breaker。
- 如果无法比较 accuracy，则保留它们在 CSV 中的先后顺序，并记录这个决定。

从仓库根目录运行：

```bash
python projects/game-statistics-analyzer/src/analyzer.py
```

然后从项目目录运行：

```bash
cd projects/game-statistics-analyzer
python src/analyzer.py
```

检查生成的 JSON 和代码改动：

```bash
git status
git diff
git log --oneline -5
```

在项目 README 中加入简短的“What I Learned”记录，以及你解决的一个 bug 或困难。修改重要运行说明或项目状态时，要保持英文和中文项目 README 内容同步。

### 必需文件

```text
projects/game-statistics-analyzer/src/analyzer.py
projects/game-statistics-analyzer/output/summary.json
projects/game-statistics-analyzer/README.md
projects/game-statistics-analyzer/README.zh-CN.md
```

### 验收标准

- [ ] 最终脚本读取 CSV，而不是硬编码答案。
- [ ] 使用 Jerry 自己编写、职责清晰的函数。
- [ ] 七行必需终端输出的值和格式全部匹配。
- [ ] 根据 kills 选择 `G008` 为最佳场次、`G014` 为最差场次。
- [ ] 缺失的 accuracy 不会导致崩溃，也不会被错误地当成零。
- [ ] `summary.json` 有效，并包含五个必需键。
- [ ] 变量名、函数名、文件名和源代码注释都使用英文。
- [ ] 程序可以从文档中的两个位置运行。
- [ ] README 文件包含清晰的运行说明，并保持完整的双语对应。
- [ ] Jerry 能解释每个重要部分，包括 AI 协助生成的任何代码。

### 讲解检查

1. 数据如何从 CSV 文件流向终端和 JSON 输出？
2. 哪个函数最重要，为什么？
3. 哪个 bug 或困难最需要思考？
4. 程序如何处理缺失的 accuracy？
5. 为什么不能把计算结果直接写入程序？

### 建议 Git Commit

```text
week1 day5: complete game statistics analyzer v1
```

### 可选挑战

在不修改必需输出标签的前提下，加入 60% 胜率和平均反应时间。

## 每周演示

在第 1 周结束时进行一次简短的现场演示，展示：

1. 作为输入的 `data/game_sessions.csv`。
2. `src/analyzer.py` 和其中最重要的函数。
3. 全新运行后得到的必需终端输出。
4. 生成的 `output/summary.json`。
5. 使用 `git log --oneline` 展示五天的 commits。

不看准备好的讲稿，回答以下问题：

1. 数据如何从 CSV 流向输出？
2. 哪个函数最重要？
3. 遇到了什么 bug 或困难？
4. 缺失数据是如何处理的？
5. 如果数据集有 100,000 行，需要改变什么？

## 家长复盘

| 项目 | 分值 |
|---|---:|
| 程序可以正确运行 | 2 |
| 统计结果正确 | 2 |
| 代码通过函数合理组织 | 2 |
| Git commits 展示每日进度 | 1 |
| README/运行说明清晰 | 1 |
| Jerry 能解释代码和一个 bug | 2 |
| **总分** | **10** |

建议通过目标：**8/10**。

分数较低不代表要重新开始第 1 周。补齐缺失项目，再做一次 commit，然后重新演示。
