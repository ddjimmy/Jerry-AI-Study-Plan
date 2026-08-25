# Week 3 — Machine Learning: Can AI Predict a Win?

[中文版本](../zh-CN/week03.md)

## Weekly Mission

Move from data analysis to supervised machine learning by training models to classify a completed game session as `WIN` or `LOSS`.

The workflow for this week is:

```text
Dataset
↓
Features X
↓
Label y
↓
Train/Test Split
↓
Choose Algorithm
↓
Train Model
↓
Predict
↓
Compare Results
```

Continue the work in Jerry's coding repository:

[chessfan7777/AI-study](https://github.com/chessfan7777/AI-study)

Use the existing project and dataset:

```text
game-statistics-analyzer/data/game-sessions.csv
```

The coding repository uses `game-sessions.csv` with a hyphen. Use that real filename in Jerry's scripts. Do not create another copy of the dataset.

Work for 60–90 minutes each weekday. Aim for approximately 30% learning and 70% building, debugging, explaining, and committing. Follow:

**Learn → Build → Debug → Explain → Improve → Demo**

AI may ask questions, explain concepts, provide hints, help debug Jerry's attempt, or review code. It must not provide a complete copy-paste solution. If Jerry cannot explain important code, the task is not complete.

## Accurate Project Description

The target label is:

```text
result → WIN / LOSS
```

The candidate features are:

```text
duration_minutes
kills
deaths
accuracy
reaction_time_ms
```

Most of these values describe a completed game. Therefore, this project is accurately described as:

> Classifying a completed game session as WIN or LOSS from its session statistics.

It is not yet a true prediction of whether Jerry will win before a game begins. A real early or live predictor would need information available before or during the game, such as:

```text
kills_after_2_minutes
deaths_after_2_minutes
current_health
current_score
early_reaction_time
position
opponent_state
```

Keep this distinction visible in the notebook, README, Friday program, and Saturday explanation.

## Tiny Dataset Reality Check

The current dataset contains only 15 sessions. It is suitable for learning the machine-learning workflow, but it is not enough evidence for a reliable production model.

With a 20% test split, the test set may contain only about three games. Even 100% accuracy could mean only three correct predictions. A different split may produce a very different result.

Jerry must not claim:

> My AI is perfect or ready for real-world use.

He should be able to say:

> This is an educational prototype. Its measured accuracy is unstable because the dataset and test set are extremely small.

## Coding Structure

Adapt to Jerry's existing folders without reorganizing old work:

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

Because the directory names contain spaces, quote script paths in terminal commands.

Executable source code remains English-only. Do not create separate source files for English and Chinese.

## Week 3 Setup

From the `AI-study/game-statistics-analyzer/` directory, activate the existing virtual environment.

PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

Install the required libraries:

```bash
python -m pip install numpy pandas scikit-learn matplotlib jupyter
```

Create or update `requirements.txt` with the direct libraries actually used. Do not commit new virtual-environment files.

This week uses basic accuracy only as an introductory comparison. Precision, recall, F1, confusion matrices, overfitting, and underfitting belong to Week 4.

## Monday — From Rules to Learning

### Goal

Understand feature, label, model, training, prediction, and supervised learning by preparing Jerry's game-session data for machine learning.

### Why It Matters

Week 2's game agent followed rules Jerry wrote. A supervised-learning model instead studies labeled examples and learns a pattern that maps inputs to an expected answer.

```text
Week 2
State → Jerry-written rules → Action

Week 3
Labeled examples → Learning algorithm → Trained model → Prediction
```

This rule is not machine learning:

```python
if kills > 15:
    result = "WIN"
```

Jerry directly wrote the decision. In machine learning, Jerry chooses features and an algorithm, but the model learns parameters from examples.

### Concepts

- supervised learning
- feature and feature matrix `X`
- label/target and target vector `y`
- training examples
- model and prediction
- numeric input requirements
- missing values
- data leakage at a beginner level

### Mission

Create:

```text
src/week 3/day 1/prepare_ml_data.py
```

From `AI-study/game-statistics-analyzer/`, run:

```bash
python "src/week 3/day 1/prepare_ml_data.py"
```

Load `data/game-sessions.csv` with Pandas. Define:

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

Inspect:

```python
X.head()
y.head()
X.shape
y.shape
```

Locate the missing `accuracy` value. Choose a documented strategy for machine learning:

- drop the incomplete row;
- mean imputation; or
- median imputation.

Do not silently fill the value and do not replace it with zero without a defensible reason. If using imputation in the final workflow, avoid learning the fill value from test data; ask for a hint about fitting preprocessing on training data.

### Requirements

- Use Jerry's existing game-session CSV, not a student-performance dataset.
- Store the feature names in one clear collection such as `feature_names`.
- Exclude `game_id`, `date`, and `result` from `X`.
- Set `y` to the `result` column.
- Print feature names, `X.head()`, `y.head()`, and both shapes.
- Count the labels and confirm both `WIN` and `LOSS` are present.
- Detect the missing accuracy before applying the chosen strategy.
- Explain the missing-value choice in a code comment or learning note.
- Keep the original CSV unchanged.

### Hints

- A DataFrame can select several columns from a list of names.
- `isna().sum()` can reveal missing values by column.
- `game_id` identifies a row but does not describe game performance.
- Raw `date` is not a useful first feature unless it is transformed into meaningful information.
- For the simplest first workflow, dropping one incomplete row is acceptable if Jerry explains the cost.

### Questions Jerry Must Answer

1. What is a feature?
2. What is a label?
3. What is the label in this project?
4. Which columns are used as features?
5. Why is `game_id` usually not a useful predictive feature?
6. Why is raw `date` not appropriate for this first model?
7. Is the handwritten `if kills > 15` rule machine learning? Why not?
8. How did you handle missing accuracy, and what information was lost or assumed?

### Done When

- [ ] `prepare_ml_data.py` runs from the project directory.
- [ ] `X` contains only the five selected numeric features.
- [ ] `y` contains `WIN` and `LOSS`.
- [ ] The printed shapes agree with the chosen missing-value strategy.
- [ ] No missing feature value is passed forward accidentally.
- [ ] Jerry can explain: `X = inputs`, `y = correct labels`, and `model = learned relationship`.
- [ ] Jerry can accurately describe the project as completed-session classification.

### Bonus Challenge

Create `feature_names` and print a numbered, readable feature list without manually printing each name.

### Suggested Git Commit

```text
Prepare game data for machine learning
```

## Tuesday — Training Data vs Testing Data

### Goal

Use `train_test_split()` and understand why training and testing must use different examples.

### Why It Matters

Training questions are the examples a student studies. Test questions should be new. If the student receives the exact test answers during study, the test tells us little about learning.

Machine learning follows the same principle:

```text
training data → model learns
test data → model is checked on unseen examples
```

### Concepts

- training set and test set
- `X_train`, `X_test`, `y_train`, and `y_test`
- `train_test_split`
- `test_size`
- `random_state`
- reproducibility
- optional stratification
- unstable results from tiny samples

### Mission

Create:

```text
src/week 3/day 2/train_test_split.py
```

Use:

```python
from sklearn.model_selection import train_test_split
```

Reuse Monday's feature and label preparation, then split `X` and `y` into four objects. Inspect:

```python
X_train.shape
X_test.shape
y_train.shape
y_test.shape
```

Print the label counts in the complete, training, and test sets. Use one intentional `test_size` and `random_state`, and record why reproducibility is useful.

Run:

```bash
python "src/week 3/day 2/train_test_split.py"
```

### Requirements

- Split features and labels together so rows remain aligned.
- Keep test examples out of model training.
- Use a test set near 20% for the first experiment.
- Set and print the chosen `random_state`.
- Print all four shapes.
- Print enough identifiers or indices to show that train and test rows differ.
- Inspect class counts and ensure the training set contains both labels.
- State approximately how many test games are being used.
- Do not repeatedly change the seed just to obtain a higher future score.

### Hints

- One call to `train_test_split` can return all four objects.
- The order of returned objects matters; compare it with the documentation or function signature.
- `random_state` makes a random split repeatable.
- `stratify=y` can help preserve label proportions, but a three-row test set is still unstable.
- Print indices temporarily to verify alignment and separation.

### Questions Jerry Must Answer

1. Why is training and testing on exactly the same rows misleading?
2. What does the model do with training data?
3. What is test data used for?
4. What does `test_size` control?
5. What practical purpose does `random_state` serve?
6. How many games are in this test set?
7. Is evaluation on only a few games reliable? Why not?
8. Why should you not shop for the random seed with the highest score?

### Done When

- [ ] `train_test_split.py` runs successfully.
- [ ] Four correctly aligned train/test objects are created.
- [ ] Training and test shapes add up to the prepared dataset size.
- [ ] Training and test rows are visibly different.
- [ ] The training set contains both `WIN` and `LOSS`.
- [ ] Jerry can explain unseen examples without reading a prepared answer.
- [ ] The learning note warns that a roughly three-game test set is not reliable evidence.

### Bonus Challenge

Try two different `random_state` values and compare which rows enter the test set. Do not optimize the seed. Write one sentence about what this instability reveals.

### Suggested Git Commit

```text
Add train-test split for game sessions
```

## Wednesday — Jerry's First Trained Model

### Goal

Train Logistic Regression and complete the first full create → fit → predict cycle.

### Why It Matters

This is the point where the program stops following a manually written WIN/LOSS rule. The learning algorithm uses labeled training examples to estimate a model, and that trained model classifies unseen feature rows.

### Concepts

- Logistic Regression classifier
- model parameters at a high level
- `fit`
- `predict`
- actual labels vs predicted labels
- basic accuracy
- a prediction is not proof

### Mission

Create:

```text
src/week 3/day 3/logistic_regression.py
```

Use:

```python
from sklearn.linear_model import LogisticRegression
from sklearn.metrics import accuracy_score
```

Reuse the same prepared features and train/test split. Create a Logistic Regression model, train it on `X_train` and `y_train`, and predict labels for `X_test`.

The two essential operations are:

```python
model.fit(...)
model.predict(...)
```

Jerry must fill the arguments himself and explain each argument.

Print a readable actual-vs-predicted comparison, for example:

```text
Actual: WIN | Predicted: WIN
Actual: LOSS | Predicted: WIN
```

Calculate basic accuracy:

```text
accuracy = correct predictions / total predictions
```

Run:

```bash
python "src/week 3/day 3/logistic_regression.py"
```

### Requirements

- Train only on `X_train` and `y_train`.
- Predict only after the model has been fitted.
- Predict the labels for `X_test`.
- Keep actual and predicted labels aligned.
- Print every test prediction beside its actual result.
- Calculate accuracy with `accuracy_score`.
- Print both the percentage and the raw count, such as “2 correct out of 3.”
- If scikit-learn reports a convergence warning, investigate it instead of hiding it.
- Do not introduce precision, recall, F1, or confusion matrices this week.

### Hints

- `fit` learns from feature rows and their matching labels.
- `predict` expects feature columns in the same order used for training.
- Check lengths and indices before combining actual and predicted values.
- A warning is a debugging clue. Read it, identify the model involved, and ask for a focused hint.
- With only a few test rows, one changed prediction can move accuracy dramatically.

### Questions Jerry Must Answer

1. What does `model.fit(X_train, y_train)` do?
2. What does `model.predict(X_test)` do?
3. Why must `predict` happen after `fit`?
4. What is basic accuracy?
5. If accuracy is 100% on three test games, how many predictions were correct?
6. Why does that score not prove the model is perfect?
7. Did Jerry directly program the learned WIN/LOSS boundary?

### Done When

- [ ] `logistic_regression.py` runs successfully.
- [ ] A Logistic Regression model is fitted on training data.
- [ ] Predictions are produced for unseen test rows.
- [ ] Actual and predicted labels are shown together.
- [ ] Accuracy and the raw correct/total count are printed.
- [ ] Jerry can explain `fit` and `predict` without an AI-written script.
- [ ] The result is described as an educational experiment, not production evidence.

### Bonus Challenge

Invent one fictional completed session by choosing duration, kills, deaths, accuracy, and reaction time. Put the values into a one-row structure with the same feature names and order, then ask the fitted model to classify it.

### Suggested Git Commit

```text
Train first Logistic Regression game model
```

## Thursday — Model Battle: Different Algorithms, Same Problem

### Goal

Train Logistic Regression, a Decision Tree, and a Random Forest on the same split, then compare their basic accuracy and behavior.

### Why It Matters

An algorithm is a training approach; the trained model is the result of applying that approach to data. Different algorithms can learn different patterns from the same examples.

Intuition:

- **Logistic Regression:** looks for a mathematical boundary or pattern separating classes.
- **Decision Tree:** learns a sequence of decision-style splits.
- **Random Forest:** combines the predictions of many decision trees.

A learned Decision Tree may contain split-like logic, but Jerry does not manually choose those final split thresholds.

### Concepts

- algorithm vs trained model
- Logistic Regression
- Decision Tree
- Random Forest
- fair comparison using the same split
- reproducible model settings
- basic accuracy table
- score instability

### Mission

Create:

```text
src/week 3/day 4/model_comparison.py
```

Use:

```python
from sklearn.linear_model import LogisticRegression
from sklearn.tree import DecisionTreeClassifier
from sklearn.ensemble import RandomForestClassifier
```

Train all three algorithms with the same `X_train`, `X_test`, `y_train`, and `y_test`. Record:

| Model | Accuracy | Correct / Test Count | Jerry's Notes |
|---|---:|---:|---|
| Logistic Regression | ? | ? | ? |
| Decision Tree | ? | ? | ? |
| Random Forest | ? | ? | ? |

Jerry fills the results and notes. Notes should mention one observation, warning, or limitation rather than simply saying “good” or “bad.”

Run:

```bash
python "src/week 3/day 4/model_comparison.py"
```

### Requirements

- Use the same prepared dataset and exact same train/test rows for all models.
- Train all three required classifiers.
- Set reproducible random states for models that use randomness.
- Generate predictions and basic accuracy for each model.
- Print a readable comparison table.
- Include correct/test counts, not only percentages.
- Record which model Jerry selects for Friday and explain the choice.
- Mention the tiny dataset and score instability beside the results.
- Do not declare a universally “best model.”
- Keep Week 4 evaluation metrics out of this exercise.

### Hints

- Store model names and model objects in a small collection, then consider a loop.
- Each model follows the same high-level interface: create, fit, predict.
- Reusing one split makes the introductory comparison less confusing.
- A highest score may represent one extra correct answer on a three-row test set.
- Simplicity and explainability can matter when scores are effectively tied.

### Questions Jerry Must Answer

1. What stays the same across the three experiments?
2. What changes?
3. How is a Decision Tree different from Jerry's Week 2 handwritten rules?
4. Why does a Random Forest use many trees?
5. If Random Forest has the highest score, does that prove it is the best model?
6. Could a different train/test split change the winner? Why?
7. Which model will you use Friday, and what evidence and limitations support that choice?

### Done When

- [ ] `model_comparison.py` trains all three classifiers.
- [ ] Every model uses the same train/test split.
- [ ] The comparison table contains accuracy and raw counts.
- [ ] Friday's selected model is identified with a cautious explanation.
- [ ] No result is presented as reliable production performance.
- [ ] Jerry can explain: same dataset + different algorithms = different learned models.
- [ ] Jerry can distinguish an algorithm, a trained model, and a prediction.

### Bonus Challenge

Repeat the entire fair comparison with one other `random_state`. Observe whether the winner changes. The goal is to study instability, not to choose the most flattering split.

### Suggested Git Commit

```text
Compare three machine learning classifiers
```

## Friday — Game AI Friday #2: Can AI Predict My Game Result?

### Goal

Build a command-line educational prototype that accepts completed-session statistics and uses one trained model to classify the session as `WIN` or `LOSS`.

### Why It Matters

Game AI Friday #2 makes the progression from programmed rules to learned patterns visible:

```text
Game AI Friday #1
State → Jerry-written rules → Action

Game AI Friday #2
Labeled game examples → Algorithm → Trained model → Prediction
```

Jerry no longer writes a rule such as:

```python
if kills > 15:
    return "WIN"
```

He selects the input features and learning algorithm; the fitted model learns a pattern from examples.

### Concepts

- interactive command-line input
- input validation and numeric conversion
- consistent feature names and order
- model selection based on an experiment
- inference on one new row
- educational prototype vs reliable AI
- optional `predict_proba`

### Mission

Create:

```text
src/week 3/day 5/game_result_predictor.py
```

The program should train or load Jerry's selected model, then provide an experience similar to:

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

The program may output `WIN` or `LOSS`; the example is not an expected answer for those values.

Run:

```bash
python "src/week 3/day 5/game_result_predictor.py"
```

Also finish:

```text
notebooks/week3-first-machine-learning.ipynb
```

Update the coding repository's `README.md` with a Week 3 section in Jerry's own words:

```text
Week 3 — Machine Learning

- Features and labels
- Train/test split
- Logistic Regression
- Decision Tree
- Random Forest
- Game Result Predictor
```

#### What Would Make This More Real?

The program is not a reliable live-game predictor because:

1. the dataset has only 15 sessions;
2. several inputs describe the finished game;
3. the model has not been tested on enough unseen data.

A more realistic future system would:

- collect hundreds or thousands of sessions;
- record early-game statistics;
- track state over time;
- use features available before the final result is known.

### Requirements

- Use one of the models Jerry trained Thursday.
- Explain why that model was selected, including the tiny-data limitation.
- Request all five feature values from the user.
- Validate numeric input and report invalid input clearly.
- Build the new row with the same feature names and order used for training.
- Produce exactly one `WIN` or `LOSS` prediction per completed input.
- Do not implement a hidden manual WIN/LOSS rule.
- Label the result as completed-session classification, not a guaranteed pre-game prediction.
- Complete the integrated notebook checklist below.
- Update the coding-repository README with Week 3 learning and limitations.
- Keep Python identifiers, filenames, commits, and source comments in English.

### Hints

- Keep input collection separate from the function that prepares one feature row.
- Print or assert the training and prediction column order while debugging.
- Reuse Thursday's selected algorithm, but Jerry should rewrite or refactor deliberately rather than blindly copy.
- Test one normal input and at least one invalid input.
- If using probability output, first check whether the selected classifier supports `predict_proba`.

### Questions Jerry Must Answer

1. Did you directly program the rules that decide `WIN` or `LOSS`?
2. What did Jerry choose, and what did the model learn?
3. Why did you select this algorithm for the Friday prototype?
4. Why must the new row use the same feature names and order?
5. Can this model predict a win before the game starts? Why not?
6. What data would be needed for a real early-game predictor?
7. What is the difference between Week 2 Game AI and Week 3 Game AI?
8. What is one limitation you wrote in the README?

### Done When

- [ ] `game_result_predictor.py` accepts five values and returns one model prediction.
- [ ] Valid input runs without editing the source.
- [ ] Invalid numeric input is handled without a confusing traceback.
- [ ] The selected trained model, not a handwritten outcome rule, makes the classification.
- [ ] Jerry can explain the complete path from CSV rows to one prediction.
- [ ] The notebook contains all thirteen required parts.
- [ ] The coding-repository README contains a Week 3 section and honest limitations.
- [ ] Jerry calls the program an educational completed-session classifier.

### Bonus Challenge

If the selected model supports `predict_proba()`, display an optional confidence value:

```text
Prediction: WIN
Confidence: 72%
```

Explain that this model output is not automatically a calibrated, trustworthy real-world probability, especially with only 15 sessions.

### Suggested Git Commit

```text
Add Game AI result predictor
```

## Integrated Week 3 Notebook

Create:

```text
notebooks/week3-first-machine-learning.ipynb
```

The notebook should contain:

1. Load the dataset.
2. Inspect the data.
3. Handle missing values and explain the choice.
4. Define `X` and `y`.
5. Split training and test data.
6. Train Logistic Regression.
7. Train a Decision Tree.
8. Train a Random Forest.
9. Compare basic accuracy and raw correct/test counts.
10. Show predictions beside actual results.
11. Write Jerry's own observations.
12. Explain dataset and modeling limitations.
13. Write “What I Learned” in Jerry's own words.

Use Markdown cells to answer prompts such as:

- Which result surprised you?
- Did the winning model stay the same across splits?
- Which feature might be useful, and what evidence supports that thought?
- What can these 15 rows not prove?
- Why is this completed-session classification rather than pre-game prediction?

Do not let AI fill in all conclusions. The observations are part of the learning.

A useful final documentation commit is:

```text
Complete Week 3 ML notebook
```

## Game AI Roadmap Teaser

```text
Game AI Friday #1
Rule-Based Agent
↓
Game AI Friday #2
ML Game Result Predictor
↓
Future
Computer Vision Agent
↓
Game State Recognition
↓
Reinforcement Learning
↓
Roblox Studio AI Agent
```

Each Friday adds one capability. The future items are motivation, not Week 3 implementation tasks.

## Saturday Demo

From `AI-study/game-statistics-analyzer/`, Jerry should demonstrate:

1. loading `data/game-sessions.csv`;
2. selecting the five features and `result` label;
3. explaining the missing-accuracy strategy;
4. creating the train/test split and showing all four shapes;
5. training Logistic Regression, Decision Tree, and Random Forest;
6. showing the model comparison table with accuracy and raw counts;
7. showing actual vs predicted labels;
8. running the Game AI Friday predictor with one new session;
9. opening the integrated notebook and README update;
10. showing meaningful Week 3 Git history.

Suggested Git check:

```bash
git log --oneline -8
```

Jerry should narrate:

```text
Dataset → X and y → Split → Algorithm → fit → predict → compare
```

He must also state why the model is not a reliable pre-game predictor.

## Parent Review Questions

1. **What is a feature?**

   Expected concept: an input variable the model uses to make a prediction.

2. **What is the label in your model?**

   Expected: `WIN / LOSS`.

3. **Why do we separate training and test data?**

   Expected concept: to evaluate the model using examples it did not train on.

4. **What does `model.fit()` do?**

   Expected concept: it trains the model using training examples.

5. **What does `model.predict()` do?**

   Expected concept: it uses the trained model to predict labels for new inputs.

6. **What is the difference between Week 2 Game AI and Week 3 Game AI?**

   Expected concept: in Week 2 Jerry wrote the rules; in Week 3 the model learned patterns from labeled examples.

7. **Your model got high accuracy. Does that mean it is reliable?**

   Expected: not necessarily, because the dataset and test set are extremely small.

8. **Can this model really predict whether you will win before a game starts?**

   Expected: not really, because many features are final session statistics.

9. **Which model did you select for Friday, and why?**

10. **What additional data would make the project more realistic?**

## Weekly Score

| Category | Points |
|---|---:|
| Feature/label understanding | 10 |
| Train/test split | 10 |
| Logistic Regression | 15 |
| Decision Tree | 10 |
| Random Forest | 10 |
| Model comparison | 10 |
| Small-dataset reasoning | 10 |
| Game AI Friday | 15 |
| Explain the code | 5 |
| Git + documentation | 5 |
| **Total** | **100** |

Scoring does not replace understanding. If something is missing, fix that item, make another meaningful commit, and repeat the relevant demo instead of restarting the whole week.

## Week 3 Success Criteria

Week 3 is successful if Jerry can truthfully say:

> I trained machine-learning models using game-session data and used them to classify unseen sessions as WIN or LOSS.

More importantly, he should understand:

```text
Features
↓
Training Examples
↓
Algorithm
↓
Model
↓
Prediction
```

and the difference between:

```text
rules written by a programmer
```

and:

```text
patterns learned from data
```

## Controlled Scope

Week 3 uses Python, Pandas, NumPy, scikit-learn, Jupyter Notebook, and Matplotlib only if useful.

Do not introduce PyTorch, TensorFlow, neural networks, YOLO, reinforcement-learning implementation, LLM APIs, FastAPI, or Streamlit this week.

Use progressive assistance:

```text
Question → Hint → Pseudocode → Small Example → Complete Solution only if genuinely necessary
```

The important machine-learning implementation belongs to Jerry.

Avoid vague commits such as `done`, `update`, `week3`, or `final`. Commit messages should describe the learning artifact produced.
