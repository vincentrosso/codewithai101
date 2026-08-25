# QA Track

A six-lesson post-course module on software quality assurance. It assumes the twelve weeks of the main course — `pytest`, git, PRs, CI, and the habit of reading a diff before accepting it — and flips the chair around: instead of building the thing, you decide whether it's good enough to ship.

**Pace:** ~2 hours per lesson, same as a normal course day. **Lessons 1–4 are AI-free**, for the same reason Week 1 was: you can't judge Claude's test cases in Lesson 5 if you've never written your own.

## The practice app

Every lesson targets one app — **the Toolshop** at [practicesoftwaretesting.com](https://practicesoftwaretesting.com), an online hardware store built as a testing playground. Catalog, filters, search, cart, multi-step checkout, accounts, an admin area, and a REST API at `api.practicesoftwaretesting.com`.

Seeded accounts (verify before trusting — the demo data resets):

```
customer@practicesoftwaretesting.com / welcome01
admin@practicesoftwaretesting.com    / welcome01
```

It's a free service somebody maintains out of goodwill: no load testing, no request floods, no attacks on the server itself.

## Deliverables

The work lives in a new repo, `~/dev/qa-lab`, not in `brand-lens`:

```
plan/      test plan, app map, risk list, release readiness, automation shortlist
cases/     test cases, execution logs, the AI gap review
bugs/      one file per bug report
charters/  exploratory session charters and notes
tests/     the automated smoke suite (Lesson 6)
journal/   one entry per lesson
```

## Lessons

| # | Focus |
|---|-------|
| [Lesson 1](QA01.md) | What QA actually is; oracles — where "expected result" comes from when there's no spec; the app map, the test plan, the risk list |
| [Lesson 2](QA02.md) | Test case design: equivalence partitioning, boundary values, decision tables, state transitions — a ~20-case suite, each case tagged with the technique that produced it |
| [Lesson 3](QA03.md) | Execution (PASS / FAIL / **BLOCKED** / **NOT RUN**), session-based exploratory testing with a charter and a timer, isolating and minimizing a bug, the bug report — severity vs priority, actual vs expected, naming the oracle |
| [Lesson 4](QA04.md) | Regression levels, a ≤10-case smoke suite timed with a real stopwatch, the annual-cost arithmetic, flakiness, why coverage metrics mislead, the release-readiness page, the automation shortlist |
| [Lesson 5](QA05.md) | **Claude as a thinking partner** — gap analysis on your own suite, judged suggestion-by-suggestion (real-new / duplicate / hallucinated / vague / out-of-scope) with a measured hit rate; the pre-mortem prompt; and a deliberate demonstration of a fabricated bug report |
| [Lesson 6](QA06.md) | **Claude Code as a QA engineer** — Playwright smoke tests reviewed diff-by-diff, selector discipline, green→red→green, GitHub Actions on push and a daily cron, and the rule that a failing test is a question, not a task |

## The two threads

**Lessons 1–4** build the judgment: you can't prove software works, you can only sample it — so every technique in the track exists to make that sample smarter than random, and every honest report says what *wasn't* covered.

**Lessons 5–6** put Claude to work against that judgment, and both lessons hinge on the same rule stated two ways: *facts come from your hands and your eyes; Claude gets structure, coverage, and criticism.* A generated test case counts when you've executed it. A generated bug report is furniture until you've observed every specific in it. A failing test is a question to diagnose, not a task to make green.
