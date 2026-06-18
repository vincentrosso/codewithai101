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

## Week 9 — Brand Lens goes live (static site)

The briefs stop being files only a SQL query can see and become a real site Tyler opens in a browser. Brand Lens has **no runtime backend** — it's a batch pipeline producing pre-computed documents — so the site is static HTML rendered from the same `jinja2` templates as Week 8, viewed locally with `python -m http.server`. This is the re-engagement week after the abstract data weeks: all of it visible, no server concept required.

| Day | Focus |
|-----|-------|
| [Week 9, Day 1](W9D1.md) | Render each brief as a standalone HTML page (not markdown); a per-brand HTML template; write `site/<slug>.html` and open it in the browser |
| [Week 9, Day 2](W9D2.md) | An index page: `site/index.html` listing every brand with links to each brief; a small shared CSS file; the site as a set of linked pages |
| [Week 9, Day 3](W9D3.md) | Foundations: how a browser renders HTML/CSS, what "static" means (files vs a running program), `file://` vs a local server, why `python -m http.server` exists, relative vs absolute links — no code, no AI, mini-quiz |
| [Week 9, Day 4](W9D4.md) | `build_site.py` — regenerate the whole `site/` from the db in one command (empty → full site); handle the no-brief brand; test the pure render functions (no mocking, the W8D4 pattern) |
| [Week 9, Day 5](W9D5.md) | Mentor review — demo the local site, break-and-catch (a broken template or dead link), judge a Claude-written template/CSS, retrospective |

## Week 10 — Branches, PRs, and the gate (CI)

Now that Brand Lens is visible, the plumbing — motivated by Tyler's own evidence: duplicate, misnumbered commits straight to `main`. A git branch/PR workflow first (he commits to `main` today), then GitHub Actions runs `pytest` on every push, then branch protection makes a green suite *required*. The literal payoff of Weeks 5–9's tests and the [why-test](sidelines/why-test.md) sideline: the suite becomes the merge gate.

| Day | Focus |
|-----|-------|
| [Week 10, Day 1](W10D1.md) | The problem with committing to `main` (his own history as the cautionary tale); feature branches: `git switch -c`, change on a branch, push it |
| [Week 10, Day 2](W10D2.md) | Pull requests: open a PR on GitHub, review your own diff, merge, delete the branch — the PR as the review checkpoint (callback to W4's "review every diff") |
| [Week 10, Day 3](W10D3.md) | Foundations: a branch is a pointer to a commit; the history graph; merge; what a PR is socially and technically; why teams don't push to `main` — no code, no AI, mini-quiz |
| [Week 10, Day 4](W10D4.md) | CI: write `.github/workflows/ci.yml` to run `pytest` on every push/PR; watch it pass; break a test → the red ✗ on the PR |
| [Week 10, Day 5](W10D5.md) | Mentor review — branch protection makes green *required*; demo a PR blocked by a red suite; break-and-catch via CI; judge a Claude-written workflow YAML; retrospective |

## Week 11 — Deploy to S3 (live on the internet)

The static site goes public on AWS S3 — the simplest possible deploy, and the stack Tyler will actually ship to at NIT. AWS account, an IAM user (not root), the AWS CLI configured with credentials kept secret the W7 way, an S3 bucket with static website hosting, `aws s3 sync` → a public URL. Framed throughout as "this is how your code ships at NIT," not generic cloud.

| Day | Focus |
|-----|-------|
| [Week 11, Day 1](W11D1.md) | AWS account + an IAM user (not root), least privilege; install the AWS CLI; `aws configure` with credentials stored the gitignored way (W7 discipline); verify with a harmless command |
| [Week 11, Day 2](W11D2.md) | S3: create a bucket; `aws s3 sync site/ s3://…`; enable static website hosting; a public-read bucket policy; the public URL — live on the internet |
| [Week 11, Day 3](W11D3.md) | Foundations: what IaaS is ("someone else's computer"), IAM (users/policies/least privilege), credentials are secrets (an access key is the same kind of secret as a W7 API key), regions, http vs https — no code, no AI, mini-quiz |
| [Week 11, Day 4](W11D4.md) | Make deploy a one-command repeatable step (`deploy.sh`: `build_site` → `s3 sync --delete`); cost + budget alert + tear-down discipline |
| [Week 11, Day 5](W11D5.md) | Mentor review — demo the live site, cloud-shaped break-and-catch (403 / stale site that CI can't catch), judge a Claude-written deploy change, retrospective |

## Week 12 — Automate the deploy, and the finale (CI/CD)

The loop closes: GitHub Actions deploys to S3 on a green merge to `main`, so "ship only green code" becomes literal and automatic — Week 10's gate wired to Week 11's deploy. Then the capstone finale and the course-long retrospective. **The course closes here.**

| Day | Focus |
|-----|-------|
| [Week 12, Day 1](W12D1.md) | CI/CD: add a deploy workflow that syncs to S3 on a push to `main`; AWS creds as GitHub Actions secrets (secret discipline again); watch a merge auto-publish |
| [Week 12, Day 2](W12D2.md) | Guardrails: one workflow, `deploy` `needs: test` (gate → deploy chain); a broken test *skips* the deploy. Optional: scheduled regeneration via an Actions cron |
| [Week 12, Day 3](W12D3.md) | Foundations: the whole arc end-to-end, `fetch.py` → public URL; the two journeys (build vs view); what "deployed" and "CI/CD" mean; the 12-week map — no code, no AI, synthesis quiz |
| [Week 12, Day 4](W12D4.md) | Capstone build: a clean run from an empty database all the way to a robot-deployed live site; fix whatever breaks; finalize the project README + `requirements.txt` |
| [Week 12, Day 5](W12D5.md) | **Course finale** — the MVP live on the internet, the grand break-and-catch across the whole system, judge Claude on the finished tool, the big course-long retrospective |

## Course 2 (what doesn't make the cut)

Brand Lens reaches its deployed MVP at the end of Week 12, and the course closes there. The heavier, more abstract topics a static site simply doesn't need are deferred to a follow-on course, where each is motivated by a real need rather than introduced for its own sake: **FastAPI as a framework, Docker/containers, ECS/Fargate, and a genuinely dynamic backend** — the day Brand Lens needs to *do* something at request time (live search, a trigger-a-scrape button, auth). HTTPS and a custom domain via **CloudFront** (an S3 website endpoint is HTTP-only) is a natural Course-2 opener.

Loose end, not yet placed: wiring `summarize_all.py` into `runner.py` behind a `--summarize` flag (default off), so the manual end-of-Week-7 LLM step joins the pipeline without firing on every fetch run. A small task that could slot into a Course-2 warm-up or a revision pass.

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
