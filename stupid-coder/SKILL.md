---
name: stupid-coder
description: Write new Python code, or rewrite existing code, so finance/economics students and graduates who learned the core Python toolkit (pandas, NumPy, OOP, standard library, some scikit-learn) in a course and small projects, but have little hands-on practice, can read and maintain it. Calibrates to the user's own code when available. Adds a short header comment before every function/class and concise comments on practical patterns, pitfalls and unusual arguments. Optionally draws a compact Mermaid chart of how the functions call each other. Use when the user invokes "Stupid Coder" / /stupid-coder, or asks for readable, well-commented, or "explain-it-in-the-code" Python.
---

# Stupid Coder

Goal: code that someone who *knows* Python but hasn't *applied* it much can follow top to bottom without asking anyone. The reader is not a beginner: don't explain basics. Readability beats cleverness, brevity and performance (unless performance is explicitly required).

## Modes

- **Write**: the user describes a task → write new code in this style.
- **Rewrite**: the user gives code → keep the behaviour identical, restructure and comment it in this style. Do not add features or silently fix unrelated things; if a bug is spotted, point it out in one line after the code.

## Reader profile

Typical reader: a finance, economics or business student or graduate who completed a Python-for-finance course (basics, error handling, built-ins and standard library, NumPy, pandas, OOP, simulation, classic algorithms) and used scikit-learn in a thesis or small project. The appendix lists what this means in detail.

The reader **knows the important tools**: comprehensions, `lambda`, `groupby`, `merge`, `rolling`, `shift`, `loc`, broadcasting, `try/except`, `with`, classes and inheritance. The gap is **application**: they can read each piece, but may not see how the pieces combine in a real project or why a pattern is chosen.

What to comment:
- **Practical patterns and why they're used**: how steps combine, why this approach (`# shift(1) before ranking → only use info known on that day`), common pitfalls (look-ahead bias, index alignment, `SettingWithCopyWarning`, data leakage, mutating a list while looping).
- **Less common arguments or non-obvious return shapes**: `# axis=1 → across columns, one value per row`, `# result: Series indexed by ticker`.
- **Anything under "Not assumed"** in the appendix → a slightly fuller comment explaining the concept.

What NOT to comment: standard calls the reader knows (`read_csv`, `.mean()`, `.dropna()`, simple loops, basic class syntax).

### Calibrate to the user's own code (do this first)

If code the user wrote is available (attached files, notebooks, the repo, earlier in the conversation, or the code being rewritten), skim it before writing. Patterns they use correctly are actively known → comment them less. Patterns they used wrongly or avoid → comment them more. Match their conventions (notebook cells vs scripts, naming style) where these are readable. If the user states their level, that overrides the default profile.

## Code rules

1. **Descriptive names**: `daily_returns`, `portfolio_weights`, never `r`, `w`, `df2`, `tmp`, `x`. Loop variables too (`for ticker in tickers`, not `for t in ts`).
2. **One step per line**. Split long chains into named intermediate variables:
   ```python
   # not: df.dropna().groupby("ticker")["ret"].mean().sort_values()
   clean_data = prices.dropna()
   mean_return_per_ticker = clean_data.groupby("ticker")["return"].mean()
   ranked_tickers = mean_return_per_ticker.sort_values()
   ```
3. **Explicit over clever**: simple comprehensions and short `lambda`s are fine; nested comprehensions, or one-liners doing three things, become a plain loop or named steps. Vectorised pandas/NumPy is fine (normal practice in quant finance) but comment what it does.
4. **Small functions** with one job each. Order: imports → constants/settings → helper functions → main logic → `if __name__ == "__main__":`.
5. **Constants at the top** in CAPS with a comment (e.g. `TRADING_DAYS_PER_YEAR = 252  # used to annualise`).
6. No unexplained magic numbers; no unused imports.

## Comment rules

- **Section header before every function and class**: 1–3 lines saying *what it does and why*, plus inputs/outputs in plain words:
  ```python
  # --- Compute annualised volatility ---
  # Takes a Series of daily returns, returns one number:
  # the standard deviation scaled up to a yearly figure.
  def annualised_volatility(daily_returns):
  ```
- **Inline comments where the reader profile says so** (practical patterns, pitfalls, unusual arguments) or where the finance logic is non-obvious.
- Finance terms get a few-word gloss the first time (`# Sharpe ratio = return per unit of risk`).

### Comment style: precise, short, skimmable

Readers are smart but skim. Every comment must be worth reading; a few precise comments beat many vague ones.

- **Precise, not vague**: say exactly what happens and what comes out. Name the concrete effect, unit or shape.
  - Bad: `# process the data` / `# calculate returns`
  - Good: `# daily simple returns; first row is NaN (no prior price)`
- **Short**: one line, ideally under ~10 words. Fragments are fine; drop filler words ("This line…", "Here we…", "Now we…").
- **The non-obvious part only**: explain the *why* or the *catch*, not what the code literally says.
  - Bad: `returns = prices.pct_change()  # compute percentage change`
  - Good: `returns = prices.pct_change()  # (P_t / P_{t-1}) - 1, per column`
- **No comments on self-explanatory lines**: `print(...)`, imports of common libraries, simple assignments, `return result`, plotting labels, obvious `if` checks. When unsure, leave it out.
- Prefer putting the explanation in a **good variable name** over writing a comment.
- Comments must stay **correct**: if code changes, update or delete the comment.

## Optional: function-link chart

Offer it (draw it directly if asked, or if the code has ≥4 functions/classes calling each other). Use a **Mermaid flowchart** in a code block: cheap in tokens, renders in GitHub, VS Code, Obsidian and Jupyter markdown cells. Rules:
- Nodes = functions/classes with their real names; arrows = "calls" or "passes data to".
- Edge labels only where they add meaning (e.g. the DataFrame passed along). Max ~15 nodes; group helpers if more.

```mermaid
flowchart TD
    main --> load_prices
    load_prices -->|price DataFrame| compute_returns
    compute_returns --> annualised_volatility
    compute_returns --> sharpe_ratio
```

## Output format

1. The code (one block per file).
2. If rewriting: 1–3 lines on what changed structurally.
3. Chart, if applicable.

No long prose explanations — the comments carry the explanation.

## Appendix: assumed knowledge

The reader knows everything below conceptually and can use the common parts; practical experience is limited. (To adapt the skill to a different level, edit this list.)

### Python basics
- Arithmetic incl. `//`, `%`, `**`; types `int/float/str/bool`, `type()`
- `if/elif/else`, `match/case`; `for`, `while`, `break`, `continue`, `pass`, loop `else`
- Lists (indexing, slicing, mutability, shallow vs deep copy), list comprehensions
- Sets, tuples, dictionaries and their common methods
- Functions, returning multiple values, `lambda`, recursion

### Error handling, built-ins, standard library
- `try/except/else/finally`, `raise`, custom exception classes
- Built-ins: `strip/lower/upper/replace/split/join`, `int/float/str`, `abs/round`, `len/sum/max/min/sorted`, `dict/set/zip/enumerate`, `map/filter`, `isinstance`
- `math` (`log/exp/sqrt/pow/erf`), `random`, `datetime` (`strptime/strftime/timedelta`), `os` (`getcwd/listdir/path/mkdir/remove`), `sys`, `re` (`search/findall/fullmatch`), `collections` (`Counter/deque`), `itertools` (`product/permutations/combinations/cycle/accumulate`), `functools` (`reduce/partial/lru_cache`)
- `with open(...)` for files; decorator syntax `@` seen via `lru_cache`

### NumPy and pandas
- NumPy: `array/arange/linspace/reshape/T`, vectorisation and broadcasting, boolean masks (`x[mask]`, `~mask`), `arr[:, 0]` slicing, `concatenate/column_stack`, `np.linalg`, `dot`, `diag`, `mean/std/median/sum/log/exp/maximum`, `argsort`
- pandas: `Series/DataFrame`, `read_csv`, `head/describe/info`, `isna/dropna`, `loc/iloc`, `sort_values`, `unique`, `groupby/agg`, `merge`, `pivot`, `rolling`, `shift`, `pct_change`, `cov`, `reset_index`, `to_datetime`, Excel export

### Object-oriented programming
- Classes, `__init__`, `self`, instance vs class attributes
- Instance/static/class methods, `_protected`/`__private`, getters/setters
- Inheritance, overriding, `super().__init__`, multiple inheritance, MRO, polymorphism (`NotImplementedError` base methods), composition

### Simulation and plotting
- `np.random.default_rng()`, `rng.random`, `rng.standard_normal`, seeds
- Monte Carlo, Box–Muller, GBM path simulation, Monte Carlo option pricing
- `matplotlib`: `plt.figure/plot/hist/scatter/title/xlabel/legend/show`

### Algorithms and data structures
- Bubble sort, merge sort, timing with `time`, `assert`
- Linked list, stack, queue, deque, `heapq`
- Graphs (BFS/DFS), trees, heaps, dynamic programming (1D/2D tables)

### scikit-learn (project experience)
- Some hands-on use: `train_test_split`, `fit/predict/predict_proba`, common models (logistic regression, random forest, gradient boosting), scalers, standard metrics (accuracy, AUC, confusion matrix)
- Comment the less routine parts: `Pipeline`/`ColumnTransformer`, cross-validation and `TimeSeriesSplit` (why no shuffling with time series), hyperparameter search, class imbalance handling, leakage from fitting scalers on the full data

### Not assumed (explain properly when used)
Generators/`yield`, writing own decorators, `*args/**kwargs`, type hints beyond basics, `dataclasses`, `__dunder__` methods other than `__init__`, self-written context managers, `async`, `apply` with complex functions, `MultiIndex`, `resample`, `stack/unstack/melt`, `scipy`, `statsmodels`, plotting beyond basic matplotlib, regex beyond simple patterns, logging, argparse, multiprocessing.
