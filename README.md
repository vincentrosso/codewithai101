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

## Week 6

CSV and JSON files give way to a real database. Tyler moves the pipeline onto SQLite (built into Python, nothing to install), with a two-table schema (`brands` and `pages`) wired by a foreign key. Raw SQL — `SELECT`, `WHERE`, `COUNT`, `GROUP BY`, `JOIN`, no ORM — turns "how many pages have a meta description?" into a one-line query. The pipeline learns to write to the database with an idempotent upsert (re-runs refresh rows instead of duplicating), and the db layer gets tested against an in-memory database — the Week 5 fixture idea, reused.

| Day | Focus |
|-----|-------|
| [Week 6, Day 1](W6D1.md) | SQLite + `sqlite3`; schema (`brands`, `pages`), `?` placeholders, insert rows, read back with the CLI |
| [Week 6, Day 2](W6D2.md) | Querying: `SELECT`, `WHERE`, `IS NULL`, `COUNT`, `ORDER BY`, `GROUP BY`, first `JOIN`; queries from Python |
| [Week 6, Day 3](W6D3.md) | Foundations: tables as lists-of-dicts, primary/foreign keys, normalization, `NULL` — no code, no AI, mini-quiz |
| [Week 6, Day 4](W6D4.md) | Wire the db into `runner.py`; the re-run problem + upsert; test `db.py` with an in-memory `:memory:` fixture |
| [Week 6, Day 5](W6D5.md) | Mentor review — query demo, break-and-catch, judge a Claude-written query (`IS NOT NULL`, `?` safety) |

## Week 7

Claude moves from a tool Tyler *talks to* into a library his *code calls*. The week covers the Anthropic SDK, API keys in a gitignored `.env`, structured JSON output (not prose) built from the W2D4 fields — each field finally tied to a concrete brand-evaluation question — and storing the resulting brief in the database. The LLM call is wrapped so it never crashes the run (the `fetch_one` contract again) and mocked in tests so the suite stays fast, offline, and free (the W5D4 technique, new target). By Friday Tyler is judging whether Claude's brief is *grounded* in the data or hallucinated.

| Day | Focus |
|-----|-------|
| [Week 7, Day 1](W7D1.md) | First API call; get a key, `.env` + `python-dotenv`, `anthropic` SDK; non-determinism across runs |
| [Week 7, Day 2](W7D2.md) | Structured output: feed scraped fields, get back JSON; each W2D4 field → a brand-evaluation question |
| [Week 7, Day 3](W7D3.md) | Foundations: APIs as HTTP, auth, tokens, what a model is/isn't, why you can't unit-test a real call — no code, no AI |
| [Week 7, Day 4](W7D4.md) | Robust `summarize_brand` (never raises), store the brief in the db, mock the client in tests |
| [Week 7, Day 5](W7D5.md) | Mentor review — judge brief grounding vs hallucination, break-and-catch, improve the prompt with Claude |

## Week 8

The brief MVP. Everything Brand Lens produces has lived in a database where only a SQL query could see it; this week turns it into a readable per-brand document. Tyler learns `jinja2` templates (separating *how a brief looks* from *the data*), renders one markdown file per brand into a `briefs/` directory, and handles the brand with no brief yet. The foundations day names the principle that's been guiding the whole project — separation of concerns — and covers what makes a pile of files a *project*: layout, dependencies, reproducibility. Then the rendering gets tested (trivially, because it's pure), `requirements.txt` gets pinned, and the project README gets written. Friday is the MVP demo — a clean run from an empty database to a folder of briefs.

| Day | Focus |
|-----|-------|
| [Week 8, Day 1](W8D1.md) | `jinja2` templates; `render_brief` (pure) vs `write_brief` (side effect); render one brand to `briefs/<slug>.md` |
| [Week 8, Day 2](W8D2.md) | Render every brand from the database; the `json.dumps`/`loads` round trip; handle the no-brief case |
| [Week 8, Day 3](W8D3.md) | Foundations: separation of concerns, project layout, dependencies + reproducibility, the README — no code, no AI |
| [Week 8, Day 4](W8D4.md) | Test `render_brief` (no mocking — it's pure); pin `requirements.txt`; write the project README |
| [Week 8, Day 5](W8D5.md) | The MVP demo — clean run from empty db, grounding review on the finished brief, break-and-catch, milestone retro |

## Future course ideas

Weeks 9 and beyond. Candidate topics — order, scope, and inclusion subject to change (Weeks 10–12 are the soft part of the estimate; revisit the 12-week total with Tyler around Weeks 9–10):

- **Branches, PRs, and gated checks (CI).** *(planned next, Weeks 9–ish)* GitHub Actions runs the pytest suite on every push, and a red suite *blocks* the change. Prerequisite: Tyler commits straight to `main` today, so a git branch/PR workflow comes first (feature branch → pull request → merge), then basic CI, then branch protection making the green check *required*. The payoff of Weeks 5–8's tests: the suite becomes the merge gate.

- **Deploying to AWS (FastAPI web app).** *(Weeks 10–12, after CI)* A FastAPI app serving the briefs as a real running service. Build up: FastAPI locally → containerize → deploy (App Runner / Fargate over raw EC2), with an optional EventBridge schedule mirroring the autoarb cron model. Foundations on IaaS, IAM, credentials, regions — reusing W7's secret-handling discipline. Expect a dedicated FastAPI-framework week before any AWS step.

- **Wiring summarization behind a flag.** `summarize_all.py` is a manual step at the end of Week 7 (LLM calls cost money, so they shouldn't fire on every fetch run). A natural early task: fold it into `runner.py` behind a `--summarize` flag, default off.

- **Branches, PRs, and gated checks (CI).** The payoff of Weeks 5–7's tests and the [why-test](sidelines/why-test.md) sideline: GitHub Actions runs the pytest suite automatically on every push, and a red suite *blocks* the change. **Prerequisite:** Tyler currently commits straight to `main`, so this needs a git-branch/PR workflow first (feature branch → pull request → review → merge). Sequence: a branching/PR week, then basic CI (Actions running `pytest`), then branch protection that makes the green check *required* before merge. This is where "trust Claude Code's edits because the suite has your back" becomes literal — the suite is the gate.

- **Deploying to AWS (FastAPI web app).** Comes *after* CI gating exists, so deploys only ever ship green code. Target: a **FastAPI web app** that serves the briefs (not just static files) — a real running service. Because this is the heaviest jump in the course, build up to it: first wrap the pipeline output in a small FastAPI app and run it locally; then containerize; then deploy (App Runner or ECS/Fargate is gentler than raw EC2). A scheduled job (EventBridge) can regenerate briefs on a cadence, mirroring the autoarb cron model. Foundations on what IaaS is, IAM, credentials, and regions — reusing the secret-handling discipline from W7's `.env` (an AWS access key is the same kind of secret as an API key). Likely Weeks 10–12. *A FastAPI app is a big surface for a no-prior-experience learner — expect to spend a dedicated week on the framework (routes, request/response, running a server) before any AWS step.*

## Sidelines

Reference material outside the day-by-day flow. Read when curious; not paced.

| File | Topic |
|------|-------|
| [BeautifulSoup](sidelines/beautifulsoup.md) | What it is, the tree mental model, the methods you'll actually use, common pitfalls, quick reference |
| [Modules and modularity](sidelines/modules-and-modularity.md) | How Python's `import` works, where pip puts things, and the design principle of splitting code into pieces |
| [`if __name__ == "__main__":`](sidelines/main-guard.md) | What `__name__` actually is, why importing a script can accidentally run it, and the one-line fix |
| [List comprehensions](sidelines/list-comprehensions.md) | The pattern they replace, when to use them, when not to — includes a writing drill, no AI |
| [Assembly and the stack of abstractions](sidelines/assembly.md) | Punch cards to LLMs — what's actually underneath your Python, why we stopped writing assembly, why it still matters |
| [`sys.argv`, `argparse`, and `**kwargs`](sidelines/cli-args-and-kwargs.md) | Three things that look like "arguments" — what each one actually is, why argparse and kwargs are not alternatives, and the `do_work(**vars(args))` pattern |
| [Why do unit tests?](sidelines/why-test.md) | The motivational "why bother" behind W5D3's mechanics — the five real reasons (regression alarm, trusting Claude Code's edits, executable docs, design pressure, speed), when *not* to test, and a reflection |
| [The relational model](sidelines/relational-model.md) | Databases from set theory up — the formal floor under W6D3. Three sittings: sets/tuples/relations (a table *is* a subset of a Cartesian product), keys + functional dependencies + normalization as theorems (your 3NF instinct, derived), relational algebra (σ/π/⋈) + where SQL bends the math (bags, three-valued NULL logic) |

## Friday review

The week ends with a 30-minute mentor session. The learner demos the work, walks through specific lines of code, and debugs a deliberately-broken version live (no AI). Starting Week 2, the learner also demos Claude on a snippet and judges whether the explanation is correct.
