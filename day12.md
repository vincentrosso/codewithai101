# Day 12 — Foundations: File I/O, Paths, and Encodings
*v1.0.0*

**Goal for today:** no new code. Today's foundations day covers what's actually happening when you call `open()`, why paths are a topic worth taking seriously, and why Korean characters in `brands.yaml` haven't broken anything (yet).

**Time budget:** ~75 min reading, ~30 min journal/quiz, ~15 min slack.

**Rule for today:** no AI. The mini-quiz at the end is for you to answer from memory and reasoning. Same rule as Day 7.

---

## 1. Why this day exists (~5 min)

You've been calling `open()` for two weeks without thinking too hard about it. You've also been writing paths like `"brands.yaml"` and `"results.json"` without thinking about what makes a path. Both are about to bite you when scripts get bigger or move to other machines. Today removes the bites in advance.

## 2. What `with open(...) as f:` actually does (~15 min)

When you wrote:

```python
with open("brands.yaml") as f:
    config = yaml.safe_load(f)
```

four things happened, in order:

1. **`open("brands.yaml")` asks the OS for a file handle.** The OS finds the file on disk, checks permissions, and returns a number that represents your right to read from it.
2. **`as f:` binds that handle to the variable `f`.**
3. **The indented block runs.** `yaml.safe_load(f)` reads from the handle.
4. **`with` automatically closes the handle when the block ends** — even if an exception is raised inside.

Without `with`, you'd have to write:

```python
f = open("brands.yaml")
try:
    config = yaml.safe_load(f)
finally:
    f.close()
```

The `with` form is shorter, safer, and harder to mess up. In Python it's called a **context manager**. Other things use the same pattern (database connections, locks, temporary directories) — `with` is everywhere.

If you forget to close a file handle, the OS leaks the handle. On a small script, no one cares. On a long-running server, you eventually hit the OS limit and your program fails in a way that's hard to diagnose. `with` makes this kind of bug impossible.

## 3. Read modes (~10 min)

`open()` takes a second argument that specifies *how* you're opening the file:

| Mode | Meaning |
|---|---|
| `"r"` | read (default) |
| `"w"` | write — truncates the file first |
| `"a"` | append — adds to the end |
| `"rb"` / `"wb"` | read/write binary (no text decoding) |
| `"r+"` | read and write |

The most common bug: opening with `"w"` when you meant `"a"`. `"w"` silently deletes everything in the file before you write the first byte. If you opened your `results.json` with `"w"` instead of `"a"` on Day 3, you'd have lost every previous run.

Day 3's `fetch.py` used `"a"` to append each fetch as a new line. Day 11's `runner.py` uses `"w"` because it dumps the entire fresh result set every time. Different choices for different jobs.

## 4. Paths (~15 min)

A **path** is a string that names a file on the filesystem. Two kinds:

- **Absolute** — starts from the root: `/Users/tyler/dev/brand-lens/brands.yaml`
- **Relative** — starts from the current working directory: `brands.yaml` or `data/brands.yaml`

When you ran `python3 runner.py` from inside `~/dev/brand-lens`, the relative path `"brands.yaml"` resolved to `/Users/tyler/dev/brand-lens/brands.yaml`. If you had run the same command from your home directory (`cd ~ && python3 dev/brand-lens/runner.py`), the relative path would resolve to `/Users/tyler/brands.yaml` — and it would crash because that file doesn't exist.

**Relative paths are about the cwd, not the script.** This is the single most common path bug.

Python has a better tool for paths: `pathlib`. Instead of:

```python
open("data/" + filename + ".json")
```

you write:

```python
from pathlib import Path
data_dir = Path("data")
open(data_dir / (filename + ".json"))
```

`Path("data") / "results.json"` gives you `Path("data/results.json")`. `pathlib` handles slashes, OS differences (Windows uses backslashes), and gives you methods like `.exists()`, `.is_file()`, `.parent`, `.stem`. You'll start using it next week.

## 5. Encodings — why Korean characters haven't broken anything (~15 min)

Every file on disk is bytes. To turn bytes into characters (text), you need an **encoding** — a table that maps byte sequences to characters. UTF-8 is the encoding that maps to all Unicode characters, including Korean Hangul, Chinese, emoji, and accented letters.

Python 3's default text encoding is UTF-8. When you wrote:

```python
with open("brands.yaml") as f:
```

Python opened it as text mode, decoded with UTF-8. Your `brands.yaml` is plain ASCII so far (no Korean characters yet), and UTF-8 happens to look identical to ASCII for those bytes — that's why it works.

The day you put Korean characters in `brands.yaml` (`name: 이니스프리`), it'll still work — UTF-8 handles them. But the day you fetch a webpage from a server that returns a non-UTF-8 encoding (some old sites still use ISO-8859-1 or EUC-KR), `requests` may show you mojibake. The fix is usually to pass `encoding="utf-8"` explicitly:

```python
with open("brands.yaml", encoding="utf-8") as f:
```

For files you write yourself, always pass `encoding="utf-8"`. For files you fetch over HTTP, `requests` does its best — but the response carries an encoding header, and `response.text` honors it. If a page looks broken in `results.json`, the encoding is the first place to look.

## 6. Mini-quiz (~20 min, no AI, no Google)

Open `journal/day12.md`. Answer each from memory or reasoning.

1. What's the difference between `open(f, "w")` and `open(f, "a")`? Which one would silently destroy your results file?
2. You run `python3 runner.py` from `~/dev/brand-lens`. The script reads `"brands.yaml"`. Predict what happens if you `cd ~` and run `python3 dev/brand-lens/runner.py`. Where does Python look for `brands.yaml`?
3. Why is the `with` form of file handling preferred over manual `open()` and `close()`?
4. What is UTF-8, in plain English? (Two sentences max.)
5. If `runner.py` uses `from fetch import fetch_one`, where does Python look for `fetch.py`?

After your answers are written, you may run small checks to verify.

## 7. Commit (~5 min)

```
git add journal/day12.md
git commit -m "day 12: foundations — file i/o, paths, encodings, mini-quiz"
git push
```

## 8. Journal — the rest of it (~10 min)

Below your quiz answers:

```
## What I don't fully understand
Pick one or two specific concepts from today — context managers, paths, encoding — that still feel fuzzy. Be specific about which part is fuzzy.

## How my mental model changed
What did you believe about file I/O before today that you now think differently about? One paragraph.
```

---

## What "done" looks like for Day 12

- A `day12.md` journal exists with all five quiz answers, written from memory
- You can describe in plain language what `with open(...) as f:` does and why it matters
- You can explain why a relative path like `"brands.yaml"` might or might not work depending on where you run the script from
- You can name at least one situation where you'd use `encoding="utf-8"`
