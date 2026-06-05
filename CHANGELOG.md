# Changelog

Versions follow `major.minor.patch`. Default bump is **patch** (typos, wording, small additions). **Minor** for new sections or meaningful content changes. **Major** for restructuring a full day or changing the learning arc.

---

## W1D1.md

### v1.2.0 — 2026-05-04
- Reframed the journal "what confused me / felt magical" section as "What I don't fully understand" — asks for specific commands or lines instead of vague reflection

### v1.1.0 — 2026-04-30
- Section 3 expanded to cover both Mac and Windows: Win key as built-in launcher, PowerToys Run as Alt+Space alternative

### v1.0.0
- Initial release

---

## W1D2.md

### v1.1.0 — 2026-04-30
- Added section 3: creating a `subl` alias via `~/.zshrc`
- Renumbered subsequent sections (3–8 → 4–9)
- Replaced `open -a "Sublime Text"` call in Python section with `subl`

### v1.0.0
- Initial release

---

## W1D3.md

### v1.1.0 — 2026-05-04
- Removed "magic" framing from section 4 closer and from the journal reflection prompt; replaced with concrete language ("pick one specific line you executed but couldn't have written from scratch")

### v1.0.0
- Initial release

---

## W1D4.md

### v1.1.0 — 2026-05-04
- Friday review checklist no longer references "things that still feel like magic"; uses "lines or concepts you couldn't explain from scratch"

### v1.0.0
- Initial release

---

## W2D1.md

### v1.0.0 — 2026-05-04
- Initial release: catch-up on Week 1 leftovers; first Claude session with Rule 1 (explain, not write) and Rule 2 (run everything yourself)

---

## W2D2.md

### v1.0.0 — 2026-05-04
- Initial release: functions and dictionaries; refactor `fetch_many.py` to extract `fetch_one(url)`

---

## W2D3.md

### v1.0.0 — 2026-05-04
- Initial release: foundations day — HTTP layers, response anatomy, status codes, Python types, mini-quiz (no AI, no Google)

---

## W2D4.md

### v1.0.0 — 2026-05-04
- Initial release: richer HTML parsing — meta description, og:title, h1, canonical URL; consistent record shape; tightened Claude rule

---

## W2D5.md

### v1.0.0 — 2026-05-04
- Initial release: CSV output via DictWriter; sorted results; Week 2 mentor review with Claude-judging twist

---

## W3D1.md

### v1.0.0 — 2026-05-04
- Initial release: structured `brands.yaml` config; nested loop over brands × pages; aggregated `results.json`

---

## W3D2.md

### v1.0.0 — 2026-05-04
- Initial release: split into `fetch.py`, `parse.py`, `runner.py`; introduce imports and `if __name__ == "__main__":`

---

## W3D3.md

### v1.0.0 — 2026-05-04
- Initial release: foundations day on file I/O, context managers, paths (relative vs absolute, `pathlib`), and UTF-8 encoding; no-AI mini-quiz

---

## W3D4.md

### v1.0.0 — 2026-05-04
- Initial release: `argparse` with `--input`, `--output`, `--limit`, `--verbose`, `--csv`; flat per-page CSV returns

---

## W3D5.md

### v1.0.0 — 2026-05-04
- Initial release: Week 3 mentor review — pipeline demo, bug hunt, Claude judgment, retrospective journal

---

## W4D1.md

### v1.0.0 — 2026-05-08
- Initial release: install Node + Claude Code via npm; three new rules (review every diff, never auto-approve a command, ask before committing unclear code); read-only tour of brand-lens; propose-and-reject diff exercise

---

## W4D2.md

### v1.0.0 — 2026-05-08
- Initial release: Claude Code as a calibration mirror — walk through `runner.py`, `fetch.py`, `parse.py` and compare against own understanding from W3D5 review prep; surface where Claude Code is reliable vs. confidently wrong

---

## W4D3.md

### v1.0.0 — 2026-05-08
- Initial release: foundations day on reading diffs — `git diff` output anatomy, hunk headers, staged vs unstaged, spotting a bad diff (scope creep, unexpected deletions, renames-without-call-site-updates); no-AI mini-quiz

---

## W4D4.md

### v1.0.0 — 2026-05-08
- Initial release: first reviewed Claude Code edit — write spec yourself, add `--brand` flag to `runner.py`, review every line of the proposed diff, push back on scope creep, write commit message yourself

---

## W4D5.md

### v1.0.0 — 2026-05-08
- Initial release: no mentor session — write a list of 10+ specific questions about Claude Code or own code, categorize each (`claude code` / `docs` / `vincent` / `later`); Week 4 retrospective journal

---

## W5D1.md

### v1.0.0 — 2026-05-11
- Initial release: install `pytest`, create `tests/` directory, first test against `parse_page` with hand-written HTML, AAA pattern (Arrange/Act/Assert), break the code on purpose to see red, fix to see green

---

## W5D2.md

### v1.0.0 — 2026-05-11
- Initial release: extend `tests/test_parse.py` with edge cases (missing title, missing description, multiple h1s, canonical extraction), introduce `tests/conftest.py` and `@pytest.fixture` for shared HTML setup, framing of "a test is a tiny spec"

---

## W5D3.md

### v1.0.0 — 2026-05-11
- Initial release: foundations day on testing — what counts as a "unit," pure vs side-effectful functions, the test pyramid (unit/integration/e2e), tests-as-spec, what to do when a test fails (fix code vs fix test), flaky tests; no-AI mini-quiz

---

## W5D4.md

### v1.0.0 — 2026-05-11
- Initial release: mocking the network — `monkeypatch` + a minimal `FakeResponse` class to substitute `requests.get`, test the happy path (status 200, html body), test the error path (`requests.RequestException` raises → `fetch_one` returns error tuple, proving the W1D4 `try/except` actually works)

---

## W5D5.md

### v1.0.0 — 2026-05-11
- Initial release: Friday mentor review returns to normal format — demo the test suite, break-and-catch exercise (Vincent introduces a small bug, suite must catch it or Tyler writes the test that would have), Claude-judging twist (ask Claude Code to write a test, judge behavior vs implementation), Week 5 retrospective journal

---

## sidelines/beautifulsoup.md

### v1.0.0 — 2026-05-04
- Initial release: reference for BeautifulSoup — the tree mental model, vocabulary (Tag, NavigableString, attributes, selectors), the methods Tyler will actually use, parser choices, common pitfalls, quick reference. Cross-links to modules-and-modularity.md.

---

## sidelines/modules-and-modularity.md

### v1.0.0 — 2026-05-04
- Initial release: how Python's import system works (sys.path, sys.modules, what pip install actually does, your modules vs library modules) plus the design principle of modularity (clear boundaries, replaceability, testability, examples beyond Python). Cross-links to beautifulsoup.md.

---

## sidelines/why-test.md

### v1.0.0 — 2026-05-27
- Initial release: the motivational "why do unit tests" companion to W5D3's mechanics — the honest "feels like writing the code twice" objection, five grounded reasons (regression alarm, trusting Claude Code's edits, executable documentation, design pressure toward pure functions, speed over manual checking), when *not* to write a test, and a no-AI reflection prompt tied to the W5D5 retro. Cross-links to W5D3.

---

## sidelines/relational-model.md

### v1.0.0 — 2026-06-04
- Initial release: the formal-theory companion to W6D3, "databases from set theory up," built as three un-paced sittings each with a no-AI drill. Sitting 1 — sets (no order/no duplicates), tuples, domains, the Cartesian product, and the punchline that a table *is* a relation (a subset of a Cartesian product); the set-theory⇄SQL vocabulary table; Codd 1970 / declarative-vs-navigational history. Sitting 2 — functional dependencies (`X → Y`), superkey/candidate/primary keys defined via FDs, the three anomalies (update/insert/delete), normal forms 1NF–3NF (+BCNF named) with the `pages_flat` 3NF-violation worked example, "the key, the whole key, and nothing but the key," and an instinct⇄theorem map. Sitting 3 — relational algebra (σ/π/⋈), a real W6D2 query dissolved into `π(σ(⋈))`, declarative-vs-imperative + the query planner, and where SQL bends the math (bags vs sets/`DISTINCT`, ordered columns, NULL → three-valued Kleene logic as the formal reason `= NULL` returns nothing). Cross-links to W6D3 and assembly.md; light Kubrick/Nadsat + HAL flavor.

---

## W6D1.md

### v1.0.0 — 2026-05-27
- Initial release: why a database over flat files; `sqlite3` (built-in); two-table schema (`brands`, `pages`) with PRIMARY KEY / foreign key / NOT NULL / UNIQUE; `init_db`, `insert_brand`, `insert_page` with `?` placeholders (SQL-injection note); `commit()`; read back with the `sqlite3` CLI; `.gitignore` the `.db` file

---

## W6D2.md

### v1.0.0 — 2026-05-27
- Initial release: querying — `SELECT`/`WHERE`, `=` vs `IS NULL` gotcha, `COUNT`, `ORDER BY`, `GROUP BY` (per-category counts), first `JOIN` using the foreign key; queries from Python via cursor + `fetchall`; the `?`-not-f-string rule restated with the injection example

---

## W6D3.md

### v1.0.1 — 2026-06-04
- Added a callout in §4 (the "you don't need the formal theory" line) pointing curious readers to the new [relational-model](sidelines/relational-model.md) sideline — the formal math under the normalization instinct

### v1.0.0 — 2026-05-27
- Initial release: foundations day — a table as a list-of-dicts made strict, primary keys (natural vs surrogate), foreign keys and normalization (store-once-point-everywhere), why not one JSON blob, what `NULL` means (vs `0`/`""`), a note on indexes; no-AI mini-quiz

---

## W6D4.md

### v1.0.0 — 2026-05-27
- Initial release: the re-run problem (UNIQUE → IntegrityError) and `upsert_page` (`INSERT OR REPLACE`, stamp `fetched_at`); wire the db into `runner.py` (`--db` flag, connection lifecycle); test `db.py` with an in-memory `:memory:` `yield` fixture (callback to W5 fixtures, W5D3 side effects, W5D4 faking); idempotent re-runs

---

## W6D5.md

### v1.0.0 — 2026-05-27
- Initial release: Friday review — schema + live query demo, idempotent-upsert demo, break-and-catch on `db.py`, Claude-judging a query (checking `IS NOT NULL` and `?`-parameter safety), Week 6 retrospective journal

---

## W7D1.md

### v1.0.0 — 2026-05-27
- Initial release: first programmatic Claude call — API key from the console, `.env` + `.env.example` (gitignored, key = password), `anthropic` + `python-dotenv`, `client.messages.create` with `model`/`max_tokens`/`messages`, `message.content[0].text`, Haiku for cost; observe non-determinism across two runs (vs pure `parse_page`)

---

## W7D2.md

### v1.0.0 — 2026-05-27
- Initial release: structured JSON output — `page_fields_for_brand` (cursor.description → dicts), `build_prompt` mapping each W2D4 field (`description`/`h1`/`title`/`og_title`/`canonical`) to a brand-evaluation question, system prompt for JSON-only, `summarize_brand` returns a parsed dict via `json.loads`; flags the unparseable-reply gap for D4

---

## W7D3.md

### v1.0.0 — 2026-05-27
- Initial release: foundations day — an API as the W2D3 HTTP request/response with a key attached, authentication (key = password, revoke on leak), tokens as the unit of cost/limits/rate-limits, what a model is and isn't (text predictor, non-deterministic, hallucination, sees only the prompt), and the three reasons a real LLM call can't be unit-tested → mock it (W5D4 callback); no-AI mini-quiz

---

## W7D4.md

### v1.0.0 — 2026-05-27
- Initial release: robust `summarize_brand` returning `(summary, error)` and never raising (two `try/except`: API failure + JSON parse) echoing `fetch_one`; store the brief in the db (`brief` column via `ALTER TABLE`, `set_brief` with `json.dumps`, UPDATE...WHERE); test by mocking `client.messages.create` with `FakeMessage`/`FakeBlock` + `monkeypatch` (W5D4 pattern, new target) — happy/bad-JSON/API-raises paths

---

## W7D5.md

### v1.0.0 — 2026-05-27
- Initial release: Friday review — demo the full fetch→parse→store→summarize→store loop, the new grounded/generic/invented judgment of a brief against its source data, break-and-catch on `brief.py`, Claude-judging a prompt change for grounding, Week 7 retrospective; bridge to the Week 8 brief-MVP

---

## W8D1.md

### v1.0.0 — 2026-05-27
- Initial release: brief MVP begins — `jinja2` (`{{ }}` output vs `{% %}` logic), `templates/brief.md`, pure `render_brief(context)` split from side-effectful `write_brief(slug, markdown)` (W5D3 separation), render one brand's stored brief to `briefs/<slug>.md`; `briefs/` gitignored

---

## W8D2.md

### v1.0.0 — 2026-05-27
- Initial release: `generate_briefs.py` — `build_context` pulls the stored brief (`json.loads`, the round-trip back from W7D4's `json.dumps`) + pages from the db, `generate_all` loops every brand with a `__main__` guard; handle the no-brief brand via the template `{% else %}`; the pretty-vs-true grounding caution; template polish (page descriptions, no-pages case)

---

## W8D3.md

### v1.0.0 — 2026-05-27
- Initial release: foundations day — separation of concerns as the through-line of the whole project (fetch/parse/db/brief/render, pure vs side-effect, data vs presentation), the brand-lens file tree and code/config/data-output categories, dependencies + reproducibility (`requirements.txt`, pinning, "works on my machine"), the README as front door; no-AI mini-quiz

---

## W8D4.md

### v1.0.0 — 2026-05-27
- Initial release: test `render_brief` with substring assertions and NO mocking (contrast with W5D4/W7D4 — it's pure); pin `requirements.txt` to direct deps only (why not `pip freeze`); write the project README (what/setup/usage/layout) with the clone-without-asking test

---

## W8D5.md

### v1.0.0 — 2026-05-27
- Initial release: MVP demo — clean run from an empty db to a `briefs/` folder (three commands), file-tree walkthrough, grounding judgment on the finished/polished brief, break-and-catch on the template/`render.py`, Claude-judging a "Data completeness" template addition, milestone retrospective ("Week 1 me vs now"); bridge to Week 9 (branches/PRs/CI gating)
