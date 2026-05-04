# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Repo Is

A curriculum repo for a 12-week self-paced programming course. Content is pure markdown — there is no build system, no test suite, and no runnable code in this repo. The student's actual project code lives in a separate local directory (`~/dev/brand-lens/`), not here.

## Structure

Each week's content is broken into daily lesson files at the root. Week 1 is fully written; future weeks will follow the same pattern.

```
day1.md  — toolchain setup (Sublime, Ghostty, terminal basics, first hello.py)
day2.md  — Homebrew, Python 3.12, git config, SSH, first GitHub repo
day3.md  — virtual environments, requests, BeautifulSoup, save to JSONL
day4.md  — lists, for loops, try/except, batch URL fetching (fetch_many.py)
README.md — course overview, format, week-by-week table
```

## Pedagogical Constraints

**Week 1 is AI-free by design.** The learner is prohibited from using Claude, ChatGPT, Cursor, or Copilot. All lesson files for Week 1 must reinforce this and never suggest using AI as a shortcut. Google and Stack Overflow are explicitly allowed.

Starting Week 2, AI tools come online. Lessons from that point can include AI-assisted workflows.

## Capstone

The capstone project is **Brand Lens** — a tool that helps Korean brands evaluate their US market presence using web scraping, structured data, and (eventually) LLM-generated briefs. Every day's lesson is designed to advance this project. Day 3 introduces fetching and saving a single URL; Day 4 extends it to batch processing with error handling. The capstone is the throughline — lessons should not introduce concepts without connecting them to it.

## Learner Profile

Business background, no prior coding experience. Explanations should avoid assumed knowledge of CS terminology. The "Friday review" is a 30-minute mentor session (with Vincent) where the learner demos their work and debugs a deliberately-broken version of their own code live — no AI allowed during this session.

## Lesson Format

Each `dayN.md` follows a consistent structure: goal + time budget → numbered sections with inline terminal commands → commit checkpoint → journal prompt → "what done looks like" checklist. Maintain this structure when adding new days. Journal prompts should ask for genuine reflection — name specific lines or concepts the learner can't yet explain — not just factual recall. Avoid vague framings like "what felt magical."
