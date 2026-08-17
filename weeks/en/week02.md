# Week 2: Game Data Intelligence + First Game Agent

[中文版本](../zh-CN/week02.md)

## Weekly Mission

Turn the Week 1 game-session dataset into evidence: first with NumPy, then Pandas, then charts. Finish the week by building a small rule-based game agent that follows the **Observe → Decide → Act** model.

Continue working in the shared project:

```text
projects/game-statistics-analyzer/
```

Use the existing dataset:

```text
projects/game-statistics-analyzer/data/game_sessions.csv
```

Do not create separate English and Chinese source code. All file names, variable names, function names, terminal output, and source-code comments should be in English.

By Saturday, the new student-created files should look approximately like this:

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

Spend 60–90 minutes per weekday, with roughly 30% learning and 70% building. AI may explain concepts, provide hints, debug a student attempt, or review code. It must not provide the finished exercises. Any important code Jerry cannot explain is not complete.

## Week 2 Setup

From the repository root, activate the Week 1 virtual environment.

PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

macOS or Linux:

```bash
source .venv/bin/activate
```

Install the Week 2 libraries:

```bash
python -m pip install numpy pandas matplotlib
```

Create or update:

```text
projects/game-statistics-analyzer/requirements.txt
```

Record the three direct dependencies: `numpy`, `pandas`, and `matplotlib`. Do not add a second copy of the CSV.

## Monday — NumPy Statistics

### Goal

Use NumPy arrays to calculate and compare statistics from real game-session columns.

### Why It Matters

AI and data systems represent numeric information as arrays. Vectorized operations let you analyze an entire column consistently without manually updating totals inside a loop.

### Concepts

- NumPy arrays and `dtype`
- shape and indexing
- `mean`, `median`, `min`, `max`, and `std`
- population standard deviation with NumPy's default `ddof=0`
- Boolean masks and filtering
- vectorized operations compared with Python loops

### Mission

Create:

```text
projects/game-statistics-analyzer/src/numpy_stats.py
```

Read the existing CSV with Python's standard `csv` module, then convert the `kills`, `deaths`, `duration_minutes`, and `reaction_time_ms` columns into NumPy arrays.

Use NumPy to answer:

1. What are the mean and median kills?
2. What are the minimum and maximum reaction times?
3. What is the population standard deviation of kills?
4. Which sessions have at least 15 kills?
5. How many sessions have reaction time below 240 ms?

Run from the repository root:

```bash
python projects/game-statistics-analyzer/src/numpy_stats.py
```

### Requirements

- Use the existing `game_sessions.csv`; do not copy or edit it.
- Use at least four NumPy arrays.
- Use NumPy functions for the requested statistics.
- Use at least two Boolean masks.
- Include `game_id` in filtered output so results can be checked against the CSV.
- Format displayed averages to two decimal places.
- Confirm the kills array contains 15 values and its mean is `14.00`.

### Hints

- The `csv` module returns strings, so convert numeric values before building arrays.
- A comparison such as “values greater than or equal to a threshold” produces a Boolean mask.
- Apply the same mask to the `game_id` array and the numeric array to keep rows aligned.
- Print `array.shape` and `array.dtype` while debugging.

### Done When

- [ ] `numpy_stats.py` runs from the repository root without an error.
- [ ] All five questions produce calculated answers.
- [ ] The kills mean is `14.00`.
- [ ] Filtered sessions include their `game_id` values.
- [ ] No requested statistic is hard-coded.
- [ ] Jerry can explain what a Boolean mask contains and why array shapes must match.

### Bonus Challenge

Normalize the kills values with:

```text
(value - mean) / standard_deviation
```

Then identify the session with the largest positive normalized score. Derive the NumPy expression yourself.

### Git Commit Suggestion

```text
week2 monday: analyze game statistics with numpy
```

## Tuesday — Pandas Fundamentals

### Goal

Load the same CSV into a Pandas DataFrame and inspect its structure, types, rows, columns, and descriptive statistics.

### Why It Matters

Pandas provides labeled tables that make data inspection and feature analysis easier. Before training a model, an engineer must understand what each row and column represents.

### Concepts

- DataFrame, row, column, and index
- `read_csv`
- `head`, `tail`, `shape`, and `columns`
- `info` and `dtypes`
- `describe`
- selecting one column or several columns
- `loc` and `iloc`

### Mission

Create:

```text
projects/game-statistics-analyzer/src/pandas_explorer.py
```

Load `data/game_sessions.csv` into a DataFrame and produce a compact inspection report that answers:

1. How many rows and columns are present?
2. What are the column names and inferred data types?
3. Which column contains a missing value?
4. What do the first five and last three sessions look like?
5. What summary statistics does Pandas calculate for numeric columns?
6. How can you select only `game_id`, `kills`, `accuracy`, and `result`?

Run from the repository root:

```bash
python projects/game-statistics-analyzer/src/pandas_explorer.py
```

### Requirements

- Use `pandas.read_csv`.
- Print the DataFrame shape; the expected shape is `(15, 8)`.
- Use `head`, `tail`, `info`, and `describe`.
- Display the column names and data types.
- Select one Series and one smaller DataFrame.
- Demonstrate one `loc` selection and one `iloc` selection.
- Detect the one missing `accuracy` value without changing the source CSV.

### Hints

- A single bracket selection usually returns a Series; a list of column names returns a DataFrame.
- `info()` prints its report instead of returning a ready-to-print table.
- `isna().sum()` can count missing values by column.
- Use a path based on the script location so the command works regardless of the current terminal directory.

### Done When

- [ ] `pandas_explorer.py` runs without an error.
- [ ] The report shows 15 rows, 8 columns, and one missing accuracy.
- [ ] Jerry can point to the DataFrame index, columns, and values.
- [ ] Series and DataFrame selections are both demonstrated.
- [ ] `loc` and `iloc` are used correctly and explained.
- [ ] No calculations are copied from Week 1 output.

### Bonus Challenge

Set `game_id` as the DataFrame index and retrieve `G008` by label. Explain when a meaningful index helps.

### Git Commit Suggestion

```text
week2 tuesday: explore game sessions with pandas
```

## Wednesday — Cleaning, Filtering, GroupBy, and WIN vs LOSS

### Goal

Clean the dataset deliberately and compare winning sessions with losing sessions using filters and grouped statistics.

### Why It Matters

Real data is incomplete and useful questions often compare groups. A model or recommendation built on misunderstood missing values or unfair group comparisons can be confidently wrong.

### Concepts

- missing values and `NaN`
- numeric conversion with invalid-value handling
- `isna`, `dropna`, and `fillna`
- filtering with multiple conditions
- sorting with `sort_values`
- `groupby` and aggregation
- preserving raw data while creating a cleaned DataFrame

### Mission

Create:

```text
projects/game-statistics-analyzer/src/session_analysis.py
```

Load the CSV and build a cleaned analysis DataFrame. Keep the original CSV unchanged.

Answer:

1. Which row has missing accuracy?
2. Which sessions have at least 15 kills and accuracy of at least 40?
3. What are the five fastest reaction-time sessions?
4. For `WIN` and `LOSS`, what are the game count and average kills, deaths, accuracy, and reaction time?
5. How do wins and losses differ based on this small dataset?

Write at least three evidence-based observations. Each observation must cite a number from the grouped or filtered result.

Run from the repository root:

```bash
python projects/game-statistics-analyzer/src/session_analysis.py
```

### Requirements

- Convert `accuracy` to numeric with invalid values represented as `NaN`.
- Count missing values before cleaning.
- Do not replace the missing accuracy with zero.
- Choose and explain either:
  - exclude the missing value only from accuracy calculations; or
  - fill it with a justified statistic while retaining a missing-value indicator.
- Use at least two filters, including one with multiple conditions.
- Sort at least one result.
- Use `groupby("result")` with multiple aggregations.
- Confirm the grouped counts are 9 `WIN` and 6 `LOSS`.
- Keep claims modest: 15 sessions can suggest a pattern, not prove a general rule.

### Hints

- Use parentheses around each condition before combining them with `&` or `|`.
- Different columns can use different aggregation functions.
- Pandas ignores `NaN` in many numeric averages by default; verify rather than assume.
- A cleaned copy protects the original DataFrame while you experiment.

### Done When

- [ ] `session_analysis.py` runs without warnings caused by accidental chained assignment.
- [ ] The missing accuracy row is identified and handled by a documented rule.
- [ ] Filtered and sorted results include `game_id`.
- [ ] The grouped table contains both `WIN` and `LOSS`.
- [ ] Group counts are 9 wins and 6 losses.
- [ ] At least three written observations cite calculated evidence.
- [ ] Jerry can explain why correlation or group difference does not prove causation.

### Bonus Challenge

Create a `kd_ratio` column while handling zero deaths safely, then compare average K/D for wins and losses.

### Git Commit Suggestion

```text
week2 wednesday: compare winning and losing sessions
```

## Thursday — Matplotlib Visualization + Relationship Exploration

### Goal

Create readable charts and use a basic correlation calculation to explore relationships between game-performance variables.

### Why It Matters

Charts reveal distributions, outliers, and possible relationships that are hard to notice in a table. Visual evidence also makes technical results easier to explain to another person.

### Concepts

- figure and axes
- line/bar charts and scatter plots
- titles, axis labels, legends, and grids
- `tight_layout` and `savefig`
- positive, negative, weak, and strong relationships
- Pearson correlation
- correlation is not causation

### Mission

Create:

```text
projects/game-statistics-analyzer/src/visualize_sessions.py
```

Use Pandas and Matplotlib to generate:

1. `kills_by_game.png`: kills for each `game_id`.
2. `reaction_time_vs_accuracy.png`: a scatter plot of reaction time and accuracy, excluding the missing accuracy only from this chart.
3. `win_loss_comparison.png`: a grouped comparison of at least two average metrics for `WIN` and `LOSS`.

Save all charts under:

```text
projects/game-statistics-analyzer/output/charts/
```

Also calculate a numeric correlation matrix for:

```text
duration_minutes
kills
deaths
accuracy
reaction_time_ms
```

Choose one correlation pair and write a two-sentence interpretation: what the sign and size suggest, and why the dataset is too small to claim causation.

Run from the repository root:

```bash
python projects/game-statistics-analyzer/src/visualize_sessions.py
```

### Requirements

- Use the existing CSV and do not modify it.
- Create exactly the three required PNG files, with non-empty image content.
- Every chart needs a descriptive title and labeled axes.
- Add a legend when more than one series appears.
- Make all 15 game IDs readable on the kills chart.
- Handle the missing accuracy without converting it to zero.
- Print the selected correlation coefficient to a sensible number of decimal places.
- Write at least three chart-based observations.

### Hints

- Create `output/charts/` before saving if it does not exist.
- Rotate crowded x-axis labels instead of making the font tiny.
- Call `tight_layout()` before `savefig(...)`.
- Build the scatter plot from rows where both selected values are present.
- A coefficient near `1` or `-1` shows a stronger linear relationship; a coefficient near `0` shows a weaker linear relationship.

### Done When

- [ ] `visualize_sessions.py` runs and creates all three PNG files.
- [ ] The files open and show data rather than blank figures.
- [ ] Titles, labels, game IDs, and legends are readable.
- [ ] The correlation matrix uses the five required numeric columns.
- [ ] One relationship is interpreted without claiming causation.
- [ ] Three observations refer to visible chart evidence.
- [ ] Jerry can explain why a scatter plot is useful for two numeric variables.

### Bonus Challenge

Highlight `G008` and `G014` on one chart and annotate why they mattered in Week 1.

### Git Commit Suggestion

```text
week2 thursday: visualize game performance relationships
```

## Friday — Game AI Friday #1: Rule-Based Game Agent

### Goal

Build a small game agent that observes a game state, decides with explicit rules, and returns one action.

### Why It Matters

An AI agent is not only a machine-learning model. At its simplest, an agent receives information about an environment, applies a policy, and acts. Clear rule-based agents make the **Observe → Decide → Act** loop visible before later weeks introduce learned behavior.

### Concepts

- agent, environment, state, action, and policy
- Observe → Decide → Act
- deterministic rule-based decisions
- rule priority
- pure decision functions
- test cases and edge cases

### Mission

Create:

```text
projects/game-statistics-analyzer/src/game_agent.py
```

Represent a game state with:

```text
player_x
goal_x
obstacle_ahead
```

Create a student-written decision function with a responsibility similar to:

```text
choose_action(game_state)
```

It must return exactly one allowed action:

```text
LEFT
RIGHT
JUMP
STAY
```

Design a rule priority that passes these observable scenarios:

| Scenario | player_x | goal_x | obstacle_ahead | Expected action |
|---|---:|---:|---|---|
| Goal is to the right | 2 | 10 | false | `RIGHT` |
| Goal is to the left | 12 | 4 | false | `LEFT` |
| Player reached goal | 7 | 7 | false | `STAY` |
| Obstacle blocks movement | 3 | 9 | true | `JUMP` |

Run from the repository root:

```bash
python projects/game-statistics-analyzer/src/game_agent.py
```

### Requirements

- Separate state observation, decision logic, and displayed action into understandable parts.
- Use English identifiers and comments.
- Keep the decision function independent from `input()` and printing where practical.
- Return only one of the four allowed actions.
- Give obstacle handling an intentional priority and explain it.
- Run at least the four required scenarios in a small test loop.
- Print each observed state and selected action.
- Add one edge case designed by Jerry.
- Do not use NumPy, Pandas, machine learning, or an LLM for the decision.

### Hints

- Start by writing the four scenarios before writing decision rules.
- Ask which condition should be checked first when two rules could apply.
- A function that receives a state and returns an action is easier to test than one that reads keyboard input internally.
- Use a collection of test states so one loop can exercise the policy.

### Done When

- [ ] `game_agent.py` runs without interaction and tests at least five states.
- [ ] All four required scenarios return the expected actions.
- [ ] Every returned value belongs to the allowed action set.
- [ ] Observe, Decide, and Act can be pointed out in the program.
- [ ] Changing a test state's values can change the action without editing the policy.
- [ ] Jerry can explain rule order and one possible weakness of the agent.

### Bonus Challenge

Add `enemy_nearby` to the observed state and introduce one new action or priority rule. First write the expected test scenario, then change the policy.

### Git Commit Suggestion

```text
week2 friday: build first rule based game agent
```

## Saturday Demo

Demonstrate the week from the repository root:

1. Show that all four data scripts read the same `data/game_sessions.csv`.
2. Run the NumPy report and explain one Boolean mask.
3. Run the Pandas analysis and compare one `WIN` metric with one `LOSS` metric.
4. Open the three saved charts and explain one relationship carefully.
5. Run the game agent's required scenarios and one student-designed edge case.
6. Show the five Week 2 commits with:

   ```bash
   git log --oneline -5
   ```

Without reading a prepared script, Jerry should answer:

1. When would you choose a NumPy array instead of a Pandas DataFrame?
2. How was the missing accuracy handled, and why?
3. What evidence differs between wins and losses?
4. What does one chart suggest, and what does it not prove?
5. Where are Observe, Decide, and Act in the game agent?
6. How is a rule-based agent different from a trained machine-learning model?

## Parent Review Questions

1. Can Jerry run each Week 2 script without help?
2. Can Jerry show that every data script uses the original CSV?
3. Can Jerry explain one NumPy operation and one Pandas operation?
4. Can Jerry explain the missing-value decision?
5. Can Jerry use a chart to support a specific observation?
6. Can Jerry change a game state and predict the agent's action before running it?
7. Can Jerry explain one bug or confusing result from this week?
8. Do the Git commits show progress across all five days?

If an item is missing, fix that item, commit again, and repeat the relevant part of the demo. Do not restart the entire week.
