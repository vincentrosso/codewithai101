# Day 11 — Splitting Code Into Modules
*v1.0.0*

**Goal for today:** split your single-file script into three files — `fetch.py` (the network call), `parse.py` (HTML parsing), and `runner.py` (the orchestration). Learn what an `import` actually does, and what `if __name__ == "__main__":` is for.

**Time budget:** ~90 min hands-on, ~15 min journal, ~15 min slack.

---

## 1. Warm-up (~5 min)

Activate venv. Run yesterday's `fetch_many.py` against `brands.yaml`. Confirm it works.

## 2. Why split a working file (~5 min)

`fetch_many.py` is currently around 70 lines. That's still readable. But it's about to grow: SQLite next week, LLM calls the week after, brief generation the week after that. By Week 6 a single file would be 400+ lines and unscannable.

Splitting now, while the structure is small, is much easier than splitting later. The split also forces you to identify *concerns* — fetching is one concern, parsing is another, orchestration is a third. Files that hold a single concern are easier to reason about.

## 3. The plan (~5 min)

You're going to make three new files:

- `fetch.py` — exports a `fetch_one(url)` function. Knows about HTTP, knows nothing about HTML structure.
- `parse.py` — exports a `parse_page(html, url)` function. Knows about HTML, knows nothing about the network.
- `runner.py` — imports both, reads `brands.yaml`, drives the loop, writes `results.json`. Knows about config and orchestration.

The old `fetch_many.py` will be deleted at the end.

## 4. Create fetch.py (~15 min)

```
touch fetch.py
subl fetch.py
```

```python
import requests


def fetch_one(url):
    """Fetch a URL and return (status, html, error). Never raises."""
    try:
        response = requests.get(url, timeout=10)
        return response.status_code, response.text, None
    except Exception as e:
        return None, None, str(e)
```

Notice:

- Returns three things as a tuple: status, html, error. One of (html) and (error) will be `None`.
- The function does the network call and *only* the network call — no parsing, no record-building.
- The triple-quoted string at the top is a **docstring**. It documents what the function does. Tools and editors pick it up automatically.

In a Python REPL (just type `python3` in the terminal):

```
>>> from fetch import fetch_one
>>> status, html, error = fetch_one("https://example.com")
>>> status
200
>>> html[:80]
>>> exit()
```

You just imported your own module and called your own function from a REPL. That's the model: modules export functions; other code imports and uses them.

## 5. Create parse.py (~15 min)

```
touch parse.py
subl parse.py
```

```python
from bs4 import BeautifulSoup


def parse_page(html, url):
    """Extract structured fields from an HTML page. Returns a dict."""
    soup = BeautifulSoup(html, "html.parser")
    record = {
        "url": url,
        "title": None,
        "description": None,
        "og_title": None,
        "h1": None,
        "canonical": None,
    }
    if soup.title and soup.title.string:
        record["title"] = soup.title.string.strip()
    desc_tag = soup.find("meta", attrs={"name": "description"})
    if desc_tag and desc_tag.get("content"):
        record["description"] = desc_tag["content"].strip()
    og_tag = soup.find("meta", attrs={"property": "og:title"})
    if og_tag and og_tag.get("content"):
        record["og_title"] = og_tag["content"].strip()
    h1_tag = soup.find("h1")
    if h1_tag:
        record["h1"] = h1_tag.get_text(strip=True)
    canonical_tag = soup.find("link", attrs={"rel": "canonical"})
    if canonical_tag and canonical_tag.get("href"):
        record["canonical"] = canonical_tag["href"]
    return record
```

Notice:

- This function takes html (a string) and a url (a string), and returns a dict. No network. No file I/O. Pure data transformation.
- Each field is optional — if the tag isn't there, the field stays `None`. The shape is consistent regardless.
- Multi-line `if` blocks replaced yesterday's ternary expressions for readability. Both work; flat `if` reads better when there are several.

Test it from a REPL:

```
>>> from fetch import fetch_one
>>> from parse import parse_page
>>> status, html, error = fetch_one("https://example.com")
>>> parse_page(html, "https://example.com")
{'url': 'https://example.com', 'title': 'Example Domain', ...}
```

## 6. Create runner.py (~20 min)

```
touch runner.py
subl runner.py
```

```python
import json
from datetime import datetime
import yaml

from fetch import fetch_one
from parse import parse_page


def process_url(url):
    record = {
        "url": url,
        "fetched_at": datetime.now().isoformat(),
        "status": None,
        "error": None,
    }
    status, html, error = fetch_one(url)
    record["status"] = status
    record["error"] = error
    if html is not None:
        record.update(parse_page(html, url))
    return record


def main():
    with open("brands.yaml") as f:
        config = yaml.safe_load(f)

    brand_results = {}
    for slug, brand in config["brands"].items():
        print(f"\n=== {brand['name']} ===")
        pages = []
        for url in brand["pages"]:
            print(f"  fetching {url}...")
            record = process_url(url)
            if record["error"]:
                print(f"    -> FAILED: {record['error']}")
            else:
                print(f"    -> {record.get('title')}")
            pages.append(record)
        brand_results[slug] = {
            "name": brand["name"],
            "country": brand["country"],
            "pages": pages,
        }

    with open("results.json", "w") as f:
        json.dump(brand_results, f, indent=2)

    total_pages = sum(len(b["pages"]) for b in brand_results.values())
    total_errors = sum(1 for b in brand_results.values() for p in b["pages"] if p["error"])
    print(f"\ndone. {len(brand_results)} brands, {total_pages} pages, {total_errors} errors.")


if __name__ == "__main__":
    main()
```

A few new things:

- The two import lines `from fetch import fetch_one` and `from parse import parse_page` are how `runner.py` reaches into your other files. Python looks for `fetch.py` and `parse.py` in the same directory.
- `process_url(url)` glues fetch + parse together and adds a timestamp/status/error envelope. It's the seam that turns "html bytes" into "a record."
- `dict.update(other_dict)` merges keys from `other_dict` into `dict`. We use it to fold parse_page's fields onto the envelope.
- `def main():` wraps the orchestration in a function. That's a convention; it lets other code import this file without running it.
- `if __name__ == "__main__":` is the bottom-of-file pattern for "only run main() if this file was executed directly, not imported." More on this in section 7.

Run it:

```
python3 runner.py
```

Same output as yesterday's `fetch_many.py`. Confirm `results.json` looks the same.

## 7. The `__name__ == "__main__"` pattern (~5 min)

Every Python file has a built-in variable `__name__`. When you run a file directly (`python3 runner.py`), `__name__` is `"__main__"`. When you import that file from another file, `__name__` is the module name (`"runner"`).

The `if __name__ == "__main__":` block at the bottom only runs in the first case. That means you can `from runner import process_url` from another script (say, a test) without `main()` accidentally running and fetching every brand.

You don't need this on `fetch.py` or `parse.py` because they're library files — meant to be imported, not run.

## 8. Delete fetch_many.py (~2 min)

```
rm fetch_many.py
git status
```

`fetch_many.py` shows as deleted. That's intentional — it's been replaced by the three-file split.

## 9. Commit & push (~5 min)

```
git add fetch.py parse.py runner.py fetch_many.py
git commit -m "split fetch_many.py into fetch.py, parse.py, runner.py modules"
git push
```

Note `git add fetch_many.py` — that stages the deletion.

## 10. Journal (~15 min)

`journal/day11.md`. Standard structure. Add:

> Why does `parse.py` not import `requests`, and why does `fetch.py` not import `BeautifulSoup`? What would go wrong if both files imported both libraries?

---

## What "done" looks like for Day 11

- Three new files exist: `fetch.py`, `parse.py`, `runner.py`. `fetch_many.py` is gone.
- `python3 runner.py` produces the same `results.json` as yesterday's script
- You can describe what `from fetch import fetch_one` does
- You can explain in plain language what `if __name__ == "__main__":` is for
