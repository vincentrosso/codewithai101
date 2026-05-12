# `if __name__ == "__main__":`
*v1.0.0*

This is a sideline reference, not a paced lesson. Read it when you've seen `if __name__ == "__main__":` at the bottom of a Python file and can't quite say in plain English what it's doing or why it's there. There's no time budget and nothing to commit.

This pairs with [modules-and-modularity.md](modules-and-modularity.md) — you can read this one cold, but the `import` mechanics in that sideline are what make this idiom necessary in the first place.

---

## The one-line answer

`if __name__ == "__main__":` is the way a Python file says "only do this when I'm being run directly — not when I'm being imported."

If you understand that sentence, you have the idea. The rest of this doc is *why* that distinction exists, and what goes wrong without it.

---

## What `__name__` actually is

Every Python file has a built-in variable called `__name__`. Python sets it for you, automatically, depending on how the file got loaded.

Two cases. Only two.

**Case 1 — the file was run directly.**

```
python3 runner.py
```

Inside `runner.py`, `__name__` is the string `"__main__"`. Always. That's Python telling the file: *you're the entry point this time.*

**Case 2 — the file was imported.**

```python
# inside some other file
from runner import process_url
```

Now inside `runner.py`, `__name__` is the string `"runner"` — the module's own name. Python is telling the file: *you're being read so someone else can use your functions.*

That's the whole mechanism. There's no magic. `__name__` is just a string variable that says "directly run" or "imported."

---

## Why the distinction matters

Recall from [modules-and-modularity.md](modules-and-modularity.md): when Python imports a file, it **runs every line of that file from top to bottom**. Function and class definitions get registered. Anything at module level (not inside a function) executes immediately.

That last part is the trap.

Imagine `runner.py` ends with this, no guard:

```python
def process_url(url):
    ...

results = []
for url in load_urls():
    results.append(process_url(url))

print(f"done. {len(results)} pages fetched.")
```

Run it directly with `python3 runner.py` — works fine. Fetches, prints the summary.

Now imagine `tests/test_runner.py` does this:

```python
from runner import process_url

def test_process_url_handles_404():
    ...
```

The moment Python hits `from runner import process_url`, it executes *all* of `runner.py` to make `process_url` available. Which means **the for-loop runs**. Which means your test suite makes real HTTP requests to every URL in `brands.yaml`, every time you run `pytest`.

That's the bug `if __name__ == "__main__":` prevents.

---

## The fix

Wrap the "run the script" code in the guard:

```python
def process_url(url):
    ...

def main():
    results = []
    for url in load_urls():
        results.append(process_url(url))
    print(f"done. {len(results)} pages fetched.")

if __name__ == "__main__":
    main()
```

Now:

- `python3 runner.py` → `__name__` is `"__main__"` → `main()` runs. Same as before.
- `from runner import process_url` → `__name__` is `"runner"` → the `if` is false → `main()` does **not** run. Only the `def`s execute. The importer gets the function it asked for and nothing else.

The function definitions are always registered (that's the point of importing). The script-level work is gated on whether the file is the entry point.

---

## Mental model: every file is potentially two things

Every Python file is potentially two things at once:

1. **A library** — a collection of functions and classes that other code can import.
2. **A script** — something you can run from the terminal.

`if __name__ == "__main__":` is the seam between those two roles. Above the guard: definitions, available to anyone who imports. Below the guard: the script behavior, only triggered when you run the file directly.

A file without the guard collapses both roles into one. Importing the file accidentally runs the script. That's the whole problem.

---

## What goes inside `main()`

Convention is to keep the script logic inside a single `main()` function and just call it from the guard:

```python
if __name__ == "__main__":
    main()
```

Why a function instead of inlining the code under the `if`?

- **Testability.** You can write `from runner import main` in a test and call it with controlled arguments. Code stuck under an `if __name__` block can't be reached except by re-running the script.
- **Local variables.** Code at module level creates module-level variables. Code inside `main()` creates local ones, which can't accidentally collide with anything else.
- **Readability.** The guard becomes a one-liner. The file ends with a clear "here's what runs when this is the entry point" statement.

You'll see both styles. The function-call form ages better.

---

## Common variations you'll see

```python
if __name__ == "__main__":
    main()
```

The most common form. Just runs `main()`.

```python
if __name__ == "__main__":
    import sys
    sys.exit(main())
```

`main()` returns an integer exit code (0 for success, non-zero for failure), and `sys.exit` passes it to the shell. Useful when the script is going to be called from a Makefile or CI job that needs to know whether it succeeded.

```python
if __name__ == "__main__":
    raise SystemExit(main())
```

Same as above, slightly more concise. `SystemExit` is the exception that `sys.exit` raises under the hood.

You don't need to use these variants in brand-lens — `main()` alone is fine. Just recognize them when you see them in other code.

---

## Does every file need it?

No. The guard is for files that are **both** importable libraries **and** runnable scripts.

- `parse.py` defines functions that other modules call. It isn't run directly. No guard needed.
- `fetch.py` — same. No guard needed.
- `runner.py` is the entry point: you run it with `python3 runner.py`. It also defines functions that tests might one day import. **It needs the guard.**

A useful question: *would I ever type `python3 thisfile.py` at the terminal?* If yes, give it the guard. If no, skip it.

---

## Why is `__name__` written with double underscores?

The `__name__` variable, like `__init__`, `__main__`, and a handful of others, uses leading and trailing double underscores by convention. Python programmers call them **dunders** — short for "double underscore."

Dunders are reserved for names that Python itself sets or uses. The double underscores are a visual flag: *this isn't a name you invented; it's part of the language's plumbing.* Don't make up your own dunder names — leave that namespace to Python.

You don't need to memorize the list. When you see something like `__name__` or `__main__`, know it's language-level, and look up what it does if it's unfamiliar.

---

## Where to next

- Open your `runner.py` and find the `if __name__ == "__main__":` line. Cover the rest of the file with your hand. Ask yourself: *if someone wrote `from runner import process_url`, what would happen without this guard?* Then read the file with the guard in mind.
- The next time you write a script that has a "here's the actual work being done" block at the bottom, wrap it. The guard costs you two lines and saves you from a class of bug that's invisible until tests start importing your script.

Companion: [modules-and-modularity.md](modules-and-modularity.md) — the import system that makes this idiom necessary.
