# Week 4 — Can We Trust the AI?

[中文版本](../zh-CN/week04.md)

## Weekly Mission

Week 3 answered:

> Can AI make a prediction?

Week 4 asks:

> A model can make predictions, but how do we know whether those predictions are useful or trustworthy?

Continue evaluating Jerry's completed-session classifier in:

[chessfan7777/AI-study](https://github.com/chessfan7777/AI-study)

Use the existing dataset:

```text
game-statistics-analyzer/data/game-sessions.csv
```

The evaluation workflow is:

```text
Prediction
↓
Evaluation
↓
Error Analysis
↓
Model Comparison
↓
Generalization
↓
Model Choice
```

Work for 60–90 minutes each weekday, with approximately 30% learning and 70% building, debugging, explaining, and committing. Follow:

**Learn → Build → Debug → Explain → Improve → Demo**

AI may guide Jerry through questions, hints, pseudocode, small examples, and partial code. It must not provide complete copy-paste scripts. If Jerry cannot explain the important code and metric meanings, the work is not complete.

## Week 3 Starting Point

Jerry's existing Week 3 implementation already:

- loads `data/game-sessions.csv`;
- uses `duration_minutes`, `kills`, `deaths`, `accuracy`, and `reaction_time_ms` as features;
- uses `result` with `WIN` and `LOSS` as the label;
- drops the one row with missing feature data;
- produces 14 usable rows;
- uses a 20% stratified split with `random_state=42`;
- trains Logistic Regression, Decision Tree, and Random Forest;
- reports basic accuracy;
- uses Random Forest in the Week 3 command-line predictor.

Week 4 should reuse this data preparation and split before changing anything. This keeps the first comparisons understandable. Jerry may refactor repeated preparation later, but should not reorganize old work just to satisfy this plan.

The project is still a **completed-session classifier**, not a true pre-game predictor. Most features describe the final session, so model evaluation does not remove that modeling limitation.

## Tiny Dataset Warning

After removing the incomplete row, only about 14 sessions remain. A 20% test split contains roughly three games.

On a three-game test set, 100% may mean only `3 / 3` correct, while 66.7% may mean `2 / 3` correct. Metrics on three examples are mathematically valid but scientifically weak. One changed prediction can move accuracy, precision, recall, or F1 dramatically. Use language such as:

> The model achieved 100% on this tiny test split, but the result is too unstable to support strong conclusions.

Do not use:

> My model has 100% accuracy, so it is excellent.

It is acceptable for Friday's conclusion to be:

> No clear winner. We need more varied game sessions and more unseen test examples.

## Coding Structure

Add Week 4 without reorganizing Weeks 1–3:

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

Quote paths containing spaces in terminal commands. Keep filenames, identifiers, comments, and Git commit messages in English. Do not create separate source code by language.

## Week 4 Setup and Repo Cleanup Mission (15–20 Minutes)

From `AI-study/game-statistics-analyzer/`, activate the existing environment:

```powershell
.\.venv\Scripts\Activate.ps1
```

Confirm the required libraries are available:

```bash
python -m pip install numpy pandas scikit-learn matplotlib jupyter
```

Before starting new code, inspect the coding repository from `AI-study/`:

```bash
git status
git log --oneline -8
git ls-files ".venv/*"
git ls-files "*main.java"
```

The coding repository currently contains tracked virtual-environment files and an unrelated `src/week 3/day 1/main.java`. Jerry should verify they are unrelated, remove stray Hello World or temporary files, confirm the correct `.venv/` rule is in `.gitignore`, and bring the coding README through Week 3 before beginning the main Week 4 work.

Understand the difference:

- an **ignored file** is kept out of future Git additions;
- an **already tracked file** remains tracked even after a matching ignore rule is added.

After confirming the path, this command removes the virtual environment from Git's index without deleting Jerry's local environment:

```bash
git rm -r --cached game-statistics-analyzer/.venv
```

Review `git status` before committing. Do not delete or untrack files without understanding the command.

Keep unrelated Java files, temporary files, editor output, and generated environments out of Week 4 commits. Never commit secrets or credentials.

Suggested cleanup commit:

```text
Clean repository before Week 4
```

## Monday — Accuracy Can Lie

### Goal

Understand accuracy, majority class, class imbalance, and baseline models.

### Why It Matters

A high score can come from a weak strategy. If one class dominates the data, a model can ignore the minority class and still report impressive accuracy.

Consider:

```text
100 games
90 WIN
10 LOSS

Baseline:
Always predict WIN

Accuracy:
90%
```

The baseline is 90% accurate but cannot identify any `LOSS`. Accuracy must be interpreted in context.

### Concepts

- accuracy
- majority and minority classes
- class imbalance
- baseline model
- `DummyClassifier` with the `most_frequent` strategy
- always-majority prediction
- full-dataset class percentage vs test-set baseline accuracy
- fair comparison on the same test rows

A baseline is a simple reference used to ask whether a more advanced model is doing anything useful.

### Mission

Create:

```text
src/week 4/day 1/accuracy-baseline.py
```

Run:

```bash
python "src/week 4/day 1/accuracy-baseline.py"
```

Complete two experiments:

1. Build the artificial `90 WIN / 10 LOSS` example and calculate the accuracy of an always-`WIN` predictor.
2. Reuse the cleaned Week 3 game data and the same train/test split. Use `DummyClassifier(strategy="most_frequent")` as the majority-class baseline, then compare its test accuracy with one Week 3 model on the exact same test rows.

For the cleaned game data, Jerry should independently confirm:

```text
usable sessions: 14
WIN: 9
LOSS: 5
majority-class share: 9 / 14
```

Keep the full-dataset majority share separate from baseline accuracy measured on `y_test`.

### Requirements

- Calculate results from label collections rather than typing answer percentages.
- Display total samples, `WIN` count, `LOSS` count, majority class, and majority-class percentage.
- Build an always-majority prediction collection with the same length as the relevant actual labels.
- Calculate the artificial baseline accuracy.
- Calculate the real game-data baseline accuracy on `y_test`.
- Calculate one Week 3 model's accuracy on the same `y_test`.
- Print raw correct/total counts beside percentages.
- State whether the trained model beats the baseline on this split.
- Warn that a three-game test set cannot support a strong conclusion.

### Hints

- Start with actual labels before creating predictions.
- The majority class can be found from label counts.
- A repeated prediction still needs one element for every actual label.
- `from sklearn.dummy import DummyClassifier` provides a standard majority-class baseline; Jerry still needs to fit, predict, and interpret it.
- Use the same `accuracy_score` function for baseline and trained-model predictions.
- If the baseline and model tie, report the tie honestly.

### Questions Jerry Must Answer

1. What is accuracy?
2. What is class imbalance?
3. What are majority and minority classes?
4. What is a baseline?
5. Why can a 90%-accurate model still be useless?
6. If 95% of samples belong to one class, what accuracy can an always-majority baseline obtain?
7. Why must the baseline and trained model use the same test rows?
8. Did the Week 3 model clearly beat the baseline? How strong is that evidence?

### Done When

- [ ] `accuracy-baseline.py` runs successfully.
- [ ] The artificial always-`WIN` baseline calculates 90% rather than hard-coding it.
- [ ] Real cleaned-label counts are calculated and displayed.
- [ ] Baseline and trained-model test scores use identical rows.
- [ ] Percentages include raw correct/total counts.
- [ ] Jerry can explain why accuracy must be interpreted against class balance and a baseline.
- [ ] The tiny-test-set warning appears beside the comparison.

### Bonus Challenge

Create and compare:

```text
Dataset A: 50 WIN / 50 LOSS
Dataset B: 95 WIN / 5 LOSS
```

Run the same always-`WIN` baseline on both, then explain why the strategy did not improve even though its accuracy changed.

### Suggested Git Commit

```text
Add class imbalance baseline experiment
```

## Tuesday — Precision, Recall, and F1

### Goal

Understand precision, recall, and F1 by asking different questions about `WIN` predictions.

### Why It Matters

Not all mistakes mean the same thing. Accuracy combines every result into one number; precision and recall reveal different error tradeoffs.

Start with intuition:

- **Precision:** Of all sessions the AI predicted as `WIN`, how many were actually `WIN`?
- **Recall:** Of all sessions that were actually `WIN`, how many did the AI identify?
- **F1:** How well does one score balance precision and recall?

Then connect the formulas:

```text
Precision = TP / (TP + FP)
Recall = TP / (TP + FN)
F1 = 2 × (Precision × Recall) / (Precision + Recall)
```

Jerry does not need to memorize the F1 formula. He must understand the questions each metric answers.

### Concepts

- positive class
- true positive, false positive, and false negative
- precision
- recall
- F1 score
- tradeoffs between error types
- explicit `pos_label`
- zero-division behavior on tiny test sets

For this week's game evaluation, define `WIN` as the positive class and keep that choice visible.

### Mission

Create:

```text
src/week 4/day 2/precision-recall-f1.py
```

Run:

```bash
python "src/week 4/day 2/precision-recall-f1.py"
```

First reason through:

```text
AI predicts 10 sessions as WIN.
8 are actually WIN.
2 are actually LOSS.

There are 12 actual WIN sessions in total.
The AI found 8.
```

Then reuse one Week 3 model and calculate:

```python
accuracy_score(...)
precision_score(...)
recall_score(...)
f1_score(...)
```

Configure string labels explicitly so `WIN` is treated as positive. Do not silently rely on a default `pos_label` intended for numeric labels.

Display:

| Metric | Result | Plain-English meaning |
|---|---:|---|
| Accuracy | ? | ? |
| Precision | ? | ? |
| Recall | ? | ? |
| F1 | ? | ? |

Jerry fills every result and explanation.

### Requirements

- Use one Week 3 model and the same prepared train/test data.
- Set the positive class explicitly to `WIN`.
- Calculate all four metrics with scikit-learn.
- Show scores as values between 0 and 1 or percentages, consistently labeled.
- Print the underlying test count.
- Explain each metric in plain English.
- If no positive predictions cause a warning, investigate and document the chosen zero-division behavior.
- Do not claim that one tiny score estimates future performance reliably.
- Use one non-game example to explain why recall or precision might matter more.

### Hints

- Ask “predicted positive” when thinking about precision.
- Ask “actual positive” when thinking about recall.
- Mark `WIN` as positive before counting outcomes.
- With only three test rows, some metric denominators may be very small or zero.
- Medical screening, fraud detection, spam filtering, and security alerts have different error costs.

### Questions Jerry Must Answer

1. What question does precision answer?
2. What question does recall answer?
3. What does F1 try to balance?
4. Why can two models have the same accuracy but different precision and recall?
5. Which label is positive in this experiment, and where is that configured?
6. Why might medical screening prioritize recall?
7. Why might a noisy alert system care strongly about precision?
8. Why are these metrics unstable on Jerry's tiny test set?

### Done When

- [ ] `precision-recall-f1.py` runs successfully.
- [ ] `WIN` is explicitly configured as the positive class.
- [ ] Accuracy, precision, recall, and F1 are calculated.
- [ ] Each displayed metric has Jerry's plain-English explanation.
- [ ] The game scenario can be reasoned through without starting from formulas.
- [ ] Any zero-division warning is understood and handled visibly.
- [ ] Jerry can explain precision vs recall without reading the formulas.

### Bonus Challenge

Create two small fake prediction sets with the same accuracy but different precision and recall. Decide which one is preferable for one clearly defined real-world problem and explain why.

### Suggested Git Commit

```text
Add precision recall and F1 evaluation
```

## Wednesday — Confusion Matrix: Where Did the AI Go Wrong?

### Goal

Interpret true positives, false positives, false negatives, and true negatives using a confusion matrix.

### Why It Matters

A metric summarizes errors. A confusion matrix shows where each prediction went, making it easier to identify the exact mistakes hidden by accuracy.

Use `WIN` as the positive class:

```text
                         Predicted
                      WIN        LOSS

Actual WIN             TP          FN

Actual LOSS            FP          TN
```

In game language:

- **TP:** predicted `WIN`; actual result was `WIN`.
- **FP:** predicted `WIN`; actual result was `LOSS`.
- **FN:** predicted `LOSS`; actual result was `WIN`.
- **TN:** predicted `LOSS`; actual result was `LOSS`.

### Concepts

- confusion matrix
- actual labels on rows
- predicted labels on columns
- TP, FP, FN, and TN
- explicit label order
- error analysis
- context-dependent error cost
- visual display vs interpretation

### Mission

Create:

```text
src/week 4/day 3/confusion-matrix.py
```

Run:

```bash
python "src/week 4/day 3/confusion-matrix.py"
```

Use one Week 3 model to generate predictions and:

1. calculate a confusion matrix;
2. make the label order explicit as `WIN`, then `LOSS`;
3. display the matrix with actual and predicted axes labeled correctly;
4. identify TP, FN, FP, and TN from the matrix;
5. write one sentence about every cell.

Relevant tools:

```python
confusion_matrix(...)
ConfusionMatrixDisplay(...)
```

The real goal is interpretation, not merely generating a plot.

If the real three-row test matrix is nearly empty or trivial, keep it and clearly add a separate synthetic label/prediction example that contains all four outcomes. Label that example as synthetic learning data.

### Requirements

- Generate a matrix from Jerry's real game test predictions.
- Keep `WIN` as positive.
- Specify and display label order so row/column meaning is unambiguous.
- Label axes as actual and predicted.
- Identify all four outcome names and counts.
- Explain each observed error in game language.
- State which cells are empty because of this split, if any.
- Add a synthetic teaching example only when needed; never replace the real result.
- Explain why the worse error depends on the application.

### Hints

- Scikit-learn may sort string labels differently from the teaching diagram unless Jerry supplies the order.
- Verify matrix orientation before naming cells.
- The matrix shape can still be 2 × 2 when one outcome count is zero.
- Compare the sum of all cells with the number of test examples.
- Ask “what was actual?” before “what did the model predict?”

### Questions Jerry Must Answer

1. What is a true positive?
2. What is a false positive?
3. What is a false negative?
4. What is a true negative?
5. Which axis contains actual labels?
6. Why does a confusion matrix reveal more than accuracy?
7. Which error is worse, FP or FN?
8. Which error might matter more in medical screening?
9. Which error might matter more in a spam filter?
10. Why is the real game confusion matrix weak evidence?

### Done When

- [ ] `confusion-matrix.py` runs successfully.
- [ ] The real test matrix is generated and visibly labeled.
- [ ] The four cells are mapped correctly to TP, FN, FP, and TN.
- [ ] All cell counts sum to the test-set size.
- [ ] Jerry explains each nonzero cell in game language.
- [ ] Any synthetic example is clearly separated from real results.
- [ ] Jerry can interpret a confusion matrix verbally without relying on accuracy alone.

### Bonus Challenge

Create a second real confusion matrix for another Week 3 model. Ask:

> Same accuracy, but the same mistakes?

Compare error types without automatically declaring a winner.

### Suggested Git Commit

```text
Add confusion matrix analysis
```

## Thursday — Overfitting: When AI Memorizes Instead of Learns

### Goal

Understand training score, test score, generalization, overfitting, and underfitting, with the main focus on overfitting.

### Why It Matters

A model can look perfect on examples it studied and still fail on new examples.

```text
Training set
↓
Model memorizes details
↓
Training accuracy becomes very high

Unseen test data
↓
Model struggles
↓
Test accuracy is lower
```

Memorizing answers to a practice test is not the same as understanding the subject.

### Concepts

- training accuracy
- test accuracy
- generalization
- model complexity
- overfitting
- underfitting
- good fit
- Decision Tree constraints
- honest synthetic demonstration

Intuition:

```text
Underfitting:
model is too simple to learn a useful pattern

Good fit:
model learns a useful pattern that transfers

Overfitting:
model follows training details too closely
```

### Mission

Create:

```text
src/week 4/day 4/overfitting-demo.py
```

Run:

```bash
python "src/week 4/day 4/overfitting-demo.py"
```

Using Decision Tree, compare at least:

```text
Flexible Tree
vs
Constrained Tree
```

Parameters Jerry may investigate:

```text
max_depth
min_samples_split
min_samples_leaf
```

For each tree, calculate both training and test accuracy:

| Model | Training Accuracy | Test Accuracy | Jerry's Interpretation |
|---|---:|---:|---|
| Flexible Tree | ? | ? | ? |
| Constrained Tree | ? | ? | ? |

The point is not hyperparameter optimization. The point is to observe how model complexity can improve training performance while harming performance on unseen data.

The tiny game dataset may not show a clean pattern. If it does not, keep and report the real experiment, then use a small labeled synthetic dataset specifically to make overfitting visible. Clearly identify which result is synthetic. Never invent favorable metrics.

### Requirements

- Reuse the Week 3 game data and split for the first experiment.
- Train at least one flexible and one constrained Decision Tree.
- Calculate training and test accuracy for both.
- Put all four scores in a comparison table.
- Describe the size and direction of each train/test gap.
- Explain whether the real data shows clear overfitting or is inconclusive.
- If needed, add a clearly labeled synthetic demonstration.
- Explain underfitting and good fit at an intuitive level.
- Do not perform GridSearchCV or broad hyperparameter optimization.

### Hints

- A model's `score` can be calculated separately on training and test data.
- Compare the same model on two datasets before comparing models.
- An unconstrained tree can keep splitting until it fits training details.
- Reducing `max_depth` limits how many levels the tree can learn.
- A tiny test set can hide or exaggerate the train/test gap.

### Questions Jerry Must Answer

1. What is overfitting?
2. What is underfitting?
3. What does generalization mean?
4. Why can training accuracy be higher than test accuracy?
5. Is 100% training accuracy always good?
6. How might limiting tree depth reduce overfitting?
7. Did the real game dataset show a clear pattern or an inconclusive one?
8. Why must synthetic results be labeled honestly?

### Done When

- [ ] `overfitting-demo.py` runs successfully.
- [ ] Flexible and constrained trees are compared.
- [ ] Both training and test scores are reported for each model.
- [ ] Jerry writes one interpretation of the train/test gap.
- [ ] Real and synthetic results are clearly distinguished.
- [ ] No metrics are fabricated to create a cleaner story.
- [ ] Jerry can explain that a useful model must work on unseen data.

### Bonus Challenge

Plot:

```text
tree depth
vs
training accuracy
vs
test accuracy
```

If the game data produces a misleading graph, use clearly labeled synthetic data and explain why.

### Suggested Git Commit

```text
Demonstrate model overfitting
```

## Friday — Game AI Friday #3: Which AI Should I Trust?

### Goal

Build **Jerry Game AI v2 — Model Trust Report**, comparing Logistic Regression, Decision Tree, Random Forest, and KNN with multiple metrics and context.

### Why It Matters

The Game AI progression is now:

```text
Game AI Friday #1
Rule-Based Agent
Jerry writes the rules

↓

Game AI Friday #2
Machine Learning Predictor
Model learns from examples

↓

Game AI Friday #3
Model Evaluation
Which model should Jerry trust?
```

“Highest accuracy wins” is too weak. A responsible choice considers metric meaning, error types, overfitting risk, model simplicity, and evidence quality.

### Concepts

- multi-metric model comparison
- K-Nearest Neighbors (KNN)
- `k` as the number of neighbors
- nearby examples and neighbor voting
- model selection with evidence
- accuracy, precision, recall, and F1 tradeoffs
- model simplicity and overfitting risk
- “no clear winner” as a valid conclusion
- feature scaling for distance-based models
- `StandardScaler` with `Pipeline` or `make_pipeline`

KNN intuition:

```text
New sample
↓
Look at nearby examples
↓
Neighbors vote
↓
Prediction
```

Jerry's features use very different numeric ranges: kills may be in the tens, accuracy may be a percentage, and `reaction_time_ms` may be in the hundreds. KNN measures distance, so a large-number feature can dominate unless the inputs are scaled.

Use a simple scikit-learn pipeline:

```text
StandardScaler
↓
KNeighborsClassifier
```

Prefer `Pipeline` or `make_pipeline` so scaling is learned from the training data and applied consistently to test data. Do not study distance formulas, the curse of dimensionality, KD-trees, or advanced scaling theory this week.

### Mission

Create:

```text
src/week 4/day 5/game-ai-model-trust.py
```

Run:

```bash
python "src/week 4/day 5/game-ai-model-trust.py"
```

Use:

```python
LogisticRegression
DecisionTreeClassifier
RandomForestClassifier
KNeighborsClassifier
StandardScaler
Pipeline or make_pipeline
```

Train all four models with the same prepared data and split. Put KNN behind `StandardScaler` in a pipeline; do not use raw, unscaled KNN for the model comparison. Evaluate `WIN` as positive and first complete this table:

| Model | Accuracy | Precision | Recall | F1 | Notes |
|---|---:|---:|---:|---:|---|
| Logistic Regression | ? | ? | ? | ? | Jerry completes |
| Decision Tree | ? | ? | ? | ? | Jerry completes |
| Random Forest | ? | ? | ? | ? | Jerry completes |
| Scaled KNN | ? | ? | ? | ? | Jerry completes |

Then generate a trust report shaped like:

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

Jerry may select one model or report `No clear winner`. The reason must cite metrics, observed errors, overfitting risk, dataset size, and model simplicity where relevant.

Optionally extend the Week 3 input mode so a user can enter one completed session and receive a model prediction plus the selected model's evaluation summary. Make clear that aggregate test metrics do not guarantee confidence in an individual prediction.

### Requirements

- Reuse the same five features, missing-value rule, and train/test split.
- Train all four required classifiers.
- Introduce KNN only through neighbor voting and `k`.
- Use `StandardScaler` and `Pipeline` or `make_pipeline` for KNN.
- Fit preprocessing only through the training pipeline; do not scale the complete dataset before splitting.
- Use an intentional `k` that is valid for the training-set size.
- Calculate accuracy, precision, recall, and F1 for each model.
- Keep `WIN` explicitly configured as positive.
- Record raw correct/test counts and at least one error observation.
- Include training vs test evidence where it helps identify overfitting.
- Do not automatically select the highest-accuracy model.
- Permit and explain `No clear winner`.
- Print the tiny-dataset warning in the report.
- Write a strength, weakness, risk, and next improvement for the selected model or overall experiment.

### Hints

- Store model names and model objects in a collection, then apply the same fit/predict/evaluate steps.
- Use the same test rows so the introductory comparison is fair.
- On three test rows, scores can only change in large jumps.
- Build KNN as one pipeline object so `fit()` learns scaling from `X_train` and `predict()` applies the same transformation to `X_test`.
- Model-specific preprocessing is part of a fair comparison when an algorithm requires it; fairness still requires the same train/test rows and evaluation rules.
- Scientific honesty can be a stronger result than naming a winner.

### Questions Jerry Must Answer

1. Which model had the highest accuracy?
2. Which model had the highest precision?
3. Which model had the highest recall?
4. Which model had the highest F1?
5. Were all four answers the same model?
6. If not, which model would you choose, or is there no clear winner?
7. Why is “highest accuracy wins” a weak rule?
8. Can you confidently trust any model yet?
9. What does KNN do at a high level?
10. Why can different feature scales distort KNN, and how does the scaling pipeline help?
11. What additional data would make the evaluation more trustworthy?
12. Which model would you trust most, and what evidence supports that choice?

### Done When

- [ ] `game-ai-model-trust.py` runs successfully.
- [ ] All four models use the same data split.
- [ ] KNN uses a `StandardScaler` pipeline fitted on training data.
- [ ] The report contains accuracy, precision, recall, and F1 for every model.
- [ ] `WIN` is the explicit positive class.
- [ ] The selected model or `No clear winner` is supported with evidence.
- [ ] The report includes strength, weakness, risk, and next improvement.
- [ ] Tiny-data warnings are visible beside the metrics.
- [ ] Jerry can explain why multiple metrics and context are required.

### Bonus Challenge

Add a compact trust report:

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

Jerry fills the model and reasoning. Do not copy the example conclusion automatically.

### Suggested Git Commit

```text
Add Game AI model trust report
```

## Integrated Week 4 Notebook

Create:

```text
notebooks/week4-model-evaluation.ipynb
```

Include these 20 sections:

1. Load the dataset.
2. Prepare `X` and `y`.
3. Create the train/test split.
4. Examine class distribution.
5. Build the baseline classifier.
6. Calculate accuracy.
7. Calculate precision.
8. Calculate recall.
9. Calculate F1.
10. Generate and interpret a confusion matrix.
11. Compare training and test scores.
12. Run the overfitting experiment.
13. Evaluate Logistic Regression.
14. Evaluate Decision Tree.
15. Evaluate Random Forest.
16. Evaluate scaled KNN.
17. Build the model comparison table.
18. Write model-choice reasoning.
19. Explain dataset limitations.
20. Write “What I Learned” in Jerry's own words.

Use Markdown prompts that Jerry must complete:

- Which metric changed your opinion about a model?
- Which exact mistakes did the confusion matrix reveal?
- What evidence suggests overfitting, underfitting, or an inconclusive result?
- Is there enough evidence to choose a model confidently?
- What new data would reduce uncertainty?
- Why is the system still completed-session classification?

Do not let AI write Jerry's final conclusions.

A useful notebook commit is:

```text
Complete Week 4 evaluation notebook
```

## README Requirement

The coding repository README is still mainly focused on Week 1. Update it to summarize Weeks 1–4:

```text
Week 1 — Python + Game Statistics
Week 2 — NumPy / Pandas / Visualization
Week 3 — First Machine Learning Models
Week 4 — Model Evaluation
```

The Week 4 summary must include:

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

Jerry should add a short summary in his own words and state the dataset's biggest limitation.

Suggested commit:

```text
Update README through Week 4
```

## Git Hygiene Checkpoint

Before the Saturday demo:

```bash
git status
git diff --check
git log --oneline -10
git ls-files ".venv/*"
git ls-files "*main.java"
```

Confirm:

- Week 4 commits contain only related source, notebook, README, and intentional output files.
- No virtual environment is tracked.
- No `__pycache__`, IDE output, unrelated Java, random Hello World, or temporary file remains.
- No credentials or secrets appear.
- Commit messages describe concrete work.

Avoid vague commits such as `done`, `week4`, `final`, `stuff`, or `update`.

## Why This Matters Later

```text
Today
training score
test score
overfitting
generalization

Later in PyTorch and JerryMind
training loss
validation loss
model size
generalization
overfitting
```

The models will become larger and the measurements will change, but the core question remains: did the model learn something that works beyond its training examples?

Do not implement PyTorch, Transformers, MiniMind, or JerryMind during Week 4. This section is a bridge, not an assignment.

## Game AI Long-Term Motivation

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

Future
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

The future stages are motivation, not Week 4 implementation work.

## Saturday Demo

From `AI-study/game-statistics-analyzer/`, Jerry should demonstrate:

1. the class-imbalance example;
2. the majority-class baseline model;
3. accuracy, precision, recall, and F1 with `WIN` as positive;
4. a correctly labeled confusion matrix and each error type;
5. flexible vs constrained Decision Tree training/test results;
6. a four-model comparison including scaled KNN;
7. Jerry Game AI v2 Model Trust Report;
8. the integrated notebook and README update;
9. clean, meaningful Week 4 Git history.

Jerry should answer without reading a prepared script:

1. Why can high accuracy be misleading?
2. What is the difference between precision and recall?
3. What did the confusion matrix reveal?
4. What evidence might indicate overfitting?
5. Which model would you trust, or why is there no clear winner?
6. What would make the experiment more trustworthy?
7. How will these ideas matter when training neural networks later?

## Parent Review Questions

1. **Why can a 90%-accurate model still be bad?**

   Expected concept: class imbalance may let an always-majority baseline achieve a high score without useful minority-class predictions.

2. **What is a baseline model?**

   Expected concept: it is a simple reference used to judge whether a more complex model adds useful value.

3. **Explain precision without using the formula.**

   Expected concept: of the games predicted as `WIN`, how many were actually `WIN`?

4. **Explain recall without using the formula.**

   Expected concept: of all actual `WIN` games, how many did the model find?

5. **What does F1 tell you?**

   Expected concept: it combines precision and recall when both matter.

6. **Explain TP, FP, FN, and TN.**

   Jerry should explain each in game language with `WIN` as positive.

7. **What is overfitting?**

   Expected concept: the model follows training data too closely and performs worse on unseen data.

8. **Why can training accuracy be higher than test accuracy?**

9. **All your models got very high scores. Does that prove they are good?**

   Expected: no, because the dataset and test set are extremely small.

10. **What would make this experiment more trustworthy?**

   Expected ideas: more data, more diverse data, more unseen test examples, repeated evaluation, and better features.

11. **What is the biggest weakness of the current Game AI?**

    Expected concept: it uses too little data, and many inputs describe a completed game rather than a live game state.

12. **Why does KNN need special care with feature scaling?**

    Expected concept: KNN uses distance, so features with larger numeric ranges can dominate unless inputs are scaled.

## Weekly Score

| Category | Points |
|---|---:|
| Accuracy + baseline understanding | 10 |
| Class imbalance | 10 |
| Precision / Recall | 15 |
| F1 understanding | 10 |
| Confusion Matrix | 10 |
| Overfitting / Generalization | 15 |
| Model Comparison | 10 |
| Game AI Friday | 10 |
| Explain the Code | 5 |
| Git + Documentation | 5 |
| **Total** | **100** |

Scoring does not replace understanding. Fix missing items, make another meaningful commit, and repeat the relevant part of the demo instead of restarting the entire week.

## Week 4 Success Criteria

Week 4 is successful if Jerry can truthfully explain:

> A model's accuracy alone does not tell me whether it is good.

and:

> I need to examine different error types, multiple metrics, test performance, dataset size, and overfitting before deciding whether I trust a model.

Jerry should understand:

```text
Model
↓
Prediction
↓
Evaluation
↓
Error Analysis
↓
Generalization
↓
Trust
```

## Bridge to Week 5

```text
Week 3:
Can AI predict?

Week 4:
Can we trust the prediction?

Week 5:
Can we build a better AI system from what we learned?
```

Week 5 will turn the lessons into a more complete applied ML/Game AI project. Do not design or implement Week 5 during this week's work.

## Controlled Scope

Use Python, Pandas, NumPy, scikit-learn, Matplotlib when useful, and Jupyter Notebook.

Required model classes:

```text
LogisticRegression
DecisionTreeClassifier
RandomForestClassifier
KNeighborsClassifier
```

Required evaluation tools:

```text
accuracy_score
precision_score
recall_score
f1_score
confusion_matrix
ConfusionMatrixDisplay
```

Do not introduce ROC-AUC, PR-AUC, calibration, in-depth cross-validation, hyperparameter optimization, GridSearchCV, Bayesian optimization, PyTorch, neural networks, Transformers, MiniMind/JerryMind implementation, or reinforcement-learning implementation.

Cross-validation may be mentioned as a future way to repeat evaluation, but it is not a Week 4 requirement.
