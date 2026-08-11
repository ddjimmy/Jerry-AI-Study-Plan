# Week 1: Python for AI — Game Statistics Analyzer

[中文版本](../zh-CN/week01.md)

## Weekly Mission

Build **Game Statistics Analyzer v1**, a command-line program that reads game-session data from CSV, calculates useful statistics, prints a clear report, and writes a JSON summary.

Work for 60–90 minutes each weekday. Spend roughly 30% of each session learning and 70% building, testing, debugging, and committing an artifact. AI may explain, hint, debug a student attempt, or review code, but it must not write the complete solution. If you cannot explain important code, the task is not complete.

Start with the [project README](../../projects/game-statistics-analyzer/README.md) and work inside:

```text
projects/game-statistics-analyzer/
```

By the end of the week, the project should look approximately like this:

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

Do not copy a finished analyzer from AI or another source. Write it in small steps, run it often, and keep notes about bugs and decisions.

## Day 1 — Development Environment + Git

### Mission

Verify that Python, Git, VS Code, the repository, and a local virtual environment work together. Produce and run the first small Python file.

### Suggested Timebox

- 15–20 minutes: verify tools and understand the commands.
- 35–50 minutes: create the environment and script, then run and debug it.
- 10–15 minutes: explain the workflow, review the diff, and commit.

### Learn

- The difference between Git, a version-control tool, and GitHub, a repository hosting service.
- What happens when Python runs a `.py` file.
- Why a virtual environment isolates one project's Python packages.

### Build

1. Open a terminal and verify the tools:

   ```bash
   python --version
   git --version
   ```

2. Clone the repository if it is not already on the computer:

   ```bash
   git clone https://github.com/ddjimmy/Jerry-AI-Study-Plan.git
   cd Jerry-AI-Study-Plan
   ```

3. From the repository root, create a virtual environment:

   ```bash
   python -m venv .venv
   ```

4. Activate it in PowerShell:

   ```powershell
   .\.venv\Scripts\Activate.ps1
   ```

   On macOS or Linux, use:

   ```bash
   source .venv/bin/activate
   ```

5. Create `projects/game-statistics-analyzer/src/hello_ai.py`. It only needs to print a short message proving that Python ran the file.

6. Run it from the repository root:

   ```bash
   python projects/game-statistics-analyzer/src/hello_ai.py
   ```

7. Inspect the change before committing:

   ```bash
   git status
   git diff
   ```

### Required File

```text
projects/game-statistics-analyzer/src/hello_ai.py
```

### Expected Evidence

- Both version commands print installed versions.
- The terminal prompt shows the activated `.venv`.
- Running `hello_ai.py` prints the message once without an error.
- `git status` shows the new script.

### Acceptance Criteria

- [ ] `python --version` and `git --version` succeed.
- [ ] `.venv` exists locally and is not committed.
- [ ] `hello_ai.py` contains a small student-written `print(...)` statement.
- [ ] The script runs from the repository root with exit code 0.
- [ ] Jerry can explain all three concepts below without reading a prepared answer.

### Explain It

1. How are Git and GitHub different?
2. What does `python path/to/file.py` ask the computer to do?
3. Why do developers use a separate virtual environment for a project?

### Suggested Git Commit

```text
week1 day1: verify Python development environment
```

### Optional Bonus

Run `python -c "import sys; print(sys.executable)"` before and after activation and explain why the paths differ.

## Day 2 — Core Python Data Structures

### Mission

Build an in-memory player statistics analyzer using Python's core data structures and control flow.

### Suggested Timebox

- 20 minutes: review lists, dictionaries, loops, conditions, and division.
- 45–55 minutes: create data, calculate statistics, and debug edge cases.
- 10–15 minutes: explain choices and commit.

### Learn

- A dictionary groups named values that describe one player.
- A list stores many player dictionaries in an ordered collection.
- A `for` loop repeats work for each player.
- An `if` condition chooses what happens for a specific case.
- Division by zero must be handled before calculating a K/D ratio.

### Build

Create `projects/game-statistics-analyzer/src/player_stats.py`.

In the script:

1. Create a list containing at least 10 player dictionaries.
2. Give every player these fields:

   ```text
   name
   kills
   deaths
   accuracy
   ```

3. Give at least one player `deaths = 0`.
4. Use a loop and an `if` condition to calculate a K/D ratio for every player without crashing.
5. Choose and document a clear zero-death policy, such as displaying `Perfect` or treating the ratio as infinity for ranking.
6. Calculate and print:
   - every player's K/D ratio;
   - the player with the highest K/D;
   - the player with the lowest K/D;
   - average kills;
   - a leaderboard ordered from highest to lowest K/D.
7. Run the script from the repository root:

   ```bash
   python projects/game-statistics-analyzer/src/player_stats.py
   ```

### Required File

```text
projects/game-statistics-analyzer/src/player_stats.py
```

### Required Python Features

- `list`
- `dict`
- `for` loop
- `if` condition

### Acceptance Criteria

- [ ] At least 10 players are analyzed.
- [ ] Every player has `name`, `kills`, `deaths`, and `accuracy` data.
- [ ] At least one player has zero deaths and the program does not raise `ZeroDivisionError`.
- [ ] Every required statistic and a descending K/D leaderboard are printed.
- [ ] The average is calculated from the data, not typed in as an answer.
- [ ] The zero-death policy is visible in a short code comment or the project notes.
- [ ] Jerry can explain the data structures and control flow.

### Explain It

1. Why is a dictionary appropriate for one player?
2. Why is a list appropriate for many players?
3. Why must division by zero be handled before calculating K/D?
4. What is the difference between `for` and `if`?

### Suggested Git Commit

```text
week1 day2: build player statistics analyzer
```

### Optional Bonus

Also rank players by accuracy, then explain why an accuracy leaderboard answers a different question from a K/D leaderboard.

## Day 3 — Functions and Refactoring

### Mission

Refactor Day 2 so the calculation, selection, averaging, and display responsibilities live in reusable functions.

### Suggested Timebox

- 15–20 minutes: learn parameters, return values, and local variables.
- 45–55 minutes: refactor and test one function at a time.
- 10–15 minutes: compare before and after, explain, and commit.

### Learn

- A parameter supplies input to a function.
- A return value sends a result back to the caller.
- A local variable belongs to one function call.
- A focused function should have one clear responsibility.
- Returning data and printing data are different jobs.

### Build

Refactor `player_stats.py` using at least four student-written functions. Their responsibilities should be similar to:

```text
calculate_kd(...)
find_best_player(...)
find_worst_player(...)
calculate_average_kills(...)
print_leaderboard(...)
```

These names are design guidance, not implementations. You may improve the names if each function's purpose remains clear.

Also:

1. Keep the zero-death behavior from Day 2.
2. Handle at least one invalid-input case, such as negative kills, negative deaths, or a non-numeric value.
3. Keep calculations in functions that return values where practical.
4. Keep terminal formatting in a separate display function.
5. Run the script after each small refactor:

   ```bash
   python projects/game-statistics-analyzer/src/player_stats.py
   ```

### Required File

```text
projects/game-statistics-analyzer/src/player_stats.py
```

### Acceptance Tests

Record the result of these three cases in comments, a learning log, or terminal output:

| Case | Input | Expected behavior |
|---|---|---|
| Normal player | Positive kills and deaths | Returns the calculated K/D ratio |
| Zero-death player | `deaths = 0` | Follows the documented policy without crashing |
| Invalid player | One invalid value | Rejects or reports the value clearly without a misleading statistic |

### Acceptance Criteria

- [ ] The script contains at least four student-written functions.
- [ ] Function names and parameters communicate their responsibilities.
- [ ] Calculation functions return reusable results instead of only printing.
- [ ] Normal, zero-death, and invalid-input cases have been run and checked.
- [ ] The leaderboard and summary still work after refactoring.
- [ ] Jerry can identify what became simpler than the Day 2 version.

### Explain It

1. Why is `return` useful?
2. Why are several focused functions easier to maintain than one long script?
3. What became simpler after refactoring?
4. Which invalid input did you choose, and how did the program handle it?

### Suggested Git Commit

```text
week1 day3: refactor statistics logic into functions
```

### Optional Bonus

Add a few student-written `assert` checks for the three acceptance cases and explain what an assertion proves.

## Day 4 — CSV Input + JSON Output

### Mission

Replace hard-coded data with a real CSV dataset, calculate aggregate statistics, and save the result as JSON using only Python's standard library.

### Suggested Timebox

- 15–20 minutes: inspect the CSV and learn `csv` and `json` basics.
- 50–55 minutes: parse, convert, calculate, handle missing data, and write JSON.
- 10–15 minutes: inspect the output, explain, and commit.

### Learn

- CSV stores tabular text with one header row and one row per record.
- Values read from CSV begin as strings and must be converted deliberately.
- JSON stores structured data that other programs can read.
- Missing values should be detected and handled according to an explicit rule.

Do not use Pandas in Week 1. Use:

```python
import csv
import json
```

### Build

Create:

```text
projects/game-statistics-analyzer/src/csv_analyzer.py
```

The provided dataset is:

```text
projects/game-statistics-analyzer/data/game_sessions.csv
```

It contains exactly 15 sessions with these columns:

```text
game_id,date,duration_minutes,kills,deaths,accuracy,result,reaction_time_ms
```

Your script must:

1. Read all rows with the `csv` standard-library module.
2. Convert numeric fields before doing arithmetic.
3. Count the games.
4. Calculate average kills and average deaths across all 15 games.
5. Detect the one blank `accuracy` value.
6. Exclude only that blank value from the accuracy average.
7. Create `output/` if needed.
8. Write `projects/game-statistics-analyzer/output/summary.json` with:

   ```text
   total_games
   average_kills
   average_deaths
   average_accuracy
   missing_accuracy_count
   ```

9. Run from the repository root:

   ```bash
   python projects/game-statistics-analyzer/src/csv_analyzer.py
   ```

10. Also confirm it runs from the project directory:

    ```bash
    cd projects/game-statistics-analyzer
    python src/csv_analyzer.py
    ```

Design file paths relative to the script or project directory so both commands locate the same data and output files.

### Required Files

```text
projects/game-statistics-analyzer/src/csv_analyzer.py
projects/game-statistics-analyzer/output/summary.json
```

### Expected Results

```text
Total games: 15
Average kills: 14.00
Average deaths: 8.60
Average accuracy: 41.57%
Missing accuracy values: 1
```

The program must calculate these values from the CSV. Do not hard-code them.

### Acceptance Criteria

- [ ] The script uses `csv` and `json`, not Pandas.
- [ ] Exactly 15 rows are read from the provided file.
- [ ] Numeric strings are converted before calculations.
- [ ] The missing accuracy is counted once and excluded from the accuracy denominator.
- [ ] All five expected aggregate values match.
- [ ] `summary.json` is valid JSON and contains all required keys.
- [ ] Both documented run locations work.
- [ ] Jerry can explain the data flow and missing-value rule.

### Explain It

1. Why do CSV numbers initially arrive in Python as strings?
2. Why is the accuracy average divided by 14 rather than 15?
3. What is the difference between the CSV input and JSON output?
4. How did you make file paths work from both run locations?

### Suggested Git Commit

```text
week1 day4: add CSV input and JSON summary output
```

### Optional Bonus

Calculate win rate. The dataset contains 9 wins and 6 losses, so the result should be:

```text
Win rate: 60%
```

## Day 5 — Game Statistics Analyzer v1

### Mission

Create the final integrated analyzer, verify every required result, improve the project documentation, and prepare a working demo.

### Suggested Timebox

- 10–15 minutes: review the Day 3 and Day 4 code.
- 50–60 minutes: integrate functions, add best/worst selection, and test the output.
- 15 minutes: update README notes, review Git history, and commit.

### Learn

- Integration combines smaller working parts into one program.
- Acceptance criteria make “done” observable.
- A tie-breaker makes selection behavior deterministic.
- A README should let another person run and understand a project.

### Build

Create:

```text
projects/game-statistics-analyzer/src/analyzer.py
```

The final script must read:

```text
projects/game-statistics-analyzer/data/game_sessions.csv
```

and generate:

```text
projects/game-statistics-analyzer/output/summary.json
```

It must calculate everything from the CSV, use focused functions, handle the missing accuracy, and print exactly these result labels and values:

```text
Games analyzed: 15
Average kills: 14.00
Average deaths: 8.60
Average accuracy: 41.57%
Best game: G008
Worst game: G014
Missing accuracy values: 1
```

Definitions:

- **Best game:** the game with the highest number of kills.
- **Worst game:** the game with the lowest number of kills.
- If games have equal kills and both have accuracy values, higher accuracy wins the tie-breaker.
- If an accuracy comparison cannot be made, preserve their CSV order and document that choice.

Run from the repository root:

```bash
python projects/game-statistics-analyzer/src/analyzer.py
```

Then run from the project directory:

```bash
cd projects/game-statistics-analyzer
python src/analyzer.py
```

Review the generated JSON and your changes:

```bash
git status
git diff
git log --oneline -5
```

Update the project README with a short “What I Learned” note and one bug or difficulty you solved. Keep the English and Chinese project README files aligned when changing substantive instructions or project status.

### Required Files

```text
projects/game-statistics-analyzer/src/analyzer.py
projects/game-statistics-analyzer/output/summary.json
projects/game-statistics-analyzer/README.md
projects/game-statistics-analyzer/README.zh-CN.md
```

### Acceptance Criteria

- [ ] The final script reads the CSV rather than hard-coding answers.
- [ ] It uses student-written functions with clear responsibilities.
- [ ] All seven required terminal lines match the expected values and formatting.
- [ ] `G008` is selected as best and `G014` as worst from their kills.
- [ ] The missing accuracy is handled without a crash or false zero.
- [ ] `summary.json` is valid and contains the five required keys.
- [ ] Variable names, function names, file names, and source comments are in English.
- [ ] The program runs from both documented locations.
- [ ] The README files contain clear run instructions and remain bilingual equivalents.
- [ ] Jerry can explain every important part, including any AI-assisted code.

### Explain It

1. How does data move from the CSV file to terminal and JSON output?
2. Which function is most important, and why?
3. Which bug or difficulty took the most thought?
4. How did the program handle missing accuracy?
5. Why should calculated answers never be typed directly into the program?

### Suggested Git Commit

```text
week1 day5: complete game statistics analyzer v1
```

### Optional Bonus

Add the 60% win rate and average reaction time without changing the required output labels.

## Weekly Demo

Give a short live demo at the end of Week 1. Show:

1. `data/game_sessions.csv` as the input.
2. `src/analyzer.py` and the most important function.
3. The required terminal output from a fresh run.
4. The generated `output/summary.json`.
5. The five daily commits with `git log --oneline`.

Answer these questions without reading a prepared script:

1. How does data move from CSV to output?
2. Which function is most important?
3. What bug or difficulty occurred?
4. How was missing data handled?
5. What would change if the dataset had 100,000 rows?

## Parent Review

| Area | Points |
|---|---:|
| Program runs correctly | 2 |
| Statistics are correct | 2 |
| Code is organized into functions | 2 |
| Git commits show daily progress | 1 |
| README/run instructions are clear | 1 |
| Jerry can explain code and one bug | 2 |
| **Total** | **10** |

Recommended passing target: **8/10**.

A lower score does not mean restarting Week 1. Fix the missing items, make another commit, and repeat the demo.
