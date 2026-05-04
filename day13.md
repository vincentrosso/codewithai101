# Day 13 — Real CLI: argparse
*v1.0.0*

**Goal for today:** add command-line arguments to `runner.py` so the input path, output path, and a brand limit can be passed in. By the end of the day you can run `python3 runner.py --input brands.yaml --output out.json --limit 2 --csv out.csv` and the script does what you'd expect. CSV output also returns, in a flatter form.

**Time budget:** ~90 min hands-on, ~15 min journal, ~15 min slack.

---

## 1. Warm-up (~5 min)

Activate venv. Run `python3 runner.py`. Confirm it still works against `brands.yaml`.

## 2. The problem with hardcoded paths (~5 min)

Right now `runner.py` has `"brands.yaml"` and `"results.json"` baked into the code. To run against a different config, you have to edit the script. To do a quick test against just one brand, you have to either edit `brands.yaml` or change the loop. Real CLI tools don't work this way — they take arguments.

## 3. Hello-world argparse (~15 min)

`argparse` is built into Python (no install). Try it in `scratch.py`:

```python
import argparse

parser = argparse.ArgumentParser(description="Greet someone.")
parser.add_argument("--name", default="world")
parser.add_argument("--loud", action="store_true")
args = parser.parse_args()

greeting = f"hello, {args.name}"
if args.loud:
    greeting = greeting.upper() + "!"
print(greeting)
```

Run a few times:

```
python3 scratch.py
python3 scratch.py --name Tyler
python3 scratch.py --name Tyler --loud
python3 scratch.py --help
```

The `--help` output is generated automatically. Notice:

- `--name` takes a value (default `"world"`).
- `--loud` is a flag — `action="store_true"` means it becomes `True` when present, `False` otherwise.
- `args.name` and `args.loud` come back as attributes of the parsed args object.

That's the whole shape: declare arguments, parse, use.

## 4. Wire argparse into runner.py (~25 min)

Open `runner.py`. Add at the top of the file:

```python
import argparse
```

Replace `def main():` and its body with:

```python
def main():
    parser = argparse.ArgumentParser(description="Fetch brand pages and produce a structured report.")
    parser.add_argument("--input", "-i", default="brands.yaml", help="Path to brands YAML config")
    parser.add_argument("--output", "-o", default="results.json", help="Path to output JSON file")
    parser.add_argument("--limit", "-n", type=int, default=None, help="Only process the first N brands")
    parser.add_argument("--verbose", "-v", action="store_true", help="Print each page record as it's processed")
    args = parser.parse_args()

    with open(args.input) as f:
        config = yaml.safe_load(f)

    brand_items = list(config["brands"].items())
    if args.limit is not None:
        brand_items = brand_items[: args.limit]

    brand_results = {}
    for slug, brand in brand_items:
        print(f"\n=== {brand['name']} ===")
        pages = []
        for url in brand["pages"]:
            print(f"  fetching {url}...")
            record = process_url(url)
            if record["error"]:
                print(f"    -> FAILED: {record['error']}")
            else:
                print(f"    -> {record.get('title')}")
            if args.verbose:
                print(f"    record: {record}")
            pages.append(record)
        brand_results[slug] = {
            "name": brand["name"],
            "country": brand["country"],
            "pages": pages,
        }

    with open(args.output, "w") as f:
        json.dump(brand_results, f, indent=2)

    total_pages = sum(len(b["pages"]) for b in brand_results.values())
    total_errors = sum(1 for b in brand_results.values() for p in b["pages"] if p["error"])
    print(f"\ndone. {len(brand_results)} brands, {total_pages} pages, {total_errors} errors.")
    print(f"output written to {args.output}")
```

Things worth noticing:

- Each `add_argument` has a long form (`--input`) and a short form (`-i`). Short forms are convenient at the prompt; long forms are clearer in scripts.
- `type=int` tells argparse to convert the string `"2"` into the integer `2`. Without it, `args.limit` would be a string and `brand_items[:args.limit]` would crash.
- `default=None` for `--limit` means "no limit unless explicitly set." The `if args.limit is not None` guard skips the slice in that case.
- The `--help` text comes from the `help=` arguments. Try `python3 runner.py --help` after you finish.

## 5. Run it a few different ways (~10 min)

```
python3 runner.py --help
python3 runner.py
python3 runner.py --limit 1
python3 runner.py -n 1 -v
python3 runner.py -i brands.yaml -o test-output.json -n 2
```

Confirm:

- `--help` lists every option with descriptions.
- `--limit 1` only processes the first brand.
- `-v` prints the full record for every page.
- `-o test-output.json` writes there instead of `results.json`. Open the file to confirm.

If any of these fail, fix before moving on.

## 6. Bring back CSV output (~15 min)

You paused CSV output on Day 10 when the data shape changed. Now bring it back, flattened — one row per page, with brand name as a column.

Add this to the imports:

```python
import csv
```

Add a `--csv` flag inside `main()`:

```python
parser.add_argument("--csv", help="Also write a flat per-page CSV to this path")
```

Add this near the bottom of `main()`, before the summary print:

```python
    if args.csv:
        rows = []
        for slug, brand in brand_results.items():
            for page in brand["pages"]:
                row = {"brand_slug": slug, "brand_name": brand["name"], "country": brand["country"]}
                row.update(page)
                rows.append(row)
        if rows:
            keys = list(rows[0].keys())
            with open(args.csv, "w", newline="") as f:
                writer = csv.DictWriter(f, fieldnames=keys)
                writer.writeheader()
                for r in rows:
                    writer.writerow(r)
            print(f"csv written to {args.csv}")
```

Run:

```
python3 runner.py --csv results.csv
open results.csv
```

You get a flat spreadsheet — one row per page, with brand_slug / brand_name / country prepended to each row.

## 7. Commit & push (~5 min)

```
git status
git add runner.py
git commit -m "add argparse cli and optional flat csv export"
git push
```

## 8. Journal (~15 min)

`journal/day13.md`. Standard structure. Add:

> If you only got to keep one of these flags — `--input`, `--output`, `--limit`, `--csv` — which would you keep, and why? (Think about which one you actually reached for during testing.)

---

## What "done" looks like for Day 13

- `python3 runner.py --help` produces a useful help message
- `--limit N` actually limits processing to N brands
- `--csv path.csv` produces a flat one-row-per-page CSV
- You can describe in your own words what `type=int` does for an argparse argument
- You can describe in your own words why `default=None` was used for `--limit`
