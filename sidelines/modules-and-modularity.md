# Modules and Modularity
*v1.0.0*

This is a sideline reference, not a paced lesson. Read it when you want to understand what `from bs4 import BeautifulSoup` is actually doing under the hood, and why programs are written as collections of separate pieces in the first place. There's no time budget and nothing to commit.

This pairs with [beautifulsoup.md](beautifulsoup.md) — that sideline is about the concrete library; this one is about the machinery that makes libraries like that possible, and the design principle behind splitting code into pieces.

---

## Why this matters

You've now done two things that are flavors of the same idea:

1. Written `from bs4 import BeautifulSoup` to use someone else's code.
2. Written `from fetch import fetch_one` to reach into your own other file (W3D2).

Both are *imports*. Both rely on Python's module system. And both are instances of a much older programming idea: **modularity** — splitting a system into pieces with clear boundaries.

If you understand modules well, you can read other people's codebases without feeling lost, structure your own projects so they don't collapse under their own weight, and read package documentation knowing which pieces matter.

---

## Part 1: How Python modules actually work

### A module is a file

The simplest definition: a Python module is a `.py` file. When you wrote `fetch.py` and put a function `fetch_one` inside, you made a module called `fetch`. Other code can `import fetch` and reach for `fetch.fetch_one`.

The same is true for libraries you install with pip. `BeautifulSoup` lives inside a package called `bs4`, which is just a directory of `.py` files installed somewhere on your machine. When you write `from bs4 import BeautifulSoup`, Python finds those files and pulls the `BeautifulSoup` name out of them.

A *package* is a directory of modules with an `__init__.py` file in it. `bs4` is a package; `fetch.py` is a module. The distinction matters less than it sounds — both are imported the same way.

### What `import` actually does

When Python encounters `import fetch` (or `from fetch import fetch_one`), it does roughly this:

1. **Look up `fetch` in `sys.modules`.** This is a dict of every module already loaded in the current Python session. If `fetch` is there, use it — done.
2. **If not, find the file.** Python walks a list of directories called `sys.path` (more on this below) and looks for a file named `fetch.py` (or a package directory named `fetch/`). The first match wins.
3. **Run the file.** Python executes every line of `fetch.py` from top to bottom. Function and class definitions get registered; any code at module level (not inside a function) runs immediately.
4. **Cache the result.** The module is added to `sys.modules` so the next `import fetch` is instant.
5. **Bind the name in your namespace.** `import fetch` makes `fetch` a name in your file. `from fetch import fetch_one` makes `fetch_one` a name in your file (without binding `fetch` itself).

That's it. There's no compilation step, no preprocessing — `import` is just "find and run the file, then expose the names."

This is also why `if __name__ == "__main__":` matters. The module body runs every time it's imported. If you don't guard your "run the script" code with that check, importing the module accidentally runs it too.

### Where Python looks: `sys.path`

Try this in your venv:

```
python3
>>> import sys
>>> for p in sys.path:
...     print(p)
```

You'll see something like:

```
                                                # current directory (empty string)
/Users/tyler/dev/brand-lens
/opt/homebrew/Cellar/python@3.12/.../lib/python3.12
/Users/tyler/dev/brand-lens/venv/lib/python3.12/site-packages
```

Python checks each of these directories *in order*. The first match wins. That's why `from fetch import fetch_one` works — `fetch.py` lives in your project directory, and the project directory is on `sys.path` when you run `python3 runner.py` from there.

The last entry — `site-packages` — is where pip installs everything. When you ran `pip install beautifulsoup4`, pip dropped a `bs4/` directory into that folder. Now `import bs4` finds it.

Knowing this lets you debug import errors. If `import foo` fails with `ModuleNotFoundError`, the file isn't on any directory in `sys.path`. The fix is one of: install it (pip), move it, or run Python from a different directory. Don't change `sys.path` in code if you can help it — there's almost always a cleaner option.

### `import x` vs `from x import y`

Both work. They differ in what name you end up with:

```python
import bs4
soup = bs4.BeautifulSoup(html, "html.parser")    # qualified name

from bs4 import BeautifulSoup
soup = BeautifulSoup(html, "html.parser")        # just the name
```

`from x import y` is more concise; `import x` makes the source obvious at the call site. Most projects mix both. There's no rule, only style.

Avoid `from x import *` — it dumps every public name from the module into your namespace, and you lose track of where things came from. Editors and reviewers can't help if they don't know what `parse_page` is or where it lives.

### What `pip install` actually does

Pip's job is mechanical:

1. Download the package from PyPI (the Python Package Index — pypi.org).
2. Unzip it.
3. Drop the files into your venv's `site-packages/` directory.
4. Record the version in some metadata so you know what's installed.

There's no magic. After `pip install beautifulsoup4`, `bs4/` is just a folder you could open in Sublime. The code is right there. Read it sometime — you'll find the source of methods you've been calling, like `BeautifulSoup.find`. Reading library source is one of the fastest ways to level up.

### Your own modules and pip's modules are the same thing

This is the secret most beginners take a while to internalize: **there's no ladder of "real" modules vs "your" modules**. The Python standard library, pip-installed packages, and your own files are all the same thing under the hood — `.py` files Python imports through `sys.path`. That's the design.

The implication: if your `fetch.py` is good enough that another project would benefit from it, you can publish it to PyPI, run `pip install your-fetch`, and it'll be in someone else's `sys.path` exactly the way `bs4` is in yours. Nothing about the *kind* of code changes. Only the location and packaging.

---

## Part 2: Modularity as a principle

Modules are Python's mechanism. **Modularity** is the design principle that mechanism serves: split a system into pieces with clear boundaries, where each piece does one thing well and exposes a small interface to the rest.

### The principle

A modular system has parts that:

1. **Do one thing.** A module that fetches HTTP requests doesn't also parse HTML. A module that parses HTML doesn't also write to a database.
2. **Have a clear interface.** From outside, you should know what a module provides without reading its source. The function names and their docstrings are the contract.
3. **Hide their internals.** Whatever a module does to accomplish its job is its own business. Other code shouldn't depend on those internals.
4. **Can be replaced.** If `parse.py` works only on English-language pages today, a future `parse.py` that also handles Korean text can be a drop-in replacement — as long as it preserves the interface.

When you split `fetch_many.py` into `fetch.py`, `parse.py`, and `runner.py` on W3D2, you applied this principle. Fetching is one thing. Parsing is another. Orchestrating is a third. Each file does its one thing.

### Why it matters

Three concrete payoffs:

**Replaceability.** If you swap BeautifulSoup for `lxml` directly, only `parse.py` changes. `fetch.py` and `runner.py` don't know `parse.py` uses BeautifulSoup at all. The boundary protects them.

**Testability.** You can test `parse_page(html, url)` by feeding it a hand-written HTML string. No network, no file I/O, no LLMs. The function is pure: same input, same output. Pure functions are the easiest things in programming to be sure are correct. (You'll see this on Day 8 of the testing week, much later.)

**Comprehension.** When you open a 200-line `fetch_many.py`, you have to understand 200 lines to understand any one of them. When you open a 30-line `parse.py`, you have to understand 30. Modular code is *kinder to your future self*.

### The cost

Modularity isn't free:

- **More files to navigate.** A three-file project is harder to scan than a one-file project at first glance.
- **Indirection.** Reading `runner.py`, you see `process_url(url)` and have to jump to find what it does.
- **Premature splits.** If you split too early — before the right boundaries are clear — you encode the wrong structure into the project, and unwinding it costs more than starting flat would have.

The skill is knowing when to split. A useful rule: split when the same change has to be made in two places, or when one file does two distinct jobs that you can name in plain English. "It fetches *and* parses" — those are two jobs.

### Examples beyond Python

The pattern is everywhere once you start looking:

- **Unix philosophy.** "Write programs that do one thing and do it well." `grep` doesn't sort. `sort` doesn't search. They compose with pipes (`|`) — small modules combined to do bigger things.
- **Microservices.** Each service in a microservice architecture is a module: clear boundary, small interface, replaceable.
- **Electrical engineering.** A power supply doesn't also play music. A speaker doesn't also regulate voltage. Standardized interfaces (USB, AC outlets) let you swap pieces without rewiring everything.
- **Cooking — *mise en place*.** Prep each ingredient separately, in its own bowl, *before* you start cooking. Each station is a module; the recipe orchestrates them. Cooks who skip mise en place burn things.

The principle isn't a software invention. It's a way humans manage complexity in any system that has more pieces than fit in one head.

### How to think about it

When you write code from now on, ask:

- **What is this file's one job?** If you can't say it in one sentence, the file does two things and should be split.
- **What does this function take in, and what does it give back?** If the answer is hard to describe, the function is doing too much.
- **If I had to throw away half of this and rewrite it, where would the seams be?** Those seams are where the modules want to be.

Don't over-apply this. A 50-line script doesn't need three files. A 500-line script almost certainly does. `fetch_many.py` was at the boundary on W3D1 — splitting it on W3D2 was the right call. Splitting it on W1D4 would have been premature.

---

## Bringing it back to BeautifulSoup

Now reread the first section of [beautifulsoup.md](beautifulsoup.md) with this lens:

- BeautifulSoup is a module. Its one job is *parsing HTML into a tree you can query*. It doesn't fetch URLs. It doesn't render JavaScript. It doesn't store data. Other tools do those things; BeautifulSoup composes with them.
- Its interface is small: a constructor (`BeautifulSoup(html, parser)`), a handful of methods (`find`, `find_all`, `select`), and the tag objects those methods return. You can use it well without ever opening its source.
- It hides its internals — including the choice of parser. You pick `"html.parser"` or `"lxml"` and BeautifulSoup itself doesn't change.
- It can be replaced. If you outgrow BeautifulSoup, `lxml` directly or `selectolax` are drop-in alternatives for the same job. Your `fetch.py` and `runner.py` wouldn't know.

That's the principle in action. Library design *is* applied modularity. The reason BeautifulSoup is pleasant to use is that someone took the time to draw the lines well.

---

## Where to next

Two practical follow-ons whenever you're ready:

- Open the `bs4/` directory inside your venv's `site-packages/` and read one of the source files. You're going to find Python you can mostly understand — and seeing how a real library is organized is one of the most useful exercises there is.
- The next time you find yourself writing 200 lines that do two distinct things, try splitting before the file gets unwieldy. The goal isn't perfection on the first pass; it's learning to feel the seams.

Companion: [beautifulsoup.md](beautifulsoup.md) — concrete reference for the library most likely to be your first encounter with the module system.
