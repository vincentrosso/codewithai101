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
