# codewithai101

A self-paced course teaching software development and AI-assisted programming to a learner with a business background and no prior coding experience.

## Format

- **Duration:** 12 weeks
- **Pace:** ~2 hours/day, 5 days/week
- **Structure:** daily applied work on a capstone project, parallel foundations track (CS basics, formal logic, algorithms), twice-weekly mentor review
- **Capstone:** *Brand Lens* — a research tool that helps Korean brands evaluate their US market presence using web scraping, structured data, and LLM-generated briefs

## Week 1

Week 1 is intentionally **AI-free**. The goal is to build the muscle that lets the learner evaluate AI output later — reading errors, navigating the terminal, understanding what each line of code does. Google and Stack Overflow are encouraged; AI assistants come online in Week 2.

| Day | Focus |
|-----|-------|
| [Day 1](day1.md) | Toolchain setup: Sublime, Ghostty, Spotlight, terminal basics, GitHub account, first `hello.py` |
| [Day 2](day2.md) | Homebrew + modern Python, git config, SSH to GitHub, first repo, interactive Python |
| [Day 3](day3.md) | Virtual environments, pip, `requests`, BeautifulSoup, fetching a webpage, saving to JSON |
| [Day 4](day4.md) | Lists, for loops, `try`/`except`, batch processing — the seed of the capstone |

## Week 2

Claude (browser, claude.ai) comes online — under two strict rules: ask Claude to *explain*, never to *write*; run everything yourself. The week includes a foundations day mid-week to formalize the mental model behind Week 1's patterns.

| Day | Focus |
|-----|-------|
| [Day 5](day5.md) | Catch up on Week 1 leftovers; first Claude session with strict rules |
| [Day 6](day6.md) | Functions and dictionaries; refactor `fetch_many.py` into `fetch_one(url)` |
| [Day 7](day7.md) | Foundations: HTTP, types, status codes — no code, mini-quiz, no AI |
| [Day 8](day8.md) | Richer parsing: meta description, og:title, h1, canonical URL |
| [Day 9](day9.md) | CSV output, sorting; mentor review with a Claude-judging twist |

## Week 3

Move from a flat URL list to a structured `brands.yaml` config; split the script into modules; add a real CLI with `argparse`. By Friday the pipeline takes a brand config, fetches every page per brand, and writes both nested JSON and a flat CSV.

| Day | Focus |
|-----|-------|
| [Day 10](day10.md) | YAML config: `brands.yaml` with brand → pages; aggregate results per brand |
| [Day 11](day11.md) | Modules: split into `fetch.py`, `parse.py`, `runner.py`; `__name__ == "__main__"` |
| [Day 12](day12.md) | Foundations: file I/O, paths, encodings — no code, no AI, mini-quiz |
| [Day 13](day13.md) | Real CLI: `argparse` with `--input`, `--output`, `--limit`, `--csv`; flat CSV returns |
| [Day 14](day14.md) | Mentor review — brand-level pipeline demo and bug hunt |

## Weeks 4–6 (sketches — subject to change)

### Week 4 — Persisting and querying
Move from CSV/JSON files to **SQLite** (built into Python, no install). Create `brands` and `fetches` tables. Learn raw SQL: `SELECT`, `WHERE`, `JOIN`, `GROUP BY` — no ORM. Foundations day on relational thinking and primary keys. By Friday, the pipeline writes to a database and you can answer "how many brands have a meta description?" with a single SQL query.

### Week 5 — First LLM calls
Anthropic API enters the codebase. Tyler stops using Claude in a browser tab and starts calling it directly from Python with the official SDK. Topics: API keys, `.env` files, structured-output prompts (get JSON back, not prose). Foundations day on what an API actually is, authentication, and rate limits. By Friday, the pipeline fetches pages and produces an LLM-generated brand summary per brand.

### Week 6 — From tool to brief (MVP, mid-course)
Combine fetched data and LLM summaries into per-brand markdown briefs using `jinja2` templates. Generate a `briefs/` directory with one file per brand. Foundations day on project structure, `requirements.txt`, `README.md`, and a basic GitHub Actions CI step that runs the script on push. Friday is the mid-course retrospective — Brand Lens MVP demo and a look back at the first six weeks before the curriculum hands off to harder topics.

## Friday review

The week ends with a 30-minute mentor session. The learner demos the work, walks through specific lines of code, and debugs a deliberately-broken version live (no AI). Starting Week 2, the learner also demos Claude on a snippet and judges whether the explanation is correct.
