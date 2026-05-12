# List Comprehensions
*v1.0.0*

This is a sideline reference, not a paced lesson. Read it when you've seen lines like `urls = [line.strip() for line in f if line.strip()]` in your own code and can read them but couldn't write one cold. There's no time budget. There **is** a writing drill at the bottom — do it without Claude.

---

## The one-line answer

A list comprehension is a compact way to build a list. It replaces a three-line `for`/`append` pattern with one line that reads left-to-right as *"a list of `expr` for each `x` in `iterable`, where `condition` holds."*

If that sentence already makes sense, skim the anatomy section below and jump to the drill.

---

## The pattern they replace

You've written this shape many times:

```python
results = []
for url in urls:
    results.append(url.strip())
```

Three lines. The for-loop's only job is to build a list. The `.append` is mechanical noise — you do it every iteration, with the same call, on the same list.

The comprehension form:

```python
results = [url.strip() for url in urls]
```

Same output. One line. The intent — *"for each url, give me the stripped version"* — is the line itself, not a side effect of three other lines.

---

## Anatomy

A list comprehension always lives inside square brackets. It has up to three pieces:

```
[ expr  for x in iterable  if condition ]
   |          |                  |
   |          |                  └── (optional) keep only items where condition is true
   |          └── walk every item in iterable, calling it x
   └── for each x kept, put expr into the new list
```

Read it left-to-right, in plain English, every time:

```python
[url.strip() for url in urls if url.startswith("https")]
```

> *"The list of `url.strip()` for each `url` in `urls`, where `url.startswith("https")`."*

That sentence is the comprehension. If you can't say it, you can't write it yet.

---

## Walking from a for-loop to a comprehension

Three steps. Always the same three steps.

**Start with the for-loop you'd write without comprehensions.**

```python
results = []
for line in f:
    if line.strip():
        results.append(line.strip())
```

**Step 1 — find the four pieces.**

- The list being built: `results`
- The iterable being walked: `f`
- The variable name for each item: `line`
- The expression appended: `line.strip()`
- The filter (optional): `line.strip()` (truthy)

**Step 2 — write the comprehension shape.**

```
[ expr  for var in iterable  if filter ]
```

Plug in:

```python
[line.strip() for line in f if line.strip()]
```

**Step 3 — assign it.**

```python
results = [line.strip() for line in f if line.strip()]
```

Done. That's the line from your `urls.txt` loader on W2D1 — written out the long way, then folded down.

---

## When NOT to use a comprehension

The point of a comprehension is *clarity*. The moment a comprehension hurts readability, write the for-loop instead.

**The body has more than one expression.**

If the loop body does multiple things — opens a file, parses it, writes to disk, *and* records a result — that's a real loop, not a list build. Don't try to cram it.

```python
# Bad — comprehension hiding side effects
[process(url) and log(url) for url in urls]

# Better — just write the loop
for url in urls:
    log(url)
    process(url)
```

**The condition is complicated.**

A short `if` is fine. An `if` that's a 60-character boolean expression hurts more than the savings.

```python
# Hard to read
[u for u in urls if u.startswith("https") and not u.endswith(".pdf") and "tracking" not in u]

# Clearer
def is_clean(url):
    return url.startswith("https") and not url.endswith(".pdf") and "tracking" not in url

clean_urls = [u for u in urls if is_clean(u)]
```

**You need an `else`.**

Comprehensions support an `if`/`else` *in the expression*, but the syntax is different and ugly:

```python
[u if u.startswith("https") else "http://" + u for u in urls]
```

Sometimes worth it; often the for-loop is clearer.

**Nested comprehensions deeper than two levels.**

A nested comprehension can replace a nested for-loop:

```python
all_pages = [page for brand in brands for page in brand["pages"]]
```

That's readable — two levels, simple. Three levels and beyond is hard for anyone to parse. Use the nested for-loop instead.

---

## Three real cases from brand-lens

Pulled from the kind of code already in your repo. Read each, not the comprehension form first — read the for-loop form, picture it running, then read the comprehension and convince yourself they're the same.

### Case 1 — strip blank lines from a URL file

For-loop form:

```python
urls = []
with open("urls.txt") as f:
    for line in f:
        if line.strip():
            urls.append(line.strip())
```

Comprehension form (the line you saw on W2D1):

```python
with open("urls.txt") as f:
    urls = [line.strip() for line in f if line.strip()]
```

### Case 2 — keep only successful results

For-loop form:

```python
successful = []
for r in results:
    if r["error"] is None:
        successful.append(r)
```

Comprehension form:

```python
successful = [r for r in results if r["error"] is None]
```

### Case 3 — flatten brands → pages

For-loop form:

```python
all_pages = []
for slug, brand in config["brands"].items():
    for url in brand["pages"]:
        all_pages.append(url)
```

Comprehension form (two-level):

```python
all_pages = [url for slug, brand in config["brands"].items() for url in brand["pages"]]
```

Notice the order: the for-clauses read **left-to-right in the same order as the nested for-loop**. Outer first, inner second. Beginners often get this backwards — read the long form aloud, then the comprehension, until the order feels obvious.

---

## The drill (writing, not reading)

Open your own `runner.py` (or `parse.py` — whichever has more for-loops with `.append`).

**Find one for-loop whose only job is to build a list.** It probably starts with `xs = []` and ends with `xs.append(...)`. There's at least one.

Without Claude. Without rereading the cases above. Cover this doc:

1. Name the four pieces (or five, with a filter): list name, iterable, item variable, expression appended, filter (if any).
2. Plug them into `[ expr for var in iterable if filter ]`.
3. Run your file. Same output as before? Good.

Then ask yourself: *was the comprehension actually clearer?* Sometimes the answer is yes, sometimes no. The judgment is the skill — not the syntax.

If you can't find a single-purpose append loop in your code, write a small one in a scratch file:

```python
# scratch.py
brands = ["innisfree", "laneige", "amorepacific"]

# Build a list of brand URLs in the form "https://www.<brand>.com"
# First as a for-loop. Then as a comprehension. Then check they match.
```

Cover this doc, write both, run them, compare outputs. Don't paste them anywhere — the point is the muscle, not the artifact.

---

## What to journal

Add this to today's journal entry, or to the next one if you do the drill on its own day:

```
## List comprehension drill
The for-loop I converted (file + line numbers):

The comprehension form:

Was the comprehension actually clearer than the for-loop? Why or why not?
```

The third question is the one that matters. The syntax is rote; the judgment of *when to use it* is the thing you're building.

---

## Where to next

- The next time you find yourself typing `xs = []` followed by a for-loop with only an `.append` inside, pause. Try the comprehension form first.
- When you read other people's Python (libraries, examples, Claude's diffs), notice comprehensions. Read each one out loud as the long-form sentence. The fluency comes from doing this enough times that the translation is automatic.
- Set comprehensions and dict comprehensions exist too — `{x for x in xs}` and `{k: v for k, v in pairs}`. Same shape, different brackets, same mental model. You can pick those up in 60 seconds once the list version feels natural.

Companion: [modules-and-modularity.md](modules-and-modularity.md) — comprehensions are a Python-specific shape; the modular-design instinct is what makes you reach for them in the right place.
