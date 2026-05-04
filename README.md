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

## Friday review

The week ends with a 30-minute mentor session. The learner demos the work, walks through specific lines of code, and debugs a deliberately-broken version live (no AI). Starting Week 2, the learner also demos Claude on a snippet and judges whether the explanation is correct.
