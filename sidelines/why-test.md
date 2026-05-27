# Why Do Unit Tests?
*v1.0.0*

This is a sideline reference, not a paced lesson. Read it the moment writing a test feels like busywork — like you're typing the code twice, or proving something you can already see is true with your own eyes. That feeling is real, and it's worth answering head-on. Week 5's foundations day (W5D3) covered *what* a test is and *how* the pieces work. This is the other question: *why bother.* There's no time budget and nothing to commit. There's a short reflection prompt at the bottom.

---

## The honest objection first

You wrote `parse_page` in Week 2. You can read it. You can see it pulls the title out of the `<title>` tag. So when W5D1 asked you to write a test that hands `parse_page` some HTML and asserts the title comes back right — it can feel like you're being asked to prove that water is wet. You *wrote* the function. Of course it extracts the title. Why write a second piece of code to confirm the first piece does what you just watched it do?

Here's the thing the objection misses: **the test isn't for the function as it is today. It's for the function as it will be six weeks from now, after you and Claude Code have edited the file eleven times and you no longer remember what the original promise was.**

A test is a promise you make once and a machine keeps forever. That's the whole idea. Everything below is a version of it.

---

## Reason 1 — The alarm that doesn't depend on you remembering

W5D1 told this story: you accept a clean-looking diff, run the script once, it prints something reasonable, you commit. Two days later the canonical URL is always `None`. *When did that break?* You don't know, because nothing was watching.

A test is the thing that watches. You wrote `test_parse_page_extracts_canonical` on W5D2. From that moment on, every `pytest` run re-checks that promise — not because you remembered to, but because the test exists and pytest finds it. The alarm fires on the run *where the break happened*, not two days later when a brand's output looks wrong.

The value isn't in the test passing today. It's in the test failing on the one day, months from now, when something breaks the canonical extraction — and saving you the afternoon you'd otherwise spend bisecting commits trying to find out when.

## Reason 2 — This is how you trust Claude Code

This is the reason that matters most for *this* course, and it's why testing comes right after the Claude Code weeks instead of before them.

In Week 4 your only defense against a bad edit was reading the diff (W4D3). That defense has a hole you already know about: a diff can look completely reasonable and still break a behavior three functions away that the diff never touched. You can't catch that by reading. No human can.

A test suite can. When Claude Code hands you a 60-line diff that refactors `parse.py`, you don't have to hold the entire file's behavior in your head and reason about every consequence. You read the diff for *intent* — is it doing roughly the right thing? — and then you run `pytest`. If the suite stays green, every promise you wrote down still holds, including the ones the diff didn't seem to be near. If something went red, the failure message names the exact behavior that changed.

That's the trade that makes AI-assisted coding safe instead of scary: **you review the diff for intent, the tests verify the behavior.** Without tests, every Claude Code edit is an act of faith. With them, it's a checkable claim. The bigger the diff, the more the tests are doing for you. This is the single best argument for the work you did in Week 5, and it only gets stronger as the codebase grows past what you can hold in your head at once.

## Reason 3 — The tests are documentation that can't lie

W5D2 made this point and it's worth saying again from the motivation side. Read your test names top to bottom:

```
test_parse_page_extracts_title
test_parse_page_returns_none_when_title_missing
test_parse_page_extracts_meta_description
test_parse_page_returns_none_when_description_missing
test_parse_page_extracts_og_title
test_parse_page_picks_first_h1
test_parse_page_extracts_canonical
```

That list tells you what `parse_page` does *and* what it does when data is missing — without opening `parse.py`. A comment at the top of the file could say the same thing. The difference: a comment can be wrong. Someone changes the code, forgets the comment, and now the comment is a confident lie. A test that's wrong about the code *fails*. It can't drift, because the day it stops matching the code, pytest turns red and you fix one or the other.

This is why people say code without tests is a rumor. The code does *something*; the tests are the only record of what that something is supposed to be that the machine itself enforces.

## Reason 4 — Writing the test makes the code better

Notice something about Week 5: `parse_page` was easy to test and `fetch_one` was hard, and the reason was structural (W5D3 — pure vs side-effectful). That wasn't luck. W3D2 split the modules so the network-touching code (`fetch.py`) and the pure transform (`parse.py`) lived apart.

Here's the feedback loop: *the act of trying to test a function tells you whether it's well-built.* If a function is a pain to test — if you can't figure out how to call it without spinning up the whole pipeline, or you need to fake five things to get it to run — that's a signal the function is doing too much. The test difficulty is information about the design.

So testing isn't only insurance after the fact. Reaching for "what would a test for this look like?" *while* you write a function quietly pushes you toward smaller functions with clear inputs and outputs — the exact shape that's also easier to read, reuse, and hand to Claude Code with a clean instruction. The discipline pays twice.

## Reason 5 — It's faster than checking by hand, almost immediately

The break-even point is closer than it feels. Suppose you change `parse.py` and want to confirm you didn't break anything. By hand: run `runner.py`, open the JSON, scan the title, the description, the og:title, the h1, the canonical, for a couple of brands, eyeballing each. Two minutes if you're careful, and you'll skip a field eventually because you're human and it's tedious.

`pytest`: under one second, checks all seven promises, skips nothing, never gets bored. You wrote those seven tests once. You'll run them hundreds of times. The hour you spent in Week 5 buys back a few minutes every single time you touch the code — and it buys something you can't get by hand at all, which is the check running *automatically* on a change you didn't think to re-verify.

---

## When NOT to write a test

Tests are not free, and pretending they are is how you end up with a slow, brittle suite nobody trusts (W5D3 §7, flaky tests). Skip the test, or wait, when:

- **The code is a throwaway experiment.** A scratch script you'll delete this afternoon doesn't need a test. Tests are for code that lives.
- **The test would just restate the implementation.** A test that asserts "the function calls `soup.find` with these exact arguments" breaks every time you refactor, even when the behavior is identical. That's an implementation test, and it's worse than no test — it punishes you for improving the code. Test the *output*, not the *how* (this is the exact judgment W5D5 asks you to make on Claude's test).
- **The behavior genuinely isn't decided yet.** If you're still figuring out what a function should do, a test pins down an answer you haven't committed to. Write it once the behavior is real.
- **The cost of the bug is trivial and the cost of the test is high.** A test that needs forty lines of fake setup to verify a five-line helper that, if wrong, produces a slightly-off log message — that ratio doesn't pay. Judgment, not dogma.

The skill isn't "test everything." It's knowing which promises are worth writing down. The functions at the heart of brand-lens — `parse_page`, `fetch_one`, and whatever you build in Weeks 6 and 7 — are worth it. They're load-bearing, they'll be edited a lot, and they'll be edited by Claude Code, which is exactly the situation tests are built for.

---

## The shortest version

You will forget what your code was supposed to do. Claude Code will edit files you didn't expect it to touch. Both of those are certainties, not risks. A test is how today's understanding survives long enough to catch tomorrow's mistake — yours or the AI's — at the moment it happens, instead of two days later in a brand's broken output.

That's why you test. Not to prove the code works now. To find out, automatically, the day it stops.

---

## A reflection (no AI)

You answered two questions in your W5D5 retrospective: *where did a test feel unnecessary*, and *where did a test save you*. Come back to those after you've read this.

```
## Why test — second look
The moment a test felt unnecessary this week:
Reading the five reasons above, was it actually unnecessary, or was I just
not seeing the payoff yet? Which reason (1-5) speaks to that moment, if any?

The function in brand-lens I'd least want to refactor without a test:
Why that one? (Hint: which one would Claude Code be most likely to "improve"
in a way that quietly breaks something?)
```

The second question is the real one. The function you'd be *nervous* to let Claude Code rewrite is exactly the function that most deserves a test — because the test is what would let you say yes to the rewrite instead of no.

---

## Where to next

- The next time a test feels like proving water is wet, ask: *would I still be sure this works after Claude Code refactored the whole file?* If the honest answer is "I'd have to re-read it carefully," that's the test earning its place.
- When Claude Code proposes a diff, run the suite *after* you read it, every time. Make it a reflex, the way reading the diff became a reflex in Week 4.
- Weeks 6 and 7 add a database and live LLM calls — both side-effectful (W5D3 §3), both things you'll mock (W5D4) to keep tests fast and offline. The reasons here don't change; the techniques just stretch to cover new kinds of code.

Companion: [W5D3.md](../W5D3.md) — the mental model (units, pure vs side-effectful, the pyramid). This sideline is the *why*; that day is the *what* and *how*.
