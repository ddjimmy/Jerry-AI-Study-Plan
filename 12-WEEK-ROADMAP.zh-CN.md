# 12 周 AI 学习路线图

[English Version](12-WEEK-ROADMAP.md)

## 第一阶段：Python 与数据

### 第 1 周：面向 AI 的 Python

构建一个**游戏数据统计分析器（Game Statistics Analyzer）**。

技能：

- Python 数据结构
- 函数
- CSV/JSON
- Git/GitHub
- 基础程序组织

### 第 2 周：NumPy、Pandas 与数据可视化

构建一个**游戏表现仪表板（Game Performance Dashboard）**。

技能：

- NumPy 数组和统计计算
- Pandas DataFrame
- 筛选与分组
- 缺失值处理
- Matplotlib
- 根据数据证据得出结论

## 第二阶段：机器学习

### 第 3 周：第一个机器学习模型

使用 scikit-learn 训练分类模型。

技能：

- 特征（feature）和标签（label）
- 训练集/测试集划分
- 逻辑回归（Logistic Regression）
- 决策树（Decision Tree）
- 随机森林（Random Forest）

### 第 4 周：模型评估

构建一个比较多种算法的**模型对决（Model Battle）**。

技能：

- 准确率（accuracy）
- 精确率（precision）
- 召回率（recall）
- F1 分数
- 混淆矩阵（confusion matrix）
- 过拟合（overfitting）
- 欠拟合（underfitting）

### 第 5 周：机器学习项目

构建 **AI 游戏表现教练（AI Gaming Performance Coach）**。

输入可以包括：

- 游戏时长
- 准确率
- 反应时间
- 胜场/负场
- 击杀数/死亡数
- 睡眠时长

目标：预测一次游戏状态是好还是差，并解释预测结果。

## 第三阶段：深度学习与计算机视觉

### 第 6 周：PyTorch 基础

构建第一个神经网络。

技能：

- 张量（tensor）
- 模型层
- 损失函数（loss function）
- 梯度（gradient）
- 反向传播（backpropagation）
- 优化器（optimizer）
- 训练循环
- 保存和加载模型

### 第 7 周：计算机视觉

构建一个**图像分类应用（Image Classifier App）**。

技能：

- 图像表示
- 通道与像素
- 卷积神经网络（CNN）概念
- 训练与验证
- 对自己的图片执行推理

### 第 8 周：目标检测

构建一个**自定义 YOLO 检测器**。

技能：

- 图像分类与目标检测（object detection）的区别
- 边界框（bounding box）
- 使用预训练模型进行推理
- 视频/摄像头推理
- 小型自定义数据集
- 模型训练与验证

## 第四阶段：大语言模型应用

### 第 9 周：大语言模型开发

构建 **AI 游戏教练 v2**。

技能：

- 提示词（prompt）
- 上下文（context）
- 结构化输出
- Python API 集成
- 将机器学习结果与大语言模型解释结合起来

### 第 10 周：嵌入向量与 RAG

构建一个 **AI 学习伙伴（AI Study Buddy）**。

技能：

- 文档分块（chunking）
- 嵌入向量（embedding）
- 语义搜索
- 检索
- 检索增强生成（RAG）
- 基于个人笔记回答问题
- 生成测验

## 第五阶段：AI 工程

### 第 11 周：应用与 API

把模型变成其他人可以使用的应用程序。

技能：

- Streamlit
- 文件上传
- 表单
- 模型推理
- FastAPI
- REST API 端点
- 简单的应用架构

### 第 12 周：综合项目 AI Game Lab

整合以下模块：

1. 游戏表现分析器
2. 截图/目标检测器
3. AI 教练
4. Web UI

最终项目仓库应包含：

```text
ai-game-lab/
├── README.md
├── README.zh-CN.md
├── data/
├── notebooks/
├── src/
├── models/
├── api/
├── app/
└── requirements.txt
```

学生应能解释系统架构、数据集、模型选择、评估结果、已知限制和下一步改进方向。
