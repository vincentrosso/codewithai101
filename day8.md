# Day 8 — Richer Parsing: More Than Just the Title
*v1.0.0*

**Goal for today:** extend `fetch_one()` to extract several fields from each page — title, meta description, first h1, og:title, canonical URL — and return a richer record. By the end of the day, your dataset will have enough information per brand to actually compare them.

**Time budget:** ~90 min hands-on, ~15 min journal, ~15 min slack.

---

## 1. Warm-up (~5 min)

Activate venv. Run `python3 fetch_many.py` once. If it doesn't work, fix it before adding new code. The Day 4 rule still applies: don't pile new code on broken code.

## 2. Look at a real page in your browser (~10 min)

Open https://www.innisfree.com (or any brand site) in Chrome or Safari. **Right-click → View Page Source.** Don't be intimidated — it's the same kind of HTML BeautifulSoup is parsing.

Use Cmd-F to find:

- `<title>` — you already know this one
- `<meta name="description"` — a short blurb describing the page (used by Google in search results)
- `<meta property="og:title"` — the title used when the page is shared on social media
- `<link rel="canonical"` — the "official" URL for this page (sites use this to dedupe)
- `<h1>` — the main heading on the page

Notice the difference between the `<title>` tag and `<meta name="description">`. The title is short and goes in the browser tab; the description is a sentence and shows up under search results. For a brand research tool, the description often tells you more about positioning than the title does.

## 3. Extract one new field at a time (~30 min)

Open `fetch_many.py`. You're going to extend `fetch_one()` step by step. Add one field, run, confirm it works, then add the next.

### Add meta description

Inside the `try` block, after you set the title:

```python
desc_tag = soup.find("meta", attrs={"name": "description"})
record["description"] = desc_tag["content"].strip() if desc_tag else None
```

`soup.find()` finds the first matching tag. The arguments here say: "find a `<meta>` tag whose `name` attribute equals `description`." If found, `desc_tag["content"]` reads the `content` attribute of that tag. If not found, `desc_tag` is `None`, and we record `None`.

Run the script. Look at `results.json` — you should see a new `"description"` field for each successful URL.

### Add og:title

```python
og_tag = soup.find("meta", attrs={"property": "og:title"})
record["og_title"] = og_tag["content"].strip() if og_tag else None
```

Same pattern, different attribute name. Run again.

### Add canonical URL

```python
canonical_tag = soup.find("link", attrs={"rel": "canonical"})
record["canonical"] = canonical_tag["href"] if canonical_tag else None
```

Note `href`, not `content` — `<link>` tags use `href`. This is the kind of detail you'd previously have to look up; today, look it up in the BeautifulSoup docs (https://beautiful-soup-4.readthedocs.io/) before reaching for Claude.

### Add first h1

```python
h1_tag = soup.find("h1")
record["h1"] = h1_tag.get_text(strip=True) if h1_tag else None
```

`.get_text(strip=True)` pulls the text content of the tag, stripping whitespace. Different from `.string` — `.string` only works if the tag contains pure text and no other tags inside; `.get_text()` walks the whole subtree. h1 tags often contain other tags (a `<span>` for styling, for instance), so `.get_text()` is the right call here.

## 4. Initialize all fields up front (~5 min)

Your `record` dictionary at the top of `fetch_one` should now initialize every field to `None`:

```python
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
```

This matters: if the request fails entirely, you still want `description` and `og_title` keys in the output (just with `None`) so every record has the same shape. Consistent shape makes the data easy to load later — including tomorrow, when we save to CSV.

## 5. None vs. empty string — when each is right (~10 min)

You'll be tempted to write:

```python
record["description"] = desc_tag["content"] if desc_tag else ""
```

Don't. There's a real difference between "the page didn't have a description" (`None`) and "the page had a description that was an empty string" (`""`). When you load this data later to compare brands, that distinction tells you whether the brand *neglected* meta descriptions or *deliberately* left them blank. Use `None` for missing; use `""` only when the field actually exists with no value.

## 6. Claude rule update (~5 min, only if stuck)

Today's tighter rule: if your code throws an unexpected error, paste **the error and the relevant function** into Claude. Ask:

> Here's my function and the error it threw on this URL. What's wrong?

Read the answer. **Then close the Claude tab and fix it yourself based on what you understood.** Don't paste Claude's fix back into your code; type your own. The point is to convert Claude's explanation into your own understanding.

## 7. Run, inspect, commit (~10 min)

Run `python3 fetch_many.py`. Check `results.json` — every record should have all nine keys (url, fetched_at, status, title, description, og_title, h1, canonical, error). Some keys will be `null` for pages that don't have those tags. That's fine.

```
git status
git add fetch_many.py
git commit -m "extract description, og:title, h1, canonical url"
git push
```

## 8. Journal (~15 min)

`journal/day8.md`. Same structure (What I did / What I learned / What I don't fully understand / Claude check-in). Add one extra question:

> Of the four new fields you extracted today (description, og:title, h1, canonical), which would you trust most as a signal of how a brand presents itself, and why?

That's a business judgment, not a code question. The capstone needs both.

---

## What "done" looks like for Day 8

- `fetch_one()` returns a dict with nine keys: url, fetched_at, status, title, description, og_title, h1, canonical, error
- Every record in `results.json` has the same shape
- You can explain the difference between `.string` and `.get_text()` in BeautifulSoup
- You used Claude at most twice today, and never let it write code for you
