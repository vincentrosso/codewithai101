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
| [Week 1, Day 1](W1D1.md) | Toolchain setup: Sublime, Ghostty, Spotlight, terminal basics, GitHub account, first `hello.py` |
| [Week 1, Day 2](W1D2.md) | Homebrew + modern Python, git config, SSH to GitHub, first repo, interactive Python |
| [Week 1, Day 3](W1D3.md) | Virtual environments, pip, `requests`, BeautifulSoup, fetching a webpage, saving to JSON |
| [Week 1, Day 4](W1D4.md) | Lists, for loops, `try`/`except`, batch processing — the seed of the capstone |

## Week 2

Claude (browser, claude.ai) comes online — under two strict rules: ask Claude to *explain*, never to *write*; run everything yourself. The week includes a foundations day mid-week to formalize the mental model behind Week 1's patterns.

| Day | Focus |
|-----|-------|
| [Week 2, Day 1](W2D1.md) | Catch up on Week 1 leftovers; first Claude session with strict rules |
| [Week 2, Day 2](W2D2.md) | Functions and dictionaries; refactor `fetch_many.py` into `fetch_one(url)` |
| [Week 2, Day 3](W2D3.md) | Foundations: HTTP, types, status codes — no code, mini-quiz, no AI |
| [Week 2, Day 4](W2D4.md) | Richer parsing: meta description, og:title, h1, canonical URL |
| [Week 2, Day 5](W2D5.md) | CSV output, sorting; mentor review with a Claude-judging twist |

## Week 3

Move from a flat URL list to a structured `brands.yaml` config; split the script into modules; add a real CLI with `argparse`. By Friday the pipeline takes a brand config, fetches every page per brand, and writes both nested JSON and a flat CSV.

| Day | Focus |
|-----|-------|
| [Week 3, Day 1](W3D1.md) | YAML config: `brands.yaml` with brand → pages; aggregate results per brand |
| [Week 3, Day 2](W3D2.md) | Modules: split into `fetch.py`, `parse.py`, `runner.py`; `__name__ == "__main__"` |
| [Week 3, Day 3](W3D3.md) | Foundations: file I/O, paths, encodings — no code, no AI, mini-quiz |
| [Week 3, Day 4](W3D4.md) | Real CLI: `argparse` with `--input`, `--output`, `--limit`, `--csv`; flat CSV returns |
| [Week 3, Day 5](W3D5.md) | Mentor review — brand-level pipeline demo and bug hunt |

## Week 4

Tyler graduates from Claude in a browser tab to **Claude Code** — the same Claude, but running in the terminal with read access to the brand-lens repo, the ability to edit files, and the ability to run commands. The leap is bigger than Week 2's, so the rules tighten rather than loosen: review every diff before accepting; never auto-approve a command you can't explain; if Claude Code edits a line you don't fully understand, ask it to explain that line *before* you commit. Foundations day on diffs. No Vincent session this Friday — instead, Tyler writes a list of specific questions he can't answer yet about Claude Code or his own code.

| Day | Focus |
|-----|-------|
| [Week 4, Day 1](W4D1.md) | Install Claude Code; first session; new rules; read-only tour |
| [Week 4, Day 2](W4D2.md) | Claude Code as a mirror — explain `runner.py`, `fetch.py`, `parse.py` |
| [Week 4, Day 3](W4D3.md) | Foundations: reading diffs — no code, no AI, mini-quiz |
| [Week 4, Day 4](W4D4.md) | First reviewed edit: add `--brand` flag to `runner.py` |
| [Week 4, Day 5](W4D5.md) | Questions you can't answer yet — list, categorize, retrospective |

## Week 5

Tests enter the project. With Claude Code now editing real files, Tyler needs a safety net that runs in under a second and tells him exactly when a behavior broke. The week walks from a first `pytest` test against `parse_page`, through fixtures and the test-pyramid mental model, to mocking `requests.get` so `fetch.py` can be tested without the live internet. Friday returns to the normal mentor review format — Vincent introduces a small break and the suite has to catch it.

| Day | Focus |
|-----|-------|
| [Week 5, Day 1](W5D1.md) | First `pytest` test, AAA shape, green → red → green cycle |
| [Week 5, Day 2](W5D2.md) | Edge cases, `conftest.py`, `@pytest.fixture` for shared setup |
| [Week 5, Day 3](W5D3.md) | Foundations: units, pure vs side-effectful, test pyramid — no code, no AI, mini-quiz |
| [Week 5, Day 4](W5D4.md) | Mocking the network: `monkeypatch` + a fake response, happy path and error path |
| [Week 5, Day 5](W5D5.md) | Mentor review — break-and-catch, judging a Claude-written test, Week 5 retrospective |

## Future course ideas

Weeks 6 and beyond are open. Candidate topics, in no particular order — order, scope, and inclusion are all subject to change:

- **Persisting and querying with SQLite.** Move from CSV/JSON files to SQLite (built into Python, no install). Create `brands` and `fetches` tables. Learn raw SQL: `SELECT`, `WHERE`, `JOIN`, `GROUP BY` — no ORM. Foundations on relational thinking and primary keys. End state: pipeline writes to a database; "how many brands have a meta description?" is a one-line query.

- **First LLM calls from Python.** Bring Claude into the brand-lens codebase as a library, not just a tool you talk to. Anthropic SDK, API keys, `.env` files, structured-output prompts (get JSON back, not prose). Foundations on what an API actually is, authentication, rate limits. End state: pipeline produces an LLM-generated brand summary per brand. *Note when writing this week: circle back to the W2D4 fields (`description`, `og:title`, `canonical`, `h1`) and tie each one to a concrete brand-evaluation question the LLM is being asked to answer. Right now those fields are floating data points; the brief is where they earn their keep.*

- **From tool to brief — MVP.** Combine fetched data and LLM summaries into per-brand markdown briefs using `jinja2` templates. Generate a `briefs/` directory with one file per brand. Foundations on project structure, `requirements.txt`, README, basic GitHub Actions CI. The Brand Lens MVP demo.

## Sidelines

Reference material outside the day-by-day flow. Read when curious; not paced.

| File | Topic |
|------|-------|
| [BeautifulSoup](sidelines/beautifulsoup.md) | What it is, the tree mental model, the methods you'll actually use, common pitfalls, quick reference |
| [Modules and modularity](sidelines/modules-and-modularity.md) | How Python's `import` works, where pip puts things, and the design principle of splitting code into pieces |
| [`if __name__ == "__main__":`](sidelines/main-guard.md) | What `__name__` actually is, why importing a script can accidentally run it, and the one-line fix |
| [List comprehensions](sidelines/list-comprehensions.md) | The pattern they replace, when to use them, when not to — includes a writing drill, no AI |
| [Assembly and the stack of abstractions](sidelines/assembly.md) | Punch cards to LLMs — what's actually underneath your Python, why we stopped writing assembly, why it still matters |

## Friday review

The week ends with a 30-minute mentor session. The learner demos the work, walks through specific lines of code, and debugs a deliberately-broken version live (no AI). Starting Week 2, the learner also demos Claude on a snippet and judges whether the explanation is correct.
