# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A curriculum repo for a 12-week self-paced programming course. Content is pure markdown — there is no build system, no test suite, and no runnable code in this repo. The student's actual project code lives in a separate local directory (`~/dev/brand-lens/`), not here.

## Structure

Each week's content is broken into daily lesson files at the root, named `WxDy.md`. **All twelve weeks are written** (W1D1 through W12D5; Week 1 is four days, Weeks 2+ are five — 59 days total). Weeks 9–12: W9 static site (render briefs to HTML, view locally), W10 branches/PRs/CI, W11 deploy to S3, W12 CI/CD auto-deploy + finale. The course **closes at Week 12**; Brand Lens has no runtime backend, so the deploy is a static site on S3 (not a FastAPI server — that, plus Docker/Fargate, is deferred to a Course 2; see the README's "Course 2" section).

```
W1D1.md … W12D5.md — daily lessons (the day count restarts at "day N:" in commit msgs; W8 = days 35–39, W12 = days 55–59)
README.md          — course overview, format, full week-by-week table, sidelines index, future ideas
CHANGELOG.md       — per-file version history (semver; default bump = patch)
sidelines/         — reference material outside the day-by-day flow (read when curious, not paced)
```

The weekly rhythm from Week 2 on is consistent and should be preserved when drafting new weeks: **D1/D2/D4 are hands-on building, D3 is a no-AI foundations day (conceptual + mini-quiz), D5 is the Friday mentor review** (demo → break-and-catch → judge a Claude-written artifact → retrospective journal). The capstone's `brand-lens` code state at the end of each week is cumulative — new weeks build on the files prior weeks created (`fetch.py`, `parse.py`, `runner.py`, `brands.yaml`, then `db.py`, `brief.py`, `render.py`, `templates/`, `tests/`).

Sidelines are kebab-case `.md` files in `sidelines/`, each linked from the README sidelines table and from the lesson day where they're most relevant.

## Pedagogical Constraints

**Week 1 is AI-free by design.** The learner is prohibited from using Claude, ChatGPT, Cursor, or Copilot. All lesson files for Week 1 must reinforce this and never suggest using AI as a shortcut. Google and Stack Overflow are explicitly allowed.

Starting Week 2, AI tools come online. Lessons from that point can include AI-assisted workflows.

## Capstone

The capstone project is **Brand Lens** — a tool that helps Korean brands evaluate their US market presence using web scraping, structured data, and LLM-generated briefs. Every day's lesson is designed to advance this project. W1D3 introduces fetching and saving a single URL; W1D4 extends it to batch processing; the pipeline then grows a YAML config + modules (W3), a test suite (W5), a SQLite store (W6), LLM summarization (W7), and a rendered per-brand brief — the **MVP milestone reached at the end of Week 8**. The capstone is the throughline — lessons should not introduce concepts without connecting them to it.

## Learner Profile

Business background, no prior coding experience. Explanations should avoid assumed knowledge of CS terminology. The "Friday review" is a 30-minute mentor session (with Vincent) where the learner demos their work and debugs a deliberately-broken version of their own code live — no AI allowed during this session.

## Lesson Format

Each `WxDy.md` file (e.g. `W1D1.md`, `W3D5.md`) follows a consistent structure: goal + time budget → numbered sections with inline terminal commands → commit checkpoint → journal prompt → "what done looks like" checklist. Maintain this structure when adding new days. Journal prompts should ask for genuine reflection — name specific lines or concepts the learner can't yet explain — not just factual recall. Avoid vague framings like "what felt magical."

### Required journal sections

Every lesson day's journal prompt must include a section titled **"What I don't fully understand"** (or the equivalent **"A line I accepted but still don't fully grasp"** when the day involves accepting code from Claude Code). The prompt must ask for a *specific named line, function, or concept* — not a general "anything confusing?" question. This is the section that drives the next mentor session, the next lesson's recap, and any sidelines the learner needs.

### The "Edit:" pattern — encourage, don't penalize

When the learner predicts an outcome ("I think this will print an error"), runs the code, finds they were wrong, and edits the journal with the correction (`### Edit` or similar), that is the most valuable habit in the journal. Mini-quiz prompts and "predict the output" exercises should explicitly invite this: ask for a prediction *before* running, and tell the learner that updating the entry afterward — saying what they got wrong and why — is the point of the exercise, not a sign of failure. Friday review notes and lesson recaps should call out specific instances of this pattern by name when they appear in the learner's journal. The behavior is fragile early on; praise it directly so it sticks.
