# 第 2 周：游戏数据智能 + 第一个游戏 Agent

[English Version](../en/week02.md)

## 本周任务

把第 1 周的游戏场次数据转化成有证据支持的结论：先使用 NumPy，再使用 Pandas，接着制作图表。周五构建一个简单的规则型游戏 Agent，并实践 **观察（Observe）→ 决策（Decide）→ 行动（Act）** 模型。

继续在同一个共享项目中学习：

```text
projects/game-statistics-analyzer/
```

使用现有数据集：

```text
projects/game-statistics-analyzer/data/game_sessions.csv
```

不要按语言复制英文和中文源代码。所有文件名、变量名、函数名、终端输出和源代码注释都应使用英文。

到周六时，Jerry 新创建的文件结构应大致如下：

```text
projects/game-statistics-analyzer/
├── data/
│   └── game_sessions.csv
├── output/
│   └── charts/
│       ├── kills_by_game.png
│       ├── reaction_time_vs_accuracy.png
│       └── win_loss_comparison.png
├── src/
│   ├── numpy_stats.py
│   ├── pandas_explorer.py
│   ├── session_analysis.py
│   ├── visualize_sessions.py
│   └── game_agent.py
└── requirements.txt
```

每个工作日投入 60–90 分钟，大约 30% 的时间学习，70% 的时间动手构建。AI 可以解释概念、提供提示、协助调试学生已经尝试过的代码，或者进行代码审查，但不能提供完成版练习。Jerry 无法解释的重要代码不算完成。

## 第 2 周准备

从仓库根目录激活第 1 周创建的虚拟环境。

PowerShell：

```powershell
.\.venv\Scripts\Activate.ps1
```

macOS 或 Linux：

```bash
source .venv/bin/activate
```

安装第 2 周使用的库：

```bash
python -m pip install numpy pandas matplotlib
```

创建或更新：

```text
projects/game-statistics-analyzer/requirements.txt
```

记录三个直接依赖：`numpy`、`pandas` 和 `matplotlib`。不要复制第二份 CSV。

## 周一：NumPy 统计

### 目标

使用 NumPy 数组计算并比较真实游戏场次字段的统计数据。

### 为什么重要

AI 和数据系统通常使用数组表示数值信息。向量化操作（vectorized operation）可以一致地分析整列数据，不需要在循环中手动更新总数。

### 概念

- NumPy 数组和 `dtype`
- shape 和索引
- `mean`、`median`、`min`、`max` 和 `std`
- NumPy 默认使用 `ddof=0` 计算总体标准差
- 布尔掩码（Boolean mask）和筛选
- 向量化操作与 Python 循环的区别

### 任务

创建：

```text
projects/game-statistics-analyzer/src/numpy_stats.py
```

先使用 Python 标准库 `csv` 读取现有 CSV，再把 `kills`、`deaths`、`duration_minutes` 和 `reaction_time_ms` 字段转换成 NumPy 数组。

使用 NumPy 回答：

1. kills 的平均值和中位数是多少？
2. 最小和最大 reaction time 是多少？
3. kills 的总体标准差是多少？
4. 哪些场次至少有 15 个 kills？
5. reaction time 低于 240 ms 的场次有多少？

从仓库根目录运行：

```bash
python projects/game-statistics-analyzer/src/numpy_stats.py
```

### 要求

- 使用现有的 `game_sessions.csv`，不要复制或修改它。
- 至少使用四个 NumPy 数组。
- 使用 NumPy 函数计算要求的统计数据。
- 至少使用两个布尔掩码。
- 筛选结果中包含 `game_id`，以便和 CSV 核对。
- 显示平均值时保留两位小数。
- 确认 kills 数组包含 15 个值，平均值是 `14.00`。

### 提示

- `csv` 模块返回字符串，因此创建数组前要先转换数值。
- “数值大于或等于某个阈值”这样的比较会生成布尔掩码。
- 对 `game_id` 数组和数值数组应用同一个掩码，才能保持行的对应关系。
- 调试时打印 `array.shape` 和 `array.dtype`。

### 完成标准

- [ ] `numpy_stats.py` 可以从仓库根目录运行且没有报错。
- [ ] 五个问题都得到计算结果。
- [ ] kills 平均值是 `14.00`。
- [ ] 筛选结果包含对应的 `game_id`。
- [ ] 要求的统计结果都不是硬编码答案。
- [ ] Jerry 能解释布尔掩码包含什么，以及数组 shape 为什么必须匹配。

### 可选挑战

使用下面的公式标准化 kills：

```text
(value - mean) / standard_deviation
```

找出正向标准化分数最大的场次。NumPy 表达式需要 Jerry 自己推导。

### Git Commit 建议

```text
week2 monday: analyze game statistics with numpy
```

## 周二：Pandas 基础

### 目标

把同一份 CSV 加载到 Pandas DataFrame 中，检查它的结构、数据类型、行、列和描述性统计。

### 为什么重要

Pandas 提供带标签的表格，让数据检查和特征分析更方便。训练模型之前，工程师必须理解每一行和每一列代表什么。

### 概念

- DataFrame、row、column 和 index
- `read_csv`
- `head`、`tail`、`shape` 和 `columns`
- `info` 和 `dtypes`
- `describe`
- 选择一列或多列
- `loc` 和 `iloc`

### 任务

创建：

```text
projects/game-statistics-analyzer/src/pandas_explorer.py
```

把 `data/game_sessions.csv` 加载为 DataFrame，并生成一份紧凑的检查报告，回答：

1. 数据有多少行和多少列？
2. 列名和 Pandas 推断的数据类型是什么？
3. 哪一列包含缺失值？
4. 前五个和最后三个场次是什么？
5. Pandas 为数值列计算了哪些汇总统计？
6. 如何只选择 `game_id`、`kills`、`accuracy` 和 `result`？

从仓库根目录运行：

```bash
python projects/game-statistics-analyzer/src/pandas_explorer.py
```

### 要求

- 使用 `pandas.read_csv`。
- 打印 DataFrame shape；预期为 `(15, 8)`。
- 使用 `head`、`tail`、`info` 和 `describe`。
- 显示列名和数据类型。
- 选择一个 Series 和一个较小的 DataFrame。
- 演示一次 `loc` 选择和一次 `iloc` 选择。
- 找到一个缺失的 `accuracy`，但不修改源 CSV。

### 提示

- 单列的单层方括号选择通常返回 Series；列名 list 通常返回 DataFrame。
- `info()` 会直接打印报告，而不是返回一个可再次打印的表格。
- `isna().sum()` 可以按列统计缺失值。
- 根据脚本位置构建文件路径，这样当前终端目录改变时命令仍然有效。

### 完成标准

- [ ] `pandas_explorer.py` 运行时没有报错。
- [ ] 报告显示 15 行、8 列和一个缺失的 accuracy。
- [ ] Jerry 能指出 DataFrame 的 index、columns 和 values。
- [ ] 同时演示 Series 和 DataFrame 选择。
- [ ] 正确使用并解释 `loc` 和 `iloc`。
- [ ] 没有从第 1 周输出中复制计算结果。

### 可选挑战

把 `game_id` 设为 DataFrame index，再通过标签取得 `G008`。解释有意义的索引什么时候有帮助。

### Git Commit 建议

```text
week2 tuesday: explore game sessions with pandas
```

## 周三：数据清理、筛选、GroupBy 与 WIN/LOSS 分析

### 目标

有意识地清理数据，并通过筛选和分组统计比较获胜场次与失败场次。

### 为什么重要

真实数据经常不完整，而有用的问题通常需要比较不同分组。如果误解了缺失值或进行了不公平的分组比较，模型或建议可能会非常自信地得出错误结论。

### 概念

- 缺失值和 `NaN`
- 转换数值并处理无效值
- `isna`、`dropna` 和 `fillna`
- 使用多个条件筛选
- 使用 `sort_values` 排序
- `groupby` 和聚合（aggregation）
- 保留原始数据，同时创建清理后的 DataFrame

### 任务

创建：

```text
projects/game-statistics-analyzer/src/session_analysis.py
```

加载 CSV 并构建一个清理后的分析 DataFrame。保持原始 CSV 不变。

回答：

1. 哪一行缺少 accuracy？
2. 哪些场次 kills 至少为 15，而且 accuracy 至少为 40？
3. Reaction time 最快的五个场次是什么？
4. 对 `WIN` 和 `LOSS` 分组后，各自的游戏数量以及 kills、deaths、accuracy、reaction time 平均值是多少？
5. 根据这份小型数据集，胜场与负场有什么差异？

至少写三条由证据支持的观察。每条观察都必须引用筛选或分组结果中的一个数值。

从仓库根目录运行：

```bash
python projects/game-statistics-analyzer/src/session_analysis.py
```

### 要求

- 把 `accuracy` 转换为数值，无效值表示为 `NaN`。
- 清理前先统计缺失值。
- 不要把缺失 accuracy 替换成零。
- 选择并解释以下一种规则：
  - 只在 accuracy 计算中排除缺失值；或者
  - 使用有理由的统计量填充，同时保留一个缺失值标记。
- 至少使用两个筛选，其中一个包含多个条件。
- 至少对一个结果排序。
- 使用带多个聚合的 `groupby("result")`。
- 确认分组数量是 9 个 `WIN` 和 6 个 `LOSS`。
- 结论要谨慎：15 个场次只能提示某种模式，不能证明普遍规律。

### 提示

- 使用 `&` 或 `|` 组合条件之前，先给每个条件加括号。
- 不同字段可以使用不同的聚合函数。
- Pandas 在许多数值平均值中默认忽略 `NaN`，但要主动验证，不能想当然。
- 使用清理后的副本，可以在实验时保护原始 DataFrame。

### 完成标准

- [ ] `session_analysis.py` 运行时没有因意外 chained assignment 产生警告。
- [ ] 找到缺失 accuracy 的行，并按有记录的规则处理。
- [ ] 筛选和排序结果包含 `game_id`。
- [ ] 分组表同时包含 `WIN` 和 `LOSS`。
- [ ] 分组数量为 9 胜、6 负。
- [ ] 至少三条书面观察引用了计算证据。
- [ ] Jerry 能解释为什么相关性或分组差异不能证明因果关系。

### 可选挑战

安全处理零死亡后创建 `kd_ratio` 列，再比较胜场和负场的平均 K/D。

### Git Commit 建议

```text
week2 wednesday: compare winning and losing sessions
```

## 周四：Matplotlib 可视化 + 关系探索

### 目标

创建易读图表，并使用基础相关性计算探索游戏表现变量之间的关系。

### 为什么重要

图表能显示表格中不容易发现的分布、异常值和潜在关系。视觉证据也能让技术结果更容易向他人说明。

### 概念

- figure 和 axes
- 折线图/柱状图和散点图
- 标题、坐标轴标签、图例和网格
- `tight_layout` 和 `savefig`
- 正相关、负相关、弱关系和强关系
- Pearson 相关系数
- 相关性不代表因果关系

### 任务

创建：

```text
projects/game-statistics-analyzer/src/visualize_sessions.py
```

使用 Pandas 和 Matplotlib 生成：

1. `kills_by_game.png`：每个 `game_id` 的 kills。
2. `reaction_time_vs_accuracy.png`：reaction time 与 accuracy 的散点图，只在这张图中排除缺失 accuracy 的行。
3. `win_loss_comparison.png`：比较 `WIN` 和 `LOSS` 至少两个平均指标的分组图。

所有图表保存在：

```text
projects/game-statistics-analyzer/output/charts/
```

另外为以下字段计算数值相关矩阵：

```text
duration_minutes
kills
deaths
accuracy
reaction_time_ms
```

选择一对相关字段，用两句话解释：相关系数的正负和大小提示了什么，以及为什么这份数据太小，不能据此声称因果关系。

从仓库根目录运行：

```bash
python projects/game-statistics-analyzer/src/visualize_sessions.py
```

### 要求

- 使用现有 CSV，不要修改它。
- 正好创建三张要求的 PNG，图片内容不能为空。
- 每张图都有说明性的标题和坐标轴标签。
- 出现多个数据系列时添加图例。
- kills 图上的 15 个 game ID 都要可读。
- 处理缺失 accuracy 时，不能把它转换成零。
- 用合理的小数位数打印选中的相关系数。
- 至少写三条基于图表的观察。

### 提示

- 保存前先确认 `output/charts/` 存在。
- 如果 x 轴标签拥挤，可以旋转标签，不要把字体缩得太小。
- 在 `savefig(...)` 前调用 `tight_layout()`。
- 使用两个选定字段都有值的行构建散点图。
- 系数接近 `1` 或 `-1` 表示线性关系较强，接近 `0` 表示线性关系较弱。

### 完成标准

- [ ] `visualize_sessions.py` 可以运行并创建全部三张 PNG。
- [ ] 文件可以打开，而且显示真实数据，不是空白图。
- [ ] 标题、标签、game ID 和图例都清晰可读。
- [ ] 相关矩阵使用五个要求的数值字段。
- [ ] 对一个关系作出解释，但没有声称因果关系。
- [ ] 三条观察都引用了图中的可见证据。
- [ ] Jerry 能解释散点图为什么适合两个数值变量。

### 可选挑战

在一张图上突出显示 `G008` 和 `G014`，并标注它们在第 1 周为什么重要。

### Git Commit 建议

```text
week2 thursday: visualize game performance relationships
```

## 周五：Game AI Friday #1 — 规则型游戏 Agent

### 目标

构建一个小型游戏 Agent：观察游戏状态，按照明确规则进行决策，并返回一个动作。

### 为什么重要

AI Agent 不只是机器学习模型。最简单的 Agent 会接收环境信息、应用策略（policy），然后采取行动。规则型 Agent 能清楚展示 **观察 → 决策 → 行动** 循环，为以后学习经过训练的行为打基础。

### 概念

- Agent、environment、state、action 和 policy
- 观察（Observe）→ 决策（Decide）→ 行动（Act）
- 确定性的规则决策
- 规则优先级
- 纯决策函数
- 测试场景和边界情况

### 任务

创建：

```text
projects/game-statistics-analyzer/src/game_agent.py
```

使用以下字段表示游戏状态：

```text
player_x
goal_x
obstacle_ahead
```

创建一个由 Jerry 自己编写的决策函数，职责类似：

```text
choose_action(game_state)
```

它必须只返回以下一个合法动作：

```text
LEFT
RIGHT
JUMP
STAY
```

设计规则优先级，通过以下可观察场景：

| 场景 | player_x | goal_x | obstacle_ahead | 预期动作 |
|---|---:|---:|---|---|
| 目标在右侧 | 2 | 10 | false | `RIGHT` |
| 目标在左侧 | 12 | 4 | false | `LEFT` |
| 玩家已经到达目标 | 7 | 7 | false | `STAY` |
| 障碍物挡住移动 | 3 | 9 | true | `JUMP` |

从仓库根目录运行：

```bash
python projects/game-statistics-analyzer/src/game_agent.py
```

### 要求

- 把状态观察、决策逻辑和显示动作分成容易理解的部分。
- 使用英文标识符和注释。
- 在合理情况下，让决策函数不依赖 `input()` 和打印。
- 只返回四个允许动作中的一个。
- 有意识地安排障碍物处理优先级，并解释原因。
- 使用一个小型测试循环运行至少四个必需场景。
- 打印每个观察到的状态和选定动作。
- 加入一个 Jerry 自己设计的边界情况。
- 不使用 NumPy、Pandas、机器学习或 LLM 作出决策。

### 提示

- 先写四个测试场景，再写决策规则。
- 当两个规则可能同时适用时，先思考哪个条件应该优先检查。
- 接收 state 并返回 action 的函数，比内部读取键盘输入的函数更容易测试。
- 把测试状态放入一个 collection，这样可以用一个循环检查整个 policy。

### 完成标准

- [ ] `game_agent.py` 不需要交互就能运行，并测试至少五个状态。
- [ ] 四个必需场景都返回预期动作。
- [ ] 每个返回值都属于允许的动作集合。
- [ ] 能在程序中指出 Observe、Decide 和 Act。
- [ ] 只改变测试状态的值，不修改 policy，也能改变动作。
- [ ] Jerry 能解释规则顺序和 Agent 的一个弱点。

### 可选挑战

在观察状态中加入 `enemy_nearby`，再加入一个新动作或优先级规则。先写预期测试场景，然后修改 policy。

### Git Commit 建议

```text
week2 friday: build first rule based game agent
```

## 周六演示

从仓库根目录演示本周成果：

1. 展示四个数据脚本都读取同一个 `data/game_sessions.csv`。
2. 运行 NumPy 报告，并解释一个布尔掩码。
3. 运行 Pandas 分析，比较一个 `WIN` 指标和一个 `LOSS` 指标。
4. 打开三张保存的图表，谨慎解释一个关系。
5. 运行游戏 Agent 的必需场景和一个 Jerry 自己设计的边界情况。
6. 使用下面的命令展示第 2 周五个 commits：

   ```bash
   git log --oneline -5
   ```

不看准备好的讲稿，Jerry 应回答：

1. 什么情况下会选择 NumPy 数组，而不是 Pandas DataFrame？
2. 缺失的 accuracy 是如何处理的，为什么？
3. 哪项证据显示胜场和负场存在差异？
4. 一张图表提示了什么，又不能证明什么？
5. 游戏 Agent 中的 Observe、Decide 和 Act 分别在哪里？
6. 规则型 Agent 与经过训练的机器学习模型有什么不同？

## 家长复盘问题

1. Jerry 能否在没有帮助的情况下运行每个 Week 2 脚本？
2. Jerry 能否证明每个数据脚本都使用原始 CSV？
3. Jerry 能否解释一个 NumPy 操作和一个 Pandas 操作？
4. Jerry 能否解释缺失值处理决定？
5. Jerry 能否使用图表支持一条具体观察？
6. Jerry 能否修改游戏状态，并在运行前预测 Agent 的动作？
7. Jerry 能否解释本周遇到的一个 bug 或令人困惑的结果？
8. Git commits 是否展示了五天的持续进度？

如果缺少某一项，只修复该项、再次 commit，并重新演示相关部分，不需要从头开始整周学习。
