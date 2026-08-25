# 第 3 周 — 机器学习：AI 能预测一场游戏是赢还是输吗？

[English Version](../en/week03.md)

## 本周任务

从数据分析进入监督学习（supervised learning）：训练模型，根据一场已经结束的游戏统计数据，把该场次分类为 `WIN` 或 `LOSS`。

本周工作流程：

```text
数据集（Dataset）
↓
特征 X（Features X）
↓
标签 y（Label y）
↓
训练集/测试集划分（Train/Test Split）
↓
选择算法（Choose Algorithm）
↓
训练模型（Train Model）
↓
预测（Predict）
↓
比较结果（Compare Results）
```

继续在 Jerry 的实际编码仓库中完成任务：

[chessfan7777/AI-study](https://github.com/chessfan7777/AI-study)

使用现有项目和数据集：

```text
game-statistics-analyzer/data/game-sessions.csv
```

编码仓库中的真实文件名是带连字符的 `game-sessions.csv`。Jerry 的脚本应使用这个文件名，不要复制另一份数据集。

每个工作日投入 60–90 分钟，大约 30% 的时间学习，70% 的时间构建、调试、讲解并 commit。遵循：

**学习 → 构建 → 调试 → 讲解 → 改进 → 演示**

AI 可以提问、解释概念、提供提示、协助调试 Jerry 已经尝试过的代码，或者进行代码审查，但不能提供可直接复制的完整解答。Jerry 无法解释的重要代码不算完成。

## 准确描述项目

目标标签（target label）是：

```text
result → WIN / LOSS
```

候选特征（feature）是：

```text
duration_minutes
kills
deaths
accuracy
reaction_time_ms
```

这些数值大部分描述的是一场已经结束的游戏。因此，本项目的准确说法是：

> 根据完整场次统计数据，把已经结束的游戏分类为 WIN 或 LOSS。

它还不能真正预测 Jerry 在游戏开始前是否会赢。真正的赛前或实时预测器需要游戏开始前或进行中能够获得的信息，例如：

```text
kills_after_2_minutes
deaths_after_2_minutes
current_health
current_score
early_reaction_time
position
opponent_state
```

Notebook、README、周五程序和周六讲解都必须清楚保留这一区别。

## 小型数据集 Reality Check

当前数据集只有 15 个场次。它适合学习机器学习工作流程，但不足以证明模型可以可靠地用于真实产品。

如果测试集占 20%，测试集可能只有大约三场游戏。即使准确率是 100%，也可能只表示三个预测全部正确。换一种划分方式，结果可能会有很大变化。

Jerry 不能声称：

> 我的 AI 是完美的，或者已经可以在真实环境中使用。

他应该能够说明：

> 这是一个教学原型。因为数据集和测试集都非常小，测得的准确率并不稳定。

## 编码结构

适配 Jerry 已有目录，不要为了 Week 3 重排旧代码：

```text
game-statistics-analyzer/
├── data/
│   └── game-sessions.csv
├── src/
│   └── week 3/
│       ├── day 1/
│       │   └── prepare_ml_data.py
│       ├── day 2/
│       │   └── train_test_split.py
│       ├── day 3/
│       │   └── logistic_regression.py
│       ├── day 4/
│       │   └── model_comparison.py
│       └── day 5/
│           └── game_result_predictor.py
├── notebooks/
│   └── week3-first-machine-learning.ipynb
└── requirements.txt
```

目录名称中含有空格，所以在终端命令中要给脚本路径加引号。

可执行源代码只保留一份并使用英文。不要按英文和中文分别创建源文件。

## 第 3 周准备

在 `AI-study/game-statistics-analyzer/` 目录中激活现有虚拟环境。

PowerShell：

```powershell
.\.venv\Scripts\Activate.ps1
```

安装所需库：

```bash
python -m pip install numpy pandas scikit-learn matplotlib jupyter
```

创建或更新 `requirements.txt`，记录实际使用的直接依赖。不要提交新的虚拟环境文件。

本周只使用基础准确率（accuracy）进行入门比较。精确率（precision）、召回率（recall）、F1、混淆矩阵（confusion matrix）、过拟合（overfitting）和欠拟合（underfitting）留到第 4 周。

## 周一：从规则走向学习

### 目标

通过准备 Jerry 的游戏场次数据，理解特征、标签、模型、训练、预测和监督学习。

### 为什么重要

第 2 周的游戏 Agent 按照 Jerry 编写的规则行动。监督学习模型会研究带标签的示例，并学习从输入映射到正确答案的模式。

```text
第 2 周
状态 → Jerry 编写的规则 → 动作

第 3 周
带标签的示例 → 学习算法 → 训练后的模型 → 预测
```

下面的规则不是机器学习：

```python
if kills > 15:
    result = "WIN"
```

因为决策是 Jerry 直接写出来的。在机器学习中，Jerry 选择特征和算法，但模型参数由示例数据训练得到。

### 概念

- 监督学习（supervised learning）
- 特征和特征矩阵 `X`
- 标签/目标和目标向量 `y`
- 训练示例
- 模型和预测
- 数值输入要求
- 缺失值
- 入门层面的数据泄漏（data leakage）

### 任务

创建：

```text
src/week 3/day 1/prepare_ml_data.py
```

在 `AI-study/game-statistics-analyzer/` 中运行：

```bash
python "src/week 3/day 1/prepare_ml_data.py"
```

使用 Pandas 加载 `data/game-sessions.csv`。定义：

```text
X:
duration_minutes
kills
deaths
accuracy
reaction_time_ms

y:
result
```

检查：

```python
X.head()
y.head()
X.shape
y.shape
```

找到缺失的 `accuracy`。为机器学习选择并记录一种处理策略：

- 删除不完整的行；
- 均值填充（mean imputation）；或者
- 中位数填充（median imputation）。

不要静默填充，也不要在没有合理解释时用零代替缺失值。如果最终流程使用填充，不要从测试数据学习填充值；可以向 AI 请求“只用训练数据拟合预处理”的提示。

### 要求

- 使用 Jerry 现有的游戏场次 CSV，不使用学生成绩数据集。
- 把特征名保存在一个清楚的 collection 中，例如 `feature_names`。
- 从 `X` 中排除 `game_id`、`date` 和 `result`。
- 把 `y` 设置为 `result` 列。
- 打印特征名、`X.head()`、`y.head()` 和两者 shape。
- 统计标签数量，确认同时包含 `WIN` 和 `LOSS`。
- 在应用处理策略前先发现缺失 accuracy。
- 在代码注释或学习笔记中解释缺失值选择。
- 保持原始 CSV 不变。

### 提示

- DataFrame 可以通过列名 list 一次选择多列。
- `isna().sum()` 可以显示每一列的缺失值。
- `game_id` 用于识别一行，但不描述游戏表现。
- 未处理的 `date` 不是合适的第一个特征，除非先把它转换成有意义的信息。
- 对第一个简单流程来说，删除一行不完整数据是可接受的，但 Jerry 必须解释代价。

### Jerry 必须回答的问题

1. 什么是特征？
2. 什么是标签？
3. 本项目的标签是什么？
4. 哪些字段被用作特征？
5. 为什么 `game_id` 通常不是有用的预测特征？
6. 为什么原始 `date` 不适合第一个模型？
7. 手写的 `if kills > 15` 是机器学习吗？为什么不是？
8. 你如何处理缺失 accuracy？丢失了哪些信息，或者作出了什么假设？

### 完成标准

- [ ] `prepare_ml_data.py` 可以从项目目录运行。
- [ ] `X` 只包含五个选定的数值特征。
- [ ] `y` 包含 `WIN` 和 `LOSS`。
- [ ] 打印的 shape 与所选缺失值策略一致。
- [ ] 没有意外把缺失特征传到后续步骤。
- [ ] Jerry 能解释：`X = 输入`、`y = 正确标签`、`model = 学到的关系`。
- [ ] Jerry 能准确地把项目描述为完整场次分类。

### 可选挑战

创建 `feature_names`，通过循环或格式化打印带编号、易阅读的特征清单，不要逐条手写 print。

### Git Commit 建议

```text
Prepare game data for machine learning
```

## 周二：训练数据与测试数据

### 目标

使用 `train_test_split()`，并理解为什么训练和测试必须使用不同示例。

### 为什么重要

训练题是学生学习过的示例，测试题应该是新题。如果学生在学习时已经拿到了完全相同的测试答案，这场测试就很难说明他是否真正学会。

机器学习遵循同样原则：

```text
训练数据 → 模型学习
测试数据 → 在未见示例上检查模型
```

### 概念

- 训练集（training set）和测试集（test set）
- `X_train`、`X_test`、`y_train` 和 `y_test`
- `train_test_split`
- `test_size`
- `random_state`
- 可复现性（reproducibility）
- 可选的分层划分（stratification）
- 小样本造成的结果不稳定

### 任务

创建：

```text
src/week 3/day 2/train_test_split.py
```

使用：

```python
from sklearn.model_selection import train_test_split
```

复用周一的特征和标签准备，把 `X` 和 `y` 划分成四个对象。检查：

```python
X_train.shape
X_test.shape
y_train.shape
y_test.shape
```

打印完整数据、训练集和测试集中的标签数量。选择一个明确的 `test_size` 和 `random_state`，并记录可复现性为什么有用。

运行：

```bash
python "src/week 3/day 2/train_test_split.py"
```

### 要求

- 同时划分特征和标签，保持行对应关系。
- 不让测试示例进入模型训练。
- 第一次实验使用接近 20% 的测试集。
- 设置并打印所选 `random_state`。
- 打印四个 shape。
- 打印足够的 index 或标识符，证明训练行和测试行不同。
- 检查类别数量，确保训练集同时包含两种标签。
- 说明测试集大约包含多少场游戏。
- 不要为了以后获得更高分数而反复更换 seed。

### 提示

- 一次 `train_test_split` 调用可以返回全部四个对象。
- 返回对象的顺序很重要，要对照文档或函数签名。
- `random_state` 让随机划分可以重复。
- `stratify=y` 可以帮助保持标签比例，但三行测试集仍然很不稳定。
- 调试时暂时打印 index，检查对齐和分离。

### Jerry 必须回答的问题

1. 为什么在完全相同的行上训练和测试会产生误导？
2. 模型如何使用训练数据？
3. 测试数据用于什么？
4. `test_size` 控制什么？
5. `random_state` 的实际用途是什么？
6. 这个测试集有多少场游戏？
7. 只用几场游戏进行评估可靠吗？为什么？
8. 为什么不应挑选准确率最高的 random seed？

### 完成标准

- [ ] `train_test_split.py` 成功运行。
- [ ] 创建了四个正确对齐的训练/测试对象。
- [ ] 训练和测试 shape 相加等于准备后的数据集大小。
- [ ] 训练行和测试行明显不同。
- [ ] 训练集同时包含 `WIN` 和 `LOSS`。
- [ ] Jerry 不看准备好的答案，也能解释“未见示例”。
- [ ] 学习笔记明确指出大约三场游戏的测试集不能提供可靠证据。

### 可选挑战

尝试两个不同的 `random_state`，比较哪些行进入测试集。不要优化 seed。写一句话说明这种不稳定性揭示了什么。

### Git Commit 建议

```text
Add train-test split for game sessions
```

## 周三：Jerry 的第一个训练模型

### 目标

训练逻辑回归（Logistic Regression），完成第一次 create → fit → predict 全流程。

### 为什么重要

从这里开始，程序不再按照 Jerry 手写的 WIN/LOSS 规则执行。学习算法使用带标签的训练示例估计模型，再由训练后的模型分类未见过的特征行。

### 概念

- 逻辑回归分类器
- 高层理解模型参数
- `fit`
- `predict`
- 真实标签与预测标签
- 基础准确率
- 预测不等于证明

### 任务

创建：

```text
src/week 3/day 3/logistic_regression.py
```

使用：

```python
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score
```

复用同一份准备后的特征和训练/测试划分。创建 Logistic Regression 模型，使用 `X_train` 和 `y_train` 训练，再为 `X_test` 预测标签。

两个关键操作是：

```python
model.fit(...)
model.predict(...)
```

参数必须由 Jerry 自己填写，并解释每个参数。

打印易读的真实值与预测值对比，例如：

```text
Actual: WIN | Predicted: WIN
Actual: LOSS | Predicted: WIN
```

计算基础准确率：

```text
accuracy = 正确预测数 / 总预测数
```

运行：

```bash
python "src/week 3/day 3/logistic_regression.py"
```

### 要求

- 只使用 `X_train` 和 `y_train` 训练。
- 模型完成 fit 后才能 predict。
- 为 `X_test` 预测标签。
- 保持真实标签和预测标签对齐。
- 把每个测试预测与真实结果并排打印。
- 使用 `accuracy_score` 计算准确率。
- 同时打印百分比和原始数量，例如“3 个中正确 2 个”。
- 如果 scikit-learn 报告 convergence warning，要调查原因，不能隐藏警告。
- 本周不引入 precision、recall、F1 或 confusion matrix。

### 提示

- `fit` 从特征行及其对应标签中学习。
- `predict` 要求特征列的顺序与训练时一致。
- 组合真实值和预测值之前先检查长度与 index。
- Warning 是调试线索。阅读内容，确认涉及哪个模型，再请求针对性提示。
- 测试行很少时，一个预测变化就会让准确率大幅改变。

### Jerry 必须回答的问题

1. `model.fit(X_train, y_train)` 做什么？
2. `model.predict(X_test)` 做什么？
3. 为什么必须先 `fit`，再 `predict`？
4. 什么是基础准确率？
5. 如果三场测试游戏的准确率为 100%，正确预测了多少个？
6. 为什么这个分数不能证明模型完美？
7. Jerry 是否直接编写了模型学到的 WIN/LOSS 边界？

### 完成标准

- [ ] `logistic_regression.py` 成功运行。
- [ ] Logistic Regression 模型在训练数据上完成 fit。
- [ ] 为未见过的测试行生成预测。
- [ ] 真实标签和预测标签并排显示。
- [ ] 打印准确率以及“正确数/总数”。
- [ ] Jerry 不看 AI 写的讲稿，也能解释 `fit` 和 `predict`。
- [ ] 把结果描述为教学实验，而不是生产证据。

### 可选挑战

自己选择 duration、kills、deaths、accuracy 和 reaction time，构造一场虚构的完整游戏。使用相同特征名和顺序组成一行数据，让训练后的模型进行分类。

### Git Commit 建议

```text
Train first Logistic Regression game model
```

## 周四：模型对决——相同问题，不同算法

### 目标

在同一份划分上训练 Logistic Regression、Decision Tree 和 Random Forest，再比较它们的基础准确率和行为。

### 为什么重要

算法（algorithm）是训练方法；训练后的模型（trained model）是把该方法应用于数据的结果。不同算法可以从相同示例中学习不同模式。

直观理解：

- **逻辑回归（Logistic Regression）：** 寻找分隔类别的数学边界或模式。
- **决策树（Decision Tree）：** 学习一连串类似决策的分支。
- **随机森林（Random Forest）：** 组合许多决策树的预测。

学习得到的 Decision Tree 可能包含类似规则的分支，但最终分割阈值不是 Jerry 手动指定的。

### 概念

- 算法与训练后的模型
- Logistic Regression
- Decision Tree
- Random Forest
- 使用同一划分进行公平比较
- 可复现的模型设置
- 基础准确率表格
- 分数不稳定性

### 任务

创建：

```text
src/week 3/day 4/model_comparison.py
```

使用：

```python
from sklearn.linear_model import LogisticRegression
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier
```

使用同一组 `X_train`、`X_test`、`y_train` 和 `y_test` 训练三个算法。记录：

| Model | Accuracy | Correct / Test Count | Jerry's Notes |
|---|---:|---:|---|
| Logistic Regression | ? | ? | ? |
| Decision Tree | ? | ? | ? |
| Random Forest | ? | ? | ? |

结果和 notes 由 Jerry 填写。Notes 应记录一个观察、warning 或限制，不能只写“good”或“bad”。

运行：

```bash
python "src/week 3/day 4/model_comparison.py"
```

### 要求

- 三个模型使用相同的准备数据和完全相同的训练/测试行。
- 训练三个必需分类器。
- 为使用随机性的模型设置可复现的 random state。
- 为每个模型生成预测和基础准确率。
- 打印清晰的比较表。
- 除百分比外，也显示“正确数/测试总数”。
- 记录 Jerry 为周五选择的模型，并解释原因。
- 在结果旁注明数据集很小、分数不稳定。
- 不要声称某个模型普遍“最好”。
- 不把第 4 周评估指标加入本练习。

### 提示

- 可以把模型名称和模型对象放在一个小型 collection 中，再考虑用循环。
- 每个模型都遵循相同的高层接口：create、fit、predict。
- 重用同一份划分，可以让入门比较更清楚。
- 最高分可能只代表在三行测试数据上多答对一道。
- 分数基本相同时，简单性和可解释性也可能重要。

### Jerry 必须回答的问题

1. 三个实验中哪些内容保持不变？
2. 哪些内容改变了？
3. Decision Tree 与 Jerry 第 2 周手写规则有什么区别？
4. Random Forest 为什么使用许多棵树？
5. 如果 Random Forest 得分最高，是否证明它是最佳模型？
6. 换一种 train/test split 会改变“获胜模型”吗？为什么？
7. 周五选择哪个模型？哪些证据和限制支持这个选择？

### 完成标准

- [ ] `model_comparison.py` 训练三个分类器。
- [ ] 每个模型使用同一份 train/test split。
- [ ] 比较表包含准确率和原始数量。
- [ ] 明确周五选择的模型，并给出谨慎解释。
- [ ] 没有把结果说成可靠的生产性能。
- [ ] Jerry 能解释：相同数据集 + 不同算法 = 不同的训练模型。
- [ ] Jerry 能区分算法、训练后的模型和预测。

### 可选挑战

使用另一个 `random_state` 重复完整、公平的比较，观察“获胜模型”是否改变。目标是研究不稳定性，不是选择最漂亮的划分。

### Git Commit 建议

```text
Compare three machine learning classifiers
```

## 周五：Game AI Friday #2——AI 能预测我的游戏结果吗？

### 目标

构建一个命令行教学原型：接收完整场次统计数据，使用一个训练后的模型把该场次分类为 `WIN` 或 `LOSS`。

### 为什么重要

Game AI Friday #2 清楚展示从程序员规则到数据学习模式的升级：

```text
Game AI Friday #1
状态 → Jerry 编写的规则 → 动作

Game AI Friday #2
带标签的游戏示例 → 算法 → 训练后的模型 → 预测
```

Jerry 不再编写这样的规则：

```python
if kills > 15:
    return "WIN"
```

他选择输入特征和学习算法；完成 fit 的模型从示例中学习模式。

### 概念

- 交互式命令行输入
- 输入验证和数值转换
- 一致的特征名与顺序
- 根据实验选择模型
- 对一行新数据执行推理（inference）
- 教学原型与可靠 AI 的区别
- 可选的 `predict_proba`

### 任务

创建：

```text
src/week 3/day 5/game_result_predictor.py
```

程序应训练或加载 Jerry 选择的模型，并提供类似体验：

```text
=== Jerry Game AI ===

Enter session duration: 38
Enter kills: 16
Enter deaths: 7
Enter accuracy: 48
Enter reaction time: 215

AI is thinking...

Predicted result: WIN
```

程序可能输出 `WIN` 或 `LOSS`；上面的值不是这组输入的标准答案。

运行：

```bash
python "src/week 3/day 5/game_result_predictor.py"
```

同时完成：

```text
notebooks/week3-first-machine-learning.ipynb
```

在 coding repository 的 `README.md` 中加入 Jerry 用自己语言写的 Week 3 章节：

```text
Week 3 — Machine Learning

- Features and labels
- Train/test split
- Logistic Regression
- Decision Tree
- Random Forest
- Game Result Predictor
```

#### 怎样让它更接近真实系统？

当前程序不是可靠的实时游戏预测器，因为：

1. 数据集只有 15 个场次；
2. 多个输入描述已经结束的游戏；
3. 模型没有在足够多的未见数据上验证。

更现实的未来系统需要：

- 收集数百或数千个场次；
- 记录游戏早期统计；
- 持续跟踪游戏状态；
- 使用最终结果产生前就能获得的特征。

### 要求

- 使用 Jerry 周四训练过的一个模型。
- 解释选择该模型的原因，同时说明小数据限制。
- 要求用户输入全部五个特征值。
- 验证数值输入，并清楚报告无效输入。
- 使用与训练相同的特征名和顺序构建新行。
- 每组完整输入只生成一个 `WIN` 或 `LOSS` 预测。
- 不实现隐藏的手写 WIN/LOSS 规则。
- 把结果称为完整场次分类，而不是有保证的赛前预测。
- 完成下面的整合 Notebook 清单。
- 更新 coding-repository README，说明 Week 3 学习内容和限制。
- Python 标识符、文件名、commits 和源代码注释使用英文。

### 提示

- 把输入收集与“准备一行特征”的函数分开。
- 调试时打印或 assert 训练与预测的列顺序。
- 复用周四选定的算法，但要有意识地重写或重构，不能盲目复制。
- 测试一个正常输入和至少一个无效输入。
- 如果使用 probability 输出，先检查选定分类器是否支持 `predict_proba`。

### Jerry 必须回答的问题

1. 你是否直接编写了决定 `WIN` 或 `LOSS` 的规则？
2. Jerry 选择了什么？模型学习了什么？
3. 为什么为周五原型选择这个算法？
4. 为什么新数据行必须使用相同的特征名和顺序？
5. 这个模型能在游戏开始前预测胜负吗？为什么不能？
6. 真正的游戏早期预测需要哪些数据？
7. 第 2 周 Game AI 和第 3 周 Game AI 有什么区别？
8. README 中写了哪一个限制？

### 完成标准

- [ ] `game_result_predictor.py` 接收五个值并返回一个模型预测。
- [ ] 正常输入不修改源代码即可运行。
- [ ] 无效数值输入不会产生令人困惑的 traceback。
- [ ] 由选定的训练模型而不是手写结果规则完成分类。
- [ ] Jerry 能解释从 CSV 行到一个预测的完整路径。
- [ ] Notebook 包含全部十三个必需部分。
- [ ] Coding-repository README 包含 Week 3 章节和诚实的限制说明。
- [ ] Jerry 把程序称为教学型完整场次分类器。

### 可选挑战

如果选定模型支持 `predict_proba()`，可以显示可选 confidence：

```text
Prediction: WIN
Confidence: 72%
```

解释模型输出并不自动等于校准良好、值得信任的真实概率，尤其是在只有 15 个场次时。

### Git Commit 建议

```text
Add Game AI result predictor
```

## Week 3 整合 Notebook

创建：

```text
notebooks/week3-first-machine-learning.ipynb
```

Notebook 应包含：

1. 加载数据集。
2. 检查数据。
3. 处理缺失值并解释选择。
4. 定义 `X` 和 `y`。
5. 划分训练数据和测试数据。
6. 训练 Logistic Regression。
7. 训练 Decision Tree。
8. 训练 Random Forest。
9. 比较基础准确率和“正确数/测试总数”。
10. 并排显示预测值和真实结果。
11. 写下 Jerry 自己的观察。
12. 解释数据集和建模限制。
13. 用 Jerry 自己的语言写“What I Learned”。

使用 Markdown cells 回答以下提示：

- 哪个结果让你意外？
- 不同划分中的获胜模型是否保持不变？
- 哪个特征可能有用？什么证据支持这个想法？
- 这 15 行数据不能证明什么？
- 为什么这是完整场次分类，而不是赛前预测？

不要让 AI 填完全部结论。写观察本身就是学习的一部分。

适合最后文档整理的 commit：

```text
Complete Week 3 ML notebook
```

## Game AI 路线预告

```text
Game AI Friday #1
Rule-Based Agent
↓
Game AI Friday #2
ML Game Result Predictor
↓
未来
Computer Vision Agent
↓
Game State Recognition
↓
Reinforcement Learning
↓
Roblox Studio AI Agent
```

每个 Friday 都会增加一种能力。未来项目用于保持动力，不是第 3 周的实现任务。

## 周六演示

在 `AI-study/game-statistics-analyzer/` 中，Jerry 应演示：

1. 加载 `data/game-sessions.csv`；
2. 选择五个特征和 `result` 标签；
3. 解释缺失 accuracy 的处理策略；
4. 创建 train/test split，并显示四个 shape；
5. 训练 Logistic Regression、Decision Tree 和 Random Forest；
6. 显示包含准确率和原始数量的模型比较表；
7. 显示真实标签和预测标签；
8. 使用一场新游戏运行 Game AI Friday predictor；
9. 打开整合 Notebook 和 README 更新；
10. 展示有意义的 Week 3 Git 历史。

建议 Git 检查：

```bash
git log --oneline -8
```

Jerry 应按以下顺序讲解：

```text
Dataset → X and y → Split → Algorithm → fit → predict → compare
```

他还必须说明模型为什么不是可靠的赛前预测器。

## 家长复盘问题

1. **什么是特征？**

   预期概念：模型用来作出预测的输入变量。

2. **模型的标签是什么？**

   预期答案：`WIN / LOSS`。

3. **为什么要分开训练数据和测试数据？**

   预期概念：使用模型训练时没有见过的示例评估它。

4. **`model.fit()` 做什么？**

   预期概念：使用训练示例训练模型。

5. **`model.predict()` 做什么？**

   预期概念：使用训练后的模型为新输入预测标签。

6. **第 2 周 Game AI 和第 3 周 Game AI 有什么区别？**

   预期概念：第 2 周由 Jerry 编写规则；第 3 周由模型从带标签的示例中学习模式。

7. **模型准确率很高，这是否代表它很可靠？**

   预期答案：不一定，因为数据集和测试集都非常小。

8. **这个模型真的能在游戏开始前预测你是否会赢吗？**

   预期答案：不能，因为许多特征是最终场次统计。

9. **周五选择了哪个模型？为什么？**

10. **增加哪些数据能让项目更接近真实系统？**

## 每周评分

| 项目 | 分值 |
|---|---:|
| 理解特征/标签 | 10 |
| 训练集/测试集划分 | 10 |
| Logistic Regression | 15 |
| Decision Tree | 10 |
| Random Forest | 10 |
| 模型比较 | 10 |
| 小型数据集推理 | 10 |
| Game AI Friday | 15 |
| 解释代码 | 5 |
| Git + 文档 | 5 |
| **总分** | **100** |

评分不能代替真正理解。如果缺少某一项，只修复该项、再做一次有意义的 commit，并重新演示相关部分，不需要重新开始整周。

## 第 3 周成功标准

如果 Jerry 能诚实地说出下面这句话，第 3 周就成功了：

> 我使用游戏场次数据训练了机器学习模型，并用它们把未见场次分类为 WIN 或 LOSS。

更重要的是，他应该理解：

```text
特征
↓
训练示例
↓
算法
↓
模型
↓
预测
```

以及下面两者的区别：

```text
程序员编写的规则
```

和：

```text
从数据中学习的模式
```

## 控制学习范围

第 3 周使用 Python、Pandas、NumPy、scikit-learn、Jupyter Notebook，以及必要时使用 Matplotlib。

本周不引入 PyTorch、TensorFlow、神经网络、YOLO、强化学习实现、LLM APIs、FastAPI 或 Streamlit。

使用渐进式帮助：

```text
问题 → 提示 → 伪代码 → 小型示例 → 只有确实必要时才提供完整解答
```

重要的机器学习实现必须由 Jerry 完成。

避免使用 `done`、`update`、`week3` 或 `final` 这类模糊 commit。Commit message 应描述实际产出的学习成果。
