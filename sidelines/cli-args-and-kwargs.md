# `sys.argv`, `argparse`, and `**kwargs`
*v1.0.0*

This is a sideline reference, not a paced lesson. Read it when you've seen all three of these in Python code, half-noticed they all seem to involve "arguments," and want to know how they actually relate. There's no time budget and nothing to commit.

The running example is your own `runner.py`, invoked from the shell like:

```
python3 runner.py --input brands.yaml --output out.json --limit 2 --csv out.csv
```

Three things in Python code look like they deal with arguments. They live at different layers, and most of the time you only need one of them at a time.

---

## The short version

- `sys.argv` is the raw list of strings the shell handed to Python.
- `argparse` is a standard-library module that *reads* `sys.argv` and turns it into typed, named values.
- `**kwargs` is a Python language feature for passing keyword arguments around *inside* your program. It has nothing to do with the shell.

Two of them (`sys.argv`, `argparse`) sit at the boundary between the terminal and your script. The third (`**kwargs`) lives one layer in — between two Python functions in the same process.

---

## 1. `sys.argv`

### What it actually is

A list of strings, provided by Python's standard library, exposed as `sys.argv`. It contains exactly what you typed at the prompt, split on whitespace, with the first element being the script name itself. The operating system hands these strings to Python as the program starts; Python collects them into a list and puts that list at `sys.argv`.

It is the rawest possible view of the command-line arguments. No types. No validation. No help text. Strings only.

### What it does for `runner.py`

If you run:

```
python3 runner.py --input brands.yaml --limit 2
```

then inside `runner.py`:

```python
import sys
print(sys.argv)
```

prints:

```
['runner.py', '--input', 'brands.yaml', '--limit', '2']
```

Note `'2'` — a string, not an integer. The shell passes everything as text. Converting `"2"` into `2` is your job.

### Tiny example

```python
import sys

# user runs: python3 runner.py brands.yaml 2
input_path = sys.argv[1]
limit = int(sys.argv[2])
print(f"reading {input_path}, limit {limit}")
```

### When you'd reach for it

- Throwaway scripts with one or two positional arguments where writing `--help` isn't worth the keystrokes.
- When you genuinely need the *raw, unparsed* argument list — for example, a wrapper script that re-invokes another command with the same args.
- Almost never in production code. Use `argparse`.

---

## 2. `argparse`

### What it actually is

A module in Python's standard library. It reads `sys.argv` for you and gives back an object with one attribute per flag you declared.

`argparse` is *built on top of* `sys.argv`. It doesn't replace it — it parses it. If you read the argparse source, you'll find it pulling from `sys.argv` near the top.

### What it does for `runner.py`

It's the entire reason your invocation works the way you want it to:

```
python3 runner.py --input brands.yaml --output out.json --limit 2 --csv out.csv
```

`argparse` turns those flag/value pairs into:

```python
args.input   # "brands.yaml"
args.output  # "out.json"
args.limit   # 2 (an int, not "2")
args.csv     # "out.csv"
```

with `--help` generated for free, an error message if you pass an unknown flag, the difference between flags-that-take-values (`--input X`) and on/off flags (`--verbose`), and the ability to use short forms (`-i` for `--input`).

### Tiny example

The shape you already have in `runner.py`:

```python
import argparse

parser = argparse.ArgumentParser(description="Fetch brand pages.")
parser.add_argument("--input", "-i", default="brands.yaml")
parser.add_argument("--output", "-o", default="out.json")
parser.add_argument("--limit", "-n", type=int, default=None)
parser.add_argument("--csv")
parser.add_argument("--verbose", "-v", action="store_true")
args = parser.parse_args()

print(args.input, args.limit, args.verbose)
```

### When you'd reach for it

Anything you'd run more than three times from the terminal. Anything with more than one or two optional arguments. Anything someone other than you will run. In practice: every CLI in this course from W3D4 onward.

---

## 3. `**kwargs`

### What it actually is

A piece of *Python function-definition syntax*. Inside a function signature, `**kwargs` says "collect any extra keyword arguments the caller passed into a dict named `kwargs`." Inside a function call, `**some_dict` says "take this dict and unpack it into keyword arguments."

It lives entirely inside Python. The shell knows nothing about it. The shell can only pass a list of strings; it cannot pass a dict.

### What it does for `runner.py`

By itself? Nothing. You cannot type `**kwargs` at the terminal. The shell would see two asterisks and the word "kwargs" and pass them along as more positional strings.

But it shows up the moment you split `runner.py` into "parse the CLI" and "do the actual work" — see the combined pattern further down.

### Tiny example

Definition side — collect extras into a dict:

```python
def process_url(url, **kwargs):
    timeout = kwargs.get("timeout", 10)
    verbose = kwargs.get("verbose", False)
    ...
```

Call site — unpack a dict as keyword arguments:

```python
options = {"timeout": 30, "verbose": True}
process_url("https://example.com", **options)
# equivalent to: process_url("https://example.com", timeout=30, verbose=True)
```

### When you'd reach for it

- When you're writing a function that forwards arguments to another function and don't want to repeat every parameter name.
- When you want to call a function with a dict you already have, rather than unpacking it by hand.
- Specifically in `runner.py`: when you want to take the parsed argparse result and feed it into an inner `do_work(...)` function. That's the pattern below.

---

## Are `sys.argv` and `argparse` alternatives?

Yes — and `argparse` is the right choice almost every time.

`argparse` is built on `sys.argv`. Anything `argparse` does, you could in principle do yourself by reading `sys.argv` directly. Here's what you'd have to hand-write to match even a small slice of your `runner.py` CLI without it:

```python
import sys

args = {
    "input": "brands.yaml",
    "output": "out.json",
    "limit": None,
    "csv": None,
    "verbose": False,
}

argv = sys.argv[1:]   # drop "runner.py" itself
i = 0
while i < len(argv):
    a = argv[i]
    if a in ("--help", "-h"):
        print("usage: runner.py [--input PATH] [--output PATH] [--limit N] [--csv PATH] [--verbose]")
        sys.exit(0)
    elif a in ("--input", "-i"):
        args["input"] = argv[i + 1]; i += 2
    elif a in ("--output", "-o"):
        args["output"] = argv[i + 1]; i += 2
    elif a in ("--limit", "-n"):
        args["limit"] = int(argv[i + 1]); i += 2     # type conversion is on you
    elif a == "--csv":
        args["csv"] = argv[i + 1]; i += 2
    elif a in ("--verbose", "-v"):
        args["verbose"] = True; i += 1               # store_true is on you
    else:
        print(f"unknown argument: {a}", file=sys.stderr)
        sys.exit(2)
```

That's roughly the equivalent of six `parser.add_argument(...)` lines, and it doesn't even handle `--input=brands.yaml` (with the equals sign), doesn't produce a clean `--help` listing each flag with its description, and gives mediocre error messages. Everything `argparse` gives you for free here — type conversion (`type=int`), short/long forms in one declaration, `action="store_true"` for flags, auto-generated `--help`, sane error messages with proper exit codes — you'd be writing and maintaining yourself.

This is why nobody uses raw `sys.argv` past the toy stage.

---

## Is `**kwargs` an alternative to either?

No. Not even close. It's not the same kind of thing.

`sys.argv` and `argparse` deal with the boundary between **the shell and your Python program**. The shell hands Python a list of strings — that is the only shape an operating system can pass to a new process (the C-level argv is `char**`, an array of strings). There is no shell syntax for "pass a Python dict here." The shell doesn't know what a Python dict is.

`**kwargs` deals with the boundary between **two Python functions in the same running program**. It's a feature of Python's function-call syntax. It only exists once you're already inside a Python process.

So:

- The shell cannot pass `**kwargs`. Ever.
- `**kwargs` does not help you parse the command line.
- Asking "should I use `argparse` or `**kwargs`?" is a category error — like asking "should I use a screwdriver or a sandwich?"

They aren't on the same axis.

---

## The clean pattern: `argparse` AND `**kwargs` together

This is where it clicks. The two tools live at different layers, so the clean design uses both — one at each layer.

```python
import argparse

def do_work(input, output, limit, csv, verbose):
    """The actual work. No argparse. No sys.argv. Just parameters."""
    print(f"reading {input}, writing {output}, limit={limit}, csv={csv}, verbose={verbose}")
    # ... fetch pages, parse, write json/csv ...

def main():
    parser = argparse.ArgumentParser()
    parser.add_argument("--input", "-i", default="brands.yaml")
    parser.add_argument("--output", "-o", default="out.json")
    parser.add_argument("--limit", "-n", type=int, default=None)
    parser.add_argument("--csv")
    parser.add_argument("--verbose", "-v", action="store_true")
    args = parser.parse_args()

    do_work(**vars(args))

if __name__ == "__main__":
    main()
```

The interesting line is:

```python
do_work(**vars(args))
```

Two things happening:

1. `vars(args)` converts the argparse `Namespace` object into a plain dict: `{"input": "brands.yaml", "output": "out.json", "limit": 2, "csv": "out.csv", "verbose": False}`.
2. `**` unpacks that dict as keyword arguments to `do_work`. So the call becomes `do_work(input="brands.yaml", output="out.json", limit=2, csv="out.csv", verbose=False)`.

Why this pattern is worth knowing:

- **`do_work` is testable in isolation.** It takes plain Python values. A unit test can call `do_work(input="tests/fixtures/tiny.yaml", output="/tmp/out.json", limit=1, csv=None, verbose=False)` without going anywhere near `argparse` or `sys.argv`. (You'll see why this matters in Week 5.)
- **`main()` is the only place that knows about the command line.** If you ever swap argparse for a config file or a web request, `do_work` doesn't change.
- **Adding a new argument is local.** Add `parser.add_argument("--new-thing")` and a `new_thing` parameter to `do_work`. The `do_work(**vars(args))` line itself never has to change.

Notice the layering: `sys.argv` (raw strings from the shell) → `argparse` reads it → `vars(args)` converts to a dict → `**` hands the dict to `do_work` as kwargs. Each tool stays in its own lane.

This is also the pattern that makes [`if __name__ == "__main__":`](main-guard.md) earn its keep — `main()` does CLI parsing, the work lives in importable functions, and tests can call those functions directly.

---

## One-line mental models

- **`sys.argv`** — the raw list of strings the shell handed to Python. The arguments as text, with no opinions.
- **`argparse`** — a library that reads `sys.argv` and turns it into typed, named values, with `--help` for free.
- **`**kwargs`** — Python's syntax for handing a dict to a function as keyword arguments. Lives inside the program, not at the command line.

If you only remember one image: `sys.argv` is the doormat with the raw delivery on it, `argparse` is the receptionist who reads the labels and routes each item to the right place, and `**kwargs` is how two coworkers inside the building pass a clipboard between each other.

---

## Where to next

- Open `runner.py`. Refactor `main()` so its body ends with `do_work(**vars(args))` and the actual fetching/writing lives in `do_work`. Run `python3 runner.py --limit 1` — same output, but `do_work` is now callable from a test without any command-line plumbing.
- The next time you write a function that takes "a bunch of options," ask whether the caller already has those options as a dict. If yes, `**some_dict` at the call site is the clean way to forward them.

Companion: [main-guard.md](main-guard.md) — the `if __name__ == "__main__":` idiom that makes the `main()` / `do_work()` split work cleanly.
