# 第 4 周 — 我们能相信 AI 的预测吗？

[English Version](../en/week04.md)

## 本周任务

第 3 周回答了：

> AI 能作出预测吗？

第 4 周要继续追问：

> 模型能够作出预测，但我们如何判断这些预测是否有用、是否值得信任？

继续在 Jerry 的实际 coding repository 中评估完整场次分类器：

[chessfan7777/AI-study](https://github.com/chessfan7777/AI-study)

使用现有数据集：

```text
game-statistics-analyzer/data/game-sessions.csv
```

本周评估流程：

```text
预测（Prediction）
↓
评估（Evaluation）
↓
错误分析（Error Analysis）
↓
模型比较（Model Comparison）
↓
泛化（Generalization）
↓
模型选择（Model Choice）
```

每个工作日投入 60–90 分钟，大约 30% 的时间学习，70% 的时间构建、调试、讲解并 commit。遵循：

**学习 → 构建 → 调试 → 讲解 → 改进 → 演示**

AI 可以按照问题、提示、伪代码、小型示例和部分代码的顺序帮助 Jerry，但不能提供可直接复制的完整脚本。如果 Jerry 无法解释重要代码和指标含义，任务就不算完成。

## 第 3 周起点

Jerry 现有的 Week 3 实现已经：

- 加载 `data/game-sessions.csv`；
- 使用 `duration_minutes`、`kills`、`deaths`、`accuracy` 和 `reaction_time_ms` 作为特征；
- 使用 `result` 中的 `WIN` 和 `LOSS` 作为标签；
- 删除一行存在缺失特征的数据；
- 得到 14 行可用数据；
- 使用 20% 分层划分和 `random_state=42`；
- 训练 Logistic Regression、Decision Tree 和 Random Forest；
- 报告基础准确率；
- 在 Week 3 命令行预测器中使用 Random Forest。

Week 4 开始时应先复用这套数据准备和划分，不要立刻改变。这样第一次比较更容易理解。Jerry 以后可以重构重复的数据准备逻辑，但不需要为了满足本计划而重排旧代码。

这个项目仍然是**完整场次分类器**，不是真正的赛前预测器。多数特征描述最终场次，因此模型评估无法消除这一建模限制。

## 小型数据集警告

删除不完整数据后，只剩大约 14 个场次。20% 测试集大约只有三场游戏。

在三场游戏的测试集上，100% 可能只代表 `3 / 3` 个预测正确，而 66.7% 可能代表 `2 / 3` 个预测正确。在三个示例上计算的指标在数学上有效，但在科学上证据很弱。只改变一个预测，就可能让准确率（accuracy）、精确率（precision）、召回率（recall）或 F1 大幅变化。应使用这样的表达：

> 模型在这个极小测试划分上取得 100%，但结果太不稳定，无法支持强结论。

不要使用：

> 我的模型准确率是 100%，所以它非常优秀。

周五得到以下结论完全可以接受：

> 没有明确赢家。我们需要更多样的游戏场次和更多未见测试示例。

## 编码结构

添加 Week 4，不要重排 Weeks 1–3：

```text
game-statistics-analyzer/
├── data/
│   └── game-sessions.csv
├── src/
│   └── week 4/
│       ├── day 1/
│       │   └── accuracy-baseline.py
│       ├── day 2/
│       │   └── precision-recall-f1.py
│       ├── day 3/
│       │   └── confusion-matrix.py
│       ├── day 4/
│       │   └── overfitting-demo.py
│       └── day 5/
│           └── game-ai-model-trust.py
├── notebooks/
│   └── week4-model-evaluation.ipynb
└── README.md
```

终端命令中的路径包含空格，因此需要加引号。文件名、标识符、注释和 Git commit message 都使用英文。不要按语言创建两套源代码。

## 第 4 周准备与 Repo Cleanup Mission（15–20 分钟）

在 `AI-study/game-statistics-analyzer/` 中激活现有环境：

```powershell
.\.venv\Scripts\Activate.ps1
```

确认所需库可用：

```bash
python -m pip install numpy pandas scikit-learn matplotlib jupyter
```

开始新代码前，从 `AI-study/` 检查 coding repository：

```bash
git status
git log --oneline -8
git ls-files ".venv/*"
git ls-files "*main.java"
```

Coding repository 当前包含已经被 Git 跟踪的虚拟环境文件，以及一个无关的 `src/week 3/day 1/main.java`。Jerry 应先确认它们与项目无关，移除遗留的 Hello World 或临时文件，确认 `.gitignore` 中有正确的 `.venv/` 规则，并在 Week 4 主体任务开始前把 coding README 补充到 Week 3。

理解两者的区别：

- **ignored file** 不会在以后被 Git 新增；
- **already tracked file** 即使后来匹配 ignore 规则，仍会继续被跟踪。

确认路径后，下面的命令只会从 Git index 移除虚拟环境，不会删除 Jerry 本地的环境：

```bash
git rm -r --cached game-statistics-analyzer/.venv
```

Commit 前检查 `git status`。执行任何删除或取消跟踪命令前，必须理解命令。

Week 4 commits 中不要混入无关 Java 文件、临时文件、编辑器输出和生成的环境。绝不能提交 secrets 或 credentials。

建议 cleanup commit：

```text
Clean repository before Week 4
```

## 周一：准确率可能会骗人

### 目标

理解准确率、多数类（majority class）、类别不平衡（class imbalance）和基线模型（baseline model）。

### 为什么重要

高分可能来自很弱的策略。如果某一类别占据绝大多数，模型即使完全忽略少数类，也可能报告很漂亮的准确率。

思考：

```text
100 场游戏
90 WIN
10 LOSS

Baseline:
总是预测 WIN

Accuracy:
90%
```

这个 baseline 的准确率是 90%，却完全无法识别任何 `LOSS`。准确率必须放在具体背景中解释。

### 概念

- 准确率（accuracy）
- 多数类与少数类（minority class）
- 类别不平衡
- 基线模型
- 使用 `most_frequent` strategy 的 `DummyClassifier`
- 总是预测多数类
- 完整数据集类别比例与测试集 baseline accuracy 的区别
- 在相同测试行上公平比较

Baseline 是一个简单的参考模型，用来判断更复杂模型是否真的做了有用的事情。

### 任务

创建：

```text
src/week 4/day 1/accuracy-baseline.py
```

运行：

```bash
python "src/week 4/day 1/accuracy-baseline.py"
```

完成两个实验：

1. 创建人工 `90 WIN / 10 LOSS` 示例，计算总是预测 `WIN` 的准确率。
2. 复用清理后的 Week 3 游戏数据和相同 train/test split。使用 `DummyClassifier(strategy="most_frequent")` 作为多数类 baseline，再在完全相同的测试行上把它与一个 Week 3 模型比较。

对于清理后的游戏数据，Jerry 应独立确认：

```text
可用场次：14
WIN：9
LOSS：5
多数类比例：9 / 14
```

完整数据集的多数类比例与在 `y_test` 上计算的 baseline accuracy 必须分开报告。

### 要求

- 从标签 collection 计算结果，不能直接输入答案百分比。
- 显示样本总数、`WIN` 数、`LOSS` 数、多数类和多数类百分比。
- 创建与对应真实标签长度相同的 always-majority 预测 collection。
- 计算人工数据的 baseline accuracy。
- 在 `y_test` 上计算真实游戏数据的 baseline accuracy。
- 在同一个 `y_test` 上计算一个 Week 3 模型的准确率。
- 百分比旁边打印“正确数/总数”。
- 说明训练模型在这次 split 上是否超过 baseline。
- 明确警告三场测试集无法支持强结论。

### 提示

- 先准备真实标签，再创建预测。
- 可以通过标签数量找到多数类。
- 重复同一个预测时，仍然需要为每个真实标签提供一个元素。
- `from sklearn.dummy import DummyClassifier` 提供标准多数类 baseline；Jerry 仍需自己完成 fit、predict 和解释。
- Baseline 和训练模型都使用同一个 `accuracy_score`。
- 如果 baseline 与模型得分相同，要诚实报告平局。

### Jerry 必须回答的问题

1. 什么是准确率？
2. 什么是类别不平衡？
3. 什么是多数类和少数类？
4. 什么是 baseline？
5. 为什么准确率 90% 的模型仍可能毫无用处？
6. 如果 95% 样本属于同一类别，总是预测多数类能得到多少准确率？
7. 为什么 baseline 和训练模型必须使用相同测试行？
8. Week 3 模型是否明确超过 baseline？这份证据有多强？

### 完成标准

- [ ] `accuracy-baseline.py` 成功运行。
- [ ] 人工 always-`WIN` baseline 通过计算得到 90%，而不是硬编码。
- [ ] 计算并显示真实清理后标签数量。
- [ ] Baseline 和训练模型测试分数使用完全相同的行。
- [ ] 百分比同时显示原始“正确数/总数”。
- [ ] Jerry 能解释为什么准确率必须结合类别平衡和 baseline 解读。
- [ ] 比较结果旁边显示小测试集警告。

### 可选挑战

创建并比较：

```text
Dataset A: 50 WIN / 50 LOSS
Dataset B: 95 WIN / 5 LOSS
```

在两者上运行同一个 always-`WIN` baseline，再解释为什么策略没有变好，但准确率却发生变化。

### Git Commit 建议

```text
Add class imbalance baseline experiment
```

## 周二：Precision、Recall 与 F1

### 目标

通过询问有关 `WIN` 预测的不同问题，理解 precision、recall 和 F1。

### 为什么重要

不同错误的含义并不相同。Accuracy 把所有结果压缩成一个数字；precision 和 recall 可以显示不同的错误取舍。

先从直觉开始：

- **精确率（precision）：** AI 预测为 `WIN` 的场次中，实际有多少是 `WIN`？
- **召回率（recall）：** 实际为 `WIN` 的所有场次中，AI 成功找出了多少？
- **F1 分数（F1 score）：** 如何用一个分数平衡 precision 与 recall？

然后联系公式：

```text
Precision = TP / (TP + FP)
Recall = TP / (TP + FN)
F1 = 2 × (Precision × Recall) / (Precision + Recall)
```

Jerry 不需要背诵 F1 公式，但必须理解每个指标回答的问题。

### 概念

- 正类（positive class）
- true positive、false positive 和 false negative
- precision
- recall
- F1 score
- 不同错误类型之间的取舍
- 明确设置 `pos_label`
- 极小测试集中的 zero-division 行为

本周游戏评估把 `WIN` 定义为正类，并始终明确显示这个选择。

### 任务

创建：

```text
src/week 4/day 2/precision-recall-f1.py
```

运行：

```bash
python "src/week 4/day 2/precision-recall-f1.py"
```

先推理以下场景：

```text
AI 把 10 个场次预测为 WIN。
其中 8 个实际是 WIN。
2 个实际是 LOSS。

总共有 12 个真实 WIN 场次。
AI 找到了其中 8 个。
```

然后复用一个 Week 3 模型并计算：

```python
accuracy_score(...)
precision_score(...)
recall_score(...)
f1_score(...)
```

明确配置字符串标签，让 `WIN` 被当作正类。不要静默依赖为数字标签设计的默认 `pos_label`。

显示：

| Metric | Result | Plain-English meaning |
|---|---:|---|
| Accuracy | ? | ? |
| Precision | ? | ? |
| Recall | ? | ? |
| F1 | ? | ? |

每个结果和解释都由 Jerry 填写。

### 要求

- 使用一个 Week 3 模型和同一份准备后的训练/测试数据。
- 明确把正类设置为 `WIN`。
- 使用 scikit-learn 计算四个指标。
- 统一标注分数是 0 到 1 的数值还是百分比。
- 打印底层测试样本数量。
- 用简单英文解释每个指标。
- 如果没有正类预测导致 warning，要调查并记录所选 zero-division 行为。
- 不要声称一个极小样本分数能够可靠估计未来表现。
- 使用一个非游戏例子解释为什么 recall 或 precision 可能更重要。

### 提示

- 思考 precision 时问“预测为正的样本”。
- 思考 recall 时问“实际为正的样本”。
- 计算结果前先确定 `WIN` 是正类。
- 只有三行测试数据时，某些指标的分母可能很小甚至为零。
- 医疗筛查、欺诈检测、垃圾邮件过滤和安全警报对错误的代价不同。

### Jerry 必须回答的问题

1. Precision 回答什么问题？
2. Recall 回答什么问题？
3. F1 想平衡什么？
4. 为什么两个模型可以有相同 accuracy，却有不同 precision 和 recall？
5. 本实验的正类是什么？在哪里配置？
6. 为什么医疗筛查可能优先考虑 recall？
7. 为什么噪声很多的警报系统可能很重视 precision？
8. 为什么这些指标在 Jerry 的小测试集上不稳定？

### 完成标准

- [ ] `precision-recall-f1.py` 成功运行。
- [ ] 明确配置 `WIN` 为正类。
- [ ] 计算 accuracy、precision、recall 和 F1。
- [ ] 每个显示指标都有 Jerry 用简单英文写的解释。
- [ ] 不从公式出发，也能推理游戏场景。
- [ ] 理解并清楚处理任何 zero-division warning。
- [ ] Jerry 不看公式也能解释 precision 与 recall 的区别。

### 可选挑战

创建两组 accuracy 相同、precision 和 recall 不同的虚构预测。为一个定义清楚的真实问题选择其中一组，并解释原因。

### Git Commit 建议

```text
Add precision recall and F1 evaluation
```

## 周三：混淆矩阵——AI 在哪里出错？

### 目标

使用混淆矩阵（confusion matrix）解释 true positive、false positive、false negative 和 true negative。

### 为什么重要

一个指标会汇总错误，而 confusion matrix 显示每个预测去了哪里，更容易发现 accuracy 隐藏的具体错误。

把 `WIN` 作为正类：

```text
                         Predicted
                      WIN        LOSS

Actual WIN             TP          FN

Actual LOSS            FP          TN
```

在游戏语境中：

- **TP：** 预测为 `WIN`，实际结果是 `WIN`。
- **FP：** 预测为 `WIN`，实际结果是 `LOSS`。
- **FN：** 预测为 `LOSS`，实际结果是 `WIN`。
- **TN：** 预测为 `LOSS`，实际结果是 `LOSS`。

### 概念

- confusion matrix
- 真实标签位于行
- 预测标签位于列
- TP、FP、FN 和 TN
- 明确的标签顺序
- 错误分析
- 取决于使用场景的错误代价
- 生成图形与解释结果的区别

### 任务

创建：

```text
src/week 4/day 3/confusion-matrix.py
```

运行：

```bash
python "src/week 4/day 3/confusion-matrix.py"
```

使用一个 Week 3 模型生成预测，并：

1. 计算 confusion matrix；
2. 明确规定标签顺序先 `WIN`，后 `LOSS`；
3. 显示矩阵，并正确标注 actual 与 predicted axes；
4. 从矩阵中识别 TP、FN、FP 和 TN；
5. 为每个 cell 写一句解释。

相关工具：

```python
confusion_matrix(...)
ConfusionMatrixDisplay(...)
```

真正目标是解释矩阵，不只是生成图。

如果真实的三行测试矩阵几乎为空或过于简单，要保留它，再单独添加一个包含四种结果的 synthetic labels/predictions 示例。明确标注该示例是人工学习数据。

### 要求

- 从 Jerry 的真实游戏测试预测生成矩阵。
- 保持 `WIN` 为正类。
- 指定并显示标签顺序，让行列含义清楚。
- 坐标轴标记为 actual 和 predicted。
- 找出四个结果名称与数量。
- 用游戏语言解释每个观察到的错误。
- 如果某些 cell 因本次 split 为空，要明确说明。
- 只在需要时添加清楚标注的 synthetic teaching example，绝不能替代真实结果。
- 解释哪种错误更严重取决于应用场景。

### 提示

- 如果 Jerry 不提供顺序，scikit-learn 可能按与教学图不同的方式排序字符串标签。
- 命名各 cell 前先验证矩阵方向。
- 即使一种结果数量为零，矩阵仍然可以是 2 × 2。
- 检查所有 cell 的总和是否等于测试样本数。
- 先问“真实结果是什么”，再问“模型预测了什么”。

### Jerry 必须回答的问题

1. 什么是 true positive？
2. 什么是 false positive？
3. 什么是 false negative？
4. 什么是 true negative？
5. 哪个 axis 包含真实标签？
6. 为什么 confusion matrix 比 accuracy 显示更多信息？
7. FP 和 FN 哪个更严重？
8. 医疗筛查中哪种错误可能更重要？
9. 垃圾邮件过滤中哪种错误可能更重要？
10. 为什么真实游戏 confusion matrix 的证据很弱？

### 完成标准

- [ ] `confusion-matrix.py` 成功运行。
- [ ] 生成并清楚标注真实测试矩阵。
- [ ] 四个 cell 被正确对应为 TP、FN、FP 和 TN。
- [ ] 所有 cell 数量总和等于测试集大小。
- [ ] Jerry 用游戏语言解释每个非零 cell。
- [ ] 任何 synthetic example 都与真实结果清楚分开。
- [ ] Jerry 不依赖 accuracy，也能口头解释 confusion matrix。

### 可选挑战

为另一个 Week 3 模型创建第二个真实 confusion matrix。提问：

> Accuracy 相同，但错误也相同吗？

比较错误类型，不要自动宣布赢家。

### Git Commit 建议

```text
Add confusion matrix analysis
```

## 周四：过拟合——当 AI 只是记住，而没有学会

### 目标

理解训练分数（training score）、测试分数（test score）、泛化（generalization）、过拟合（overfitting）和欠拟合（underfitting），重点关注过拟合。

### 为什么重要

模型可以在学习过的示例上看起来完美，却在新示例上失败。

```text
训练集
↓
模型记住细节
↓
训练准确率变得很高

未见测试数据
↓
模型表现困难
↓
测试准确率更低
```

记住练习试卷答案，不等于真正理解课程内容。

### 概念

- training accuracy
- test accuracy
- generalization
- 模型复杂度（model complexity）
- overfitting
- underfitting
- good fit
- Decision Tree 约束
- 诚实的 synthetic demonstration

直观理解：

```text
Underfitting:
模型太简单，无法学习有用模式

Good fit:
模型学到可以迁移的有用模式

Overfitting:
模型过度贴合训练数据细节
```

### 任务

创建：

```text
src/week 4/day 4/overfitting-demo.py
```

运行：

```bash
python "src/week 4/day 4/overfitting-demo.py"
```

使用 Decision Tree，至少比较：

```text
Flexible Tree
vs
Constrained Tree
```

Jerry 可以研究的参数：

```text
max_depth
min_samples_split
min_samples_leaf
```

为每棵树分别计算 training accuracy 与 test accuracy：

| Model | Training Accuracy | Test Accuracy | Jerry's Interpretation |
|---|---:|---:|---|
| Flexible Tree | ? | ? | ? |
| Constrained Tree | ? | ? | ? |

目标不是超参数优化，而是观察模型复杂度如何可能提高训练表现，却降低未见数据上的表现。

极小的游戏数据集可能无法呈现清晰模式。如果没有清晰结果，保留并报告真实实验，再专门使用一个小型带标签 synthetic dataset 显示 overfitting。明确说明哪些结果来自 synthetic data，绝不能编造漂亮指标。

### 要求

- 第一个实验复用 Week 3 游戏数据和 split。
- 至少训练一棵 flexible tree 和一棵 constrained tree。
- 为两者计算 training accuracy 和 test accuracy。
- 把四个分数放入比较表。
- 描述每个 train/test gap 的大小和方向。
- 说明真实数据是显示清楚的 overfitting，还是结果不确定。
- 如果需要，加入清楚标注的 synthetic demonstration。
- 直观解释 underfitting 和 good fit。
- 不执行 GridSearchCV 或大范围超参数优化。

### 提示

- 可以分别在训练数据和测试数据上计算模型 `score`。
- 先比较同一个模型在两份数据上的表现，再比较不同模型。
- 不受约束的 tree 可以不断分割，直到贴合训练细节。
- 减小 `max_depth` 会限制树能够学习的层数。
- 极小测试集可能隐藏或夸大 train/test gap。

### Jerry 必须回答的问题

1. 什么是 overfitting？
2. 什么是 underfitting？
3. 什么是 generalization？
4. 为什么 training accuracy 可能高于 test accuracy？
5. 100% training accuracy 一定好吗？
6. 限制 tree depth 如何减少 overfitting？
7. 真实游戏数据呈现了清晰模式，还是结果不确定？
8. 为什么必须诚实标注 synthetic results？

### 完成标准

- [ ] `overfitting-demo.py` 成功运行。
- [ ] 比较 flexible tree 和 constrained tree。
- [ ] 每个模型都报告 training score 和 test score。
- [ ] Jerry 写下一条对 train/test gap 的解释。
- [ ] 真实结果与 synthetic results 清楚区分。
- [ ] 没有为了让故事更漂亮而编造指标。
- [ ] Jerry 能解释有用模型必须在未见数据上工作。

### 可选挑战

绘制：

```text
tree depth
vs
training accuracy
vs
test accuracy
```

如果游戏数据产生误导性图表，使用清楚标注的 synthetic data，并解释原因。

### Git Commit 建议

```text
Demonstrate model overfitting
```

## 周五：Game AI Friday #3——我应该相信哪个 AI？

### 目标

构建 **Jerry Game AI v2 — Model Trust Report**，使用多个指标和具体背景比较 Logistic Regression、Decision Tree、Random Forest 与 KNN。

### 为什么重要

Game AI 已经发展到：

```text
Game AI Friday #1
Rule-Based Agent
Jerry 编写规则

↓

Game AI Friday #2
Machine Learning Predictor
模型从示例中学习

↓

Game AI Friday #3
Model Evaluation
Jerry 应该相信哪个模型？
```

“Accuracy 最高就获胜”是很弱的规则。负责任的选择需要考虑指标含义、错误类型、overfitting 风险、模型简单性和证据质量。

### 概念

- 多指标模型比较
- K-Nearest Neighbors（KNN）
- `k` 表示邻居数量
- nearby examples 与 neighbor voting
- 使用证据选择模型
- accuracy、precision、recall 与 F1 取舍
- 模型简单性和 overfitting 风险
- “没有明确赢家”也是有效结论
- 面向 distance-based model 的 feature scaling
- `StandardScaler` 与 `Pipeline` 或 `make_pipeline`

KNN 直觉：

```text
新样本
↓
查看附近示例
↓
邻居投票
↓
作出预测
```

Jerry 的特征使用差异很大的数值范围：kills 可能是几十，accuracy 可能是百分比，而 `reaction_time_ms` 可能是几百。KNN 使用距离，因此如果不缩放输入，数值较大的特征可能主导结果。

使用简单的 scikit-learn pipeline：

```text
StandardScaler
↓
KNeighborsClassifier
```

优先使用 `Pipeline` 或 `make_pipeline`，让 scaling 只从训练数据中学习，再一致地应用于测试数据。本周不深入学习距离公式、维度灾难（curse of dimensionality）、KD-trees 或高级 scaling 理论。

### 任务

创建：

```text
src/week 4/day 5/game-ai-model-trust.py
```

运行：

```bash
python "src/week 4/day 5/game-ai-model-trust.py"
```

使用：

```python
LogisticRegression
DecisionTreeClassifier
RandomForestClassifier
KNeighborsClassifier
StandardScaler
Pipeline or make_pipeline
```

使用相同准备数据和 split 训练四个模型。把 KNN 放在 `StandardScaler` pipeline 中；模型比较不能使用未经 scaling 的原始 KNN。把 `WIN` 作为正类，并先完成下面的表格：

| Model | Accuracy | Precision | Recall | F1 | Notes |
|---|---:|---:|---:|---:|---|
| Logistic Regression | ? | ? | ? | ? | Jerry 填写 |
| Decision Tree | ? | ? | ? | ? | Jerry 填写 |
| Random Forest | ? | ? | ? | ? | Jerry 填写 |
| Scaled KNN | ? | ? | ? | ? | Jerry 填写 |

然后生成类似以下结构的 trust report：

```text
=== Jerry Game AI v2 ===

Model Comparison

Logistic Regression
Accuracy: ...
Precision: ...
Recall: ...
F1: ...

Decision Tree
...

Random Forest
...

KNN
...

Selected Model:
...

Reason:
...

Warning:
Only about 14 usable sessions exist.
The test set is extremely small.
Metric differences are unstable.
```

Jerry 可以选择一个模型，也可以报告 `No clear winner`。理由必须在适用时引用指标、观察到的错误、overfitting 风险、数据集大小和模型简单性。

可以选择扩展 Week 3 input mode，让用户输入一个完整场次，再获得模型预测和所选模型的评估摘要。必须说明总体测试指标不能保证单次预测的 confidence。

### 要求

- 复用相同的五个特征、缺失值规则和 train/test split。
- 训练四个必需分类器。
- 只通过 neighbor voting 和 `k` 介绍 KNN。
- 为 KNN 使用 `StandardScaler` 和 `Pipeline` 或 `make_pipeline`。
- 只能通过 training pipeline 拟合预处理；不能在 split 前缩放完整数据集。
- 选择一个对训练集大小有效的 `k`。
- 为每个模型计算 accuracy、precision、recall 和 F1。
- 明确配置 `WIN` 为正类。
- 记录“正确数/测试总数”和至少一个错误观察。
- 在有助于识别 overfitting 时加入 training/test 证据。
- 不要自动选择 accuracy 最高的模型。
- 允许并解释 `No clear winner`。
- 在报告中打印小数据警告。
- 为所选模型或整体实验写出 strength、weakness、risk 和 next improvement。

### 提示

- 把模型名和模型对象放入 collection，再应用相同的 fit/predict/evaluate 步骤。
- 使用相同测试行，让入门比较公平。
- 在三行测试数据上，分数只能以很大的幅度变化。
- 把 KNN 构建为一个 pipeline object，让 `fit()` 从 `X_train` 学习 scaling，并让 `predict()` 对 `X_test` 应用同样的变换。
- 当算法需要特定预处理时，model-specific preprocessing 属于公平比较的一部分；公平性仍要求使用相同的 train/test rows 和评估规则。
- 科学上的诚实可能比强行命名一个赢家更重要。

### Jerry 必须回答的问题

1. 哪个模型的 accuracy 最高？
2. 哪个模型的 precision 最高？
3. 哪个模型的 recall 最高？
4. 哪个模型的 F1 最高？
5. 四个答案是否都是同一个模型？
6. 如果不是，应选择哪个模型，还是没有明确赢家？
7. 为什么“accuracy 最高就获胜”是很弱的规则？
8. 现在能否有信心地相信任何模型？
9. KNN 在高层面上如何工作？
10. 不同 feature scale 为什么会扭曲 KNN？Scaling pipeline 如何解决这个问题？
11. 增加哪些数据会让评估更值得信任？
12. 你最信任哪个模型？什么证据支持这个选择？

### 完成标准

- [ ] `game-ai-model-trust.py` 成功运行。
- [ ] 四个模型使用同一份 data split。
- [ ] KNN 使用在 training data 上拟合的 `StandardScaler` pipeline。
- [ ] 报告为每个模型显示 accuracy、precision、recall 和 F1。
- [ ] 明确把 `WIN` 配置为正类。
- [ ] 所选模型或 `No clear winner` 有证据支持。
- [ ] 报告包含 strength、weakness、risk 和 next improvement。
- [ ] 指标旁边清楚显示小数据警告。
- [ ] Jerry 能解释模型评估为什么需要多个指标和具体背景。

### 可选挑战

添加简短 trust report：

```text
Model: Random Forest

Strength:
...

Weakness:
...

Risk:
Small dataset

Next improvement:
Collect more game sessions
```

模型和理由由 Jerry 填写，不能自动复制示例结论。

### Git Commit 建议

```text
Add Game AI model trust report
```

## Week 4 整合 Notebook

创建：

```text
notebooks/week4-model-evaluation.ipynb
```

包括以下 20 个部分：

1. 加载数据集。
2. 准备 `X` 和 `y`。
3. 创建 train/test split。
4. 检查 class distribution。
5. 构建 baseline classifier。
6. 计算 accuracy。
7. 计算 precision。
8. 计算 recall。
9. 计算 F1。
10. 生成并解释 confusion matrix。
11. 比较 training 和 test scores。
12. 运行 overfitting 实验。
13. 评估 Logistic Regression。
14. 评估 Decision Tree。
15. 评估 Random Forest。
16. 评估 scaled KNN。
17. 构建 model comparison table。
18. 写模型选择理由。
19. 解释数据集限制。
20. 用 Jerry 自己的语言写“What I Learned”。

使用必须由 Jerry 完成的 Markdown prompts：

- 哪个指标改变了你对模型的看法？
- Confusion matrix 揭示了哪些具体错误？
- 哪些证据提示 overfitting、underfitting，或者结果无法判断？
- 证据是否足以让你有信心选择模型？
- 哪些新数据可以减少不确定性？
- 为什么系统仍然是完整场次分类？

不要让 AI 编写 Jerry 的最终结论。

适合 Notebook 的 commit：

```text
Complete Week 4 evaluation notebook
```

## README 要求

Coding repository 的 README 目前仍主要停留在 Week 1。更新它，让它总结 Weeks 1–4：

```text
Week 1 — Python + Game Statistics
Week 2 — NumPy / Pandas / Visualization
Week 3 — First Machine Learning Models
Week 4 — Model Evaluation
```

Week 4 总结必须包括：

```text
Week 4 — Model Evaluation

- Accuracy and baseline models
- Class imbalance
- Precision
- Recall
- F1 score
- Confusion Matrix
- Overfitting
- Model comparison
- Game AI Model Trust Report
```

Jerry 应用自己的语言写简短总结，并说明数据集最大的限制。

建议 commit：

```text
Update README through Week 4
```

## Git Hygiene 检查点

周六演示前运行：

```bash
git status
git diff --check
git log --oneline -10
git ls-files ".venv/*"
git ls-files "*main.java"
```

确认：

- Week 4 commits 只包含相关 source、Notebook、README 和有意保留的 output files。
- 没有被跟踪的 virtual environment。
- 没有 `__pycache__`、IDE output、无关 Java、随机 Hello World 或临时文件。
- 没有 credentials 或 secrets。
- Commit messages 描述具体工作。

避免 `done`、`week4`、`final`、`stuff` 或 `update` 这类模糊 commits。

## 为什么这对以后很重要

```text
现在
training score
test score
overfitting
generalization

以后在 PyTorch 和 JerryMind 中
training loss
validation loss
model size
generalization
overfitting
```

模型以后会变大，测量方式也会变化，但核心问题不变：模型是否学到了能够应用于训练示例之外的内容？

Week 4 不实现 PyTorch、Transformers、MiniMind 或 JerryMind。这一节用于衔接未来，不是本周作业。

## Game AI 长期路线

```text
Game AI Friday #1
Rule-Based Agent

↓

Game AI Friday #2
Machine Learning Game Result Predictor

↓

Game AI Friday #3
Which AI Should I Trust?

↓

未来
Game State Recognition

↓

Computer Vision

↓

Action Selection

↓

Reinforcement Learning

↓

Roblox Studio AI Agent
```

未来阶段用于保持学习动力，不是 Week 4 实现任务。

## 周六演示

在 `AI-study/game-statistics-analyzer/` 中，Jerry 应演示：

1. class imbalance 示例；
2. majority-class baseline model；
3. 以 `WIN` 为正类的 accuracy、precision、recall 和 F1；
4. 正确标注的 confusion matrix 和每种错误；
5. Flexible 与 constrained Decision Tree 的 training/test 结果；
6. 包含 scaled KNN 的四模型比较；
7. Jerry Game AI v2 Model Trust Report；
8. 整合 Notebook 和 README 更新；
9. 清晰、有意义的 Week 4 Git 历史。

Jerry 不看准备好的讲稿，回答：

1. 为什么高 accuracy 可能产生误导？
2. Precision 与 recall 有什么区别？
3. Confusion matrix 揭示了什么？
4. 哪些证据可能表示 overfitting？
5. 你会信任哪个模型，或者为什么没有明确赢家？
6. 什么会让实验更值得信任？
7. 以后训练神经网络时，这些概念为什么仍然重要？

## 家长复盘问题

1. **为什么准确率 90% 的模型仍可能很差？**

   预期概念：类别不平衡可能让 always-majority baseline 在无法有效预测少数类时仍得到高分。

2. **什么是 baseline model？**

   预期概念：它是简单的参考模型，用来判断更复杂的模型是否真正增加了有用价值。

3. **不使用公式解释 precision。**

   预期概念：在模型预测为 `WIN` 的游戏中，有多少实际也是 `WIN`？

4. **不使用公式解释 recall。**

   预期概念：在所有实际为 `WIN` 的游戏中，模型找到了多少？

5. **F1 说明什么？**

   预期概念：当 precision 和 recall 都重要时，F1 把两者结合起来。

6. **解释 TP、FP、FN 和 TN。**

   Jerry 应以 `WIN` 为正类，在游戏语境中解释每一个。

7. **什么是 overfitting？**

   预期概念：模型过度贴合训练数据，在未见数据上的表现更差。

8. **为什么 training accuracy 可能高于 test accuracy？**

9. **所有模型都得到了很高的分数，这能证明它们都很好吗？**

   预期答案：不能，因为数据集和测试集都极小。

10. **什么会让这个实验更值得信任？**

   预期想法：更多数据、更多样的数据、更多未见测试示例、重复评估和更好的特征。

11. **当前 Game AI 最大的弱点是什么？**

    预期概念：训练数据太少，而且许多输入描述完整场次，而不是真实的实时游戏状态。

12. **为什么 KNN 在 feature scaling 方面需要特别处理？**

    预期概念：KNN 使用距离，所以如果不缩放输入，数值范围较大的特征可能主导结果。

## 每周评分

| 项目 | 分值 |
|---|---:|
| 理解 Accuracy + Baseline | 10 |
| Class Imbalance | 10 |
| Precision / Recall | 15 |
| 理解 F1 | 10 |
| Confusion Matrix | 10 |
| Overfitting / Generalization | 15 |
| Model Comparison | 10 |
| Game AI Friday | 10 |
| 解释代码 | 5 |
| Git + 文档 | 5 |
| **总分** | **100** |

评分不能代替真正理解。如果缺少某一项，只修复该项、再做一次有意义的 commit，并重新演示相关部分，不需要重启整周学习。

## 第 4 周成功标准

如果 Jerry 能诚实解释下面两句话，第 4 周就成功了：

> 模型的 accuracy 本身不能说明它是否优秀。

以及：

> 在决定是否信任模型前，我需要检查不同错误类型、多个指标、测试表现、数据集大小和 overfitting。

Jerry 应理解：

```text
模型
↓
预测
↓
评估
↓
错误分析
↓
泛化
↓
信任
```

## 衔接第 5 周

```text
第 3 周：
AI 能预测吗？

第 4 周：
我们能相信这个预测吗？

第 5 周：
我们能否根据已经学到的内容构建更好的 AI 系统？
```

第 5 周会把这些经验转化为更完整的应用型 ML/Game AI 项目。本周不要设计或实现 Week 5。

## 控制学习范围

使用 Python、Pandas、NumPy、scikit-learn、必要时使用 Matplotlib，以及 Jupyter Notebook。

必需模型 classes：

```text
LogisticRegression
DecisionTreeClassifier
RandomForestClassifier
KNeighborsClassifier
```

必需评估工具：

```text
accuracy_score
precision_score
recall_score
f1_score
confusion_matrix
ConfusionMatrixDisplay
```

本周不引入 ROC-AUC、PR-AUC、calibration、深入 cross-validation、超参数优化、GridSearchCV、Bayesian optimization、PyTorch、神经网络、Transformers、MiniMind/JerryMind 实现或 reinforcement-learning implementation。

可以把 cross-validation 简短地提为未来重复评估的方法，但它不是 Week 4 要求。
