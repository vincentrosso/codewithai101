# Day 3 — First Real Script: Fetching a Webpage
*v1.1.0*

**Goal for today:** write a Python script that takes a URL, fetches the page, extracts the title, and saves it to a file. This is the seed of the capstone — every week's project grows from this.

**Time budget:** ~90 min hands-on, ~15 min journal, ~15 min slack.

---

## 1. Warm-up (~10 min)

In Ghostty, navigate to the project, run `git status` (should be clean), run yesterday's hello.py once. If anything's broken, debug it before moving on. Broken things compound — fix them while they're small.

## 2. Virtual environments — what and why (~15 min)

Real Python projects need libraries that aren't built in. We're about to install one (`requests`). But installing libraries globally on your Mac is a mess — different projects need different versions, things conflict, and a year from now you have no idea what's installed for what. The solution is a **virtual environment**: a per-project sandbox for Python packages.

```
cd ~/dev/brand-lens
python3 -m venv venv
ls
```

You'll see a new `venv/` folder. That's the sandbox. To use it, you have to "activate" it:

```
source venv/bin/activate
```

Notice your prompt changed — it now starts with `(venv)`. That means any `python3` or `pip` commands now use the sandbox, not your system Python. Always activate before working on this project. To leave the sandbox, type `deactivate` (don't do that yet).

We don't want git tracking the `venv/` folder — it's huge and machine-specific. Create `.gitignore` in the project root:

```
touch .gitignore
open -a "Sublime Text" .gitignore
```

In Sublime, add:

```
venv/
__pycache__/
*.pyc
.DS_Store
```

Save. Run `git status` — `venv/` should no longer appear. The `.gitignore` itself should appear; commit it:

```
git add .gitignore
git commit -m "add gitignore"
```

## 3. Install your first package (~5 min)

With venv still activated:

```
pip install requests
pip list
```

`pip` is Python's package installer. `requests` is the de facto library for making HTTP calls in Python — it's what almost every Python program uses to fetch web pages or call APIs. `pip list` shows what's installed in your venv.

## 4. Fetch your first webpage (~20 min)

Create a new file: `fetch.py`

```
touch fetch.py
open -a "Sublime Text" fetch.py
```

In Sublime, write this — type it, don't paste:

```python
import requests

url = "https://example.com"
response = requests.get(url)

print("status code:", response.status_code)
print("---")
print(response.text[:500])
```

Save. In Ghostty (with venv active):

```
python3 fetch.py
```

You should see `status code: 200` and the first 500 characters of example.com's HTML. **Pause here.** You just made your computer reach out across the internet, ask a server for a webpage, and read the response. This is what every browser, every app, every API call is doing under the hood. It's not mysterious — it's `requests.get()`.

Now break it deliberately and see what happens. Try each, run, observe the error, then fix:

- Change the URL to `"https://this-site-does-not-exist-12345.com"` — what error?
- Change the URL to `"not-a-url"` — different error?
- Forget the `import requests` line — different error again?

Reading errors is half of programming. The patterns you're seeing now (NameError, ConnectionError, etc.) are ones you'll see thousands of times.

## 5. Extract the title (~15 min)

Right now you're getting raw HTML — a wall of `<tags>`. You want just the page title. Install BeautifulSoup, the standard tool for parsing HTML in Python:

```
pip install beautifulsoup4
```

Update `fetch.py`:

```python
import requests
from bs4 import BeautifulSoup

url = "https://example.com"
response = requests.get(url)

soup = BeautifulSoup(response.text, "html.parser")
title = soup.title.string

print("URL:", url)
print("Title:", title)
```

Run it. You should get `Title: Example Domain`.

Now try it with a real site:

- `https://www.apple.com`
- `https://en.wikipedia.org/wiki/Soft_power`
- A Korean brand site of your choice (a brand you might actually research for the capstone)

Different sites, different titles. You're now a basic web scraper.

## 6. Save the result to a file (~15 min)

Printing to the screen is fine for debugging, but real tools save data. We want to save each fetch to a JSON file so we can build up a collection.

Update `fetch.py`:

```python
import requests
from bs4 import BeautifulSoup
import json
from datetime import datetime

url = input("URL to fetch: ")
response = requests.get(url)

soup = BeautifulSoup(response.text, "html.parser")
title = soup.title.string if soup.title else "(no title)"

result = {
    "url": url,
    "title": title,
    "fetched_at": datetime.now().isoformat(),
}

with open("results.json", "a") as f:
    f.write(json.dumps(result) + "\n")

print("saved:", result)
```

Run it three times with three different URLs. Then look at `results.json` — three lines of JSON, one per fetch. Each line is a complete record of what you fetched, when. This file format (one JSON object per line) is called JSONL and is genuinely what real systems use for log-style data.

A few things worth noticing:

- `if soup.title else "(no title)"` — that's defensive code. Some pages don't have titles. Without that guard, the script would crash on those pages. You'll get a feel for where to add these guards over time.
- `"a"` mode in `open()` means "append" — add to the end of the file rather than overwriting. Try changing it to `"w"` and running again. What happens?
- `datetime.now().isoformat()` produces an ISO 8601 timestamp — a format that's both human-readable and sortable. Always use this format for timestamps; never invent your own.

## 7. Commit and push (~5 min)

```
git status
git add fetch.py
git commit -m "add fetch script: url -> title in jsonl"
git push
```

**Don't commit `results.json`** — data files don't belong in git. Add it to `.gitignore`:

```
results.json
```

Then commit the gitignore update separately. (If you accidentally committed `results.json`, mention it in the journal and I'll show you how to undo it Friday.)

## 8. Journal (~15 min)

`journal/day3.md`. Specifically address:

- In your own words, what does a virtual environment do and why does it exist?
- When you ran `requests.get(url)`, what actually happened on the network? (Best guess.)
- What's the difference between `response.text` and `soup.title.string`?
- Pick one specific line from `fetch.py` that you executed but couldn't have written from scratch. Name the line and what part you can't explain.

---

## What "done" looks like for Day 3

- A working venv in `~/dev/brand-lens/`
- `requests` and `beautifulsoup4` installed in the venv
- `fetch.py` works on at least three different real sites
- `results.json` exists with at least three entries
- The repo is pushed to GitHub with `fetch.py` and updated `.gitignore`, but no `results.json`
