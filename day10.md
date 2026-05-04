# Day 10 — From URL List to Brand Config
*v1.0.0*

**Goal for today:** replace your flat `urls.txt` with `brands.yaml` — a structured file where each brand is a named entity with metadata and a list of pages. Update the script to loop over brands, then over each brand's pages, and aggregate the results per brand.

**Time budget:** ~90 min hands-on, ~15 min journal, ~15 min slack.

---

## 1. Warm-up (~5 min)

Activate venv. Run `python3 fetch_many.py` once against your existing `urls.txt`. Confirm `results.csv` still works. We're about to outgrow this input format.

## 2. Why a brand needs more than a URL (~10 min)

A brand isn't a single page — it's a homepage, an About page, a product page, sometimes regional sites. Yesterday's pipeline can fetch a brand's homepage, but to actually evaluate a brand you need *several* pages, and you need to know which pages belong to which brand.

A flat `urls.txt` can't carry that structure. We need a richer input format.

## 3. Why YAML (~5 min)

YAML is a human-friendly format for structured data. Compared to JSON it has fewer brackets and quotes, supports comments, and is easier to edit by hand. For config files (where humans edit the file directly), YAML is the convention. For data being passed between programs, JSON wins.

Brand Lens config is the human-edit case, so: YAML.

## 4. Install pyyaml; create brands.yaml (~15 min)

With venv active:

```
pip install pyyaml
```

Create `brands.yaml`:

```
touch brands.yaml
subl brands.yaml
```

Type this in:

```yaml
brands:
  innisfree:
    name: Innisfree
    country: South Korea
    pages:
      - https://www.innisfree.com
      - https://us.innisfree.com
  laneige:
    name: Laneige
    country: South Korea
    pages:
      - https://www.laneige.com
      - https://us.laneige.com
  cosrx:
    name: COSRX
    country: South Korea
    pages:
      - https://www.cosrx.com
```

A few notes on YAML syntax:

- Indentation matters — two spaces, no tabs.
- `- ` (dash-space) starts a list item.
- The top-level key (`brands`) is a dict whose values are themselves dicts.
- Comments start with `#` if you want to add them.

Run `cat brands.yaml` to confirm it looks right.

## 5. Load YAML in Python (~10 min)

In `scratch.py`:

```python
import yaml

with open("brands.yaml") as f:
    config = yaml.safe_load(f)

print(type(config))
print(config.keys())
print(config["brands"]["innisfree"])
print(config["brands"]["innisfree"]["pages"])
```

Run it. Notice:

- `yaml.safe_load(f)` parses the file into a Python dict. The structure mirrors your YAML exactly.
- `safe_load` (not `load`) — always use `safe_load` for files you didn't write yourself; the unsafe version can execute arbitrary Python.
- The output is regular Python dicts and lists. Once it's loaded, the YAML origin is invisible.

## 6. Refactor — nested loop (~25 min)

Open `fetch_many.py`. The current outer loop is over URLs. Now it's: over brands, then over each brand's pages.

```python
import yaml
import json
from datetime import datetime
import requests
from bs4 import BeautifulSoup


def fetch_one(url):
    record = {
        "url": url,
        "fetched_at": datetime.now().isoformat(),
        "status": None,
        "title": None,
        "description": None,
        "og_title": None,
        "h1": None,
        "canonical": None,
        "error": None,
    }
    try:
        response = requests.get(url, timeout=10)
        soup = BeautifulSoup(response.text, "html.parser")
        record["status"] = response.status_code
        record["title"] = soup.title.string.strip() if soup.title else "(no title)"
        desc_tag = soup.find("meta", attrs={"name": "description"})
        record["description"] = desc_tag["content"].strip() if desc_tag else None
        og_tag = soup.find("meta", attrs={"property": "og:title"})
        record["og_title"] = og_tag["content"].strip() if og_tag else None
        h1_tag = soup.find("h1")
        record["h1"] = h1_tag.get_text(strip=True) if h1_tag else None
        canonical_tag = soup.find("link", attrs={"rel": "canonical"})
        record["canonical"] = canonical_tag["href"] if canonical_tag else None
    except Exception as e:
        record["error"] = str(e)
    return record


with open("brands.yaml") as f:
    config = yaml.safe_load(f)

brand_results = {}
for slug, brand in config["brands"].items():
    print(f"\n=== {brand['name']} ===")
    pages = []
    for url in brand["pages"]:
        print(f"  fetching {url}...")
        record = fetch_one(url)
        if record["error"]:
            print(f"    -> FAILED: {record['error']}")
        else:
            print(f"    -> {record['title']}")
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
```

You can keep the existing `fetch_one` function. Type the new orchestration code yourself — the outer loop, the inner loop, and the result aggregation.

What changed:

- Input is `brands.yaml` (loaded with `yaml.safe_load`), not `urls.txt`.
- Outer loop iterates `config["brands"].items()` — each iteration gives you a slug ("innisfree") and the brand dict.
- Inner loop iterates that brand's `pages`.
- Output is a dict-of-dicts: `brand_results[slug]` has the brand's name, country, and a list of page records.
- Output goes to `results.json` as pretty-printed JSON (`indent=2`) — easier to read.
- The summary at the end counts brands, pages, and errors.

CSV output is paused for today. We'll restore it in a flatter form on Day 13.

## 7. Run, inspect, commit (~10 min)

```
python3 fetch_many.py
```

Open `results.json` in Sublime — it should be deeply structured: brand → name/country/pages → each page's record. Different shape than yesterday.

```
git status
git add fetch_many.py brands.yaml
git commit -m "switch input to brands.yaml; aggregate results per brand"
git push
```

`results.json` should already be in `.gitignore` from Week 1.

## 8. Journal (~15 min)

`journal/day10.md`. Standard structure (What I did / What I learned / What I don't fully understand / Claude check-in). Add:

> If `brands.yaml` had 100 brands and one of them had a typo making it invalid YAML, what would happen, and at what line of your script would the error appear? (Don't run anything to check — predict.)

---

## What "done" looks like for Day 10

- `brands.yaml` exists with at least 3 brands, each with at least 1 page
- `fetch_many.py` reads `brands.yaml` and produces `results.json` with brand-level structure
- A failed page in one brand doesn't crash the script or stop other brands from being fetched
- You can describe in your own words how a YAML file becomes a Python dict
