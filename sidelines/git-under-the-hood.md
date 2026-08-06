# Git Under the Hood — Where It Came From, What It Actually Is, and the Commands Nobody Taught You
*v1.0.0*

This is a sideline reference, not a paced lesson. There's no time budget and nothing to commit except the drills.

You have been typing `git add`, `git commit`, `git push` since Week 1, Day 1. Roughly two hundred times by now. W10D3 gave you the working model — *a commit is a snapshot with a parent, a branch is a label pointing into the graph* — and that model is correct. This sideline goes underneath it and answers three questions that day left alone:

1. **Where did git come from,** and what was so bad about the tools before it that a kernel developer wrote a replacement in ten days?
2. **What is git, mechanically?** Not "a version control system" — what is the actual data structure on your disk, and why is it built out of cryptographic hashes?
3. **What else can it do?** You know four commands. Git ships about a hundred and fifty. Maybe twenty are worth your time, and about six of those will get you out of trouble you can't currently get out of.

Like the [relational-model](relational-model.md) sideline, this is built as **three sittings**, best taken on three different days, each ending in a short no-AI drill. Unlike that one, this sideline pays off immediately: Sitting 3 is a toolbox you'll use next week.

**Companion:** [W10D3.md](../W10D3.md) is the model. This is the machine under the model. Read that day first.

---

# Sitting 1 — Before Git: Forty Years of Getting It Wrong

## 1.1 The problem, stated plainly

Before you had git, you had the thing everyone has: a folder.

```
brand_lens.py
brand_lens_v2.py
brand_lens_v2_FIXED.py
brand_lens_v2_FIXED_final.py
brand_lens_v2_FIXED_final_USE_THIS_ONE.py
```

Every business person has lived this with spreadsheets and contracts; you've probably got a Dropbox folder shaped exactly like it right now. And it *sort of* works, until you need to answer one of these:

- Which version is on the server?
- What exactly changed between `final` and `USE_THIS_ONE`, line by line?
- Bill emailed me his edits and I edited the same file. Now what?
- This broke sometime in the last month. When? Which change did it?
- I need the version from March 3rd, and I need to be *sure* it's the one from March 3rd.

Version control is the software category invented to answer those five questions. The forty-year history below is the story of getting progressively better answers, and the punchline is that git's answers are so much better that it won the entire market.

## 1.2 SCCS and RCS (1972, 1982): one file at a time

The first real one was **SCCS** (Source Code Control System), Marc Rochkind at Bell Labs, 1972. Then **RCS** (Revision Control System), Walter Tichy, 1982 — cleaner, faster, and the one that set the mental furniture for the next twenty years.

The unit of work was **a single file**. You'd have `parse.py` and, sitting next to it, `parse.py,v` — a history file holding every past version. Not as full copies: as **deltas**. RCS stored the newest version whole and then a chain of *reverse diffs* — instructions like "to get from version 7 back to version 6, put these two lines back and delete that one." Storage was scarce, so history was stored as a recipe for reconstructing the past. **Remember the word delta.** It's the thing git throws away.

The second defining feature was **locking**. To edit a file you ran `co -l parse.py` — check out, locked. While you held the lock, nobody else could edit that file. They had to wait, or come find you.

This is what computer scientists call **pessimistic concurrency control**: assume a collision *will* happen, and prevent it by letting only one person touch the thing at a time. It's the same design as a bathroom key at a gas station. It works, and it does not scale — because the bottleneck isn't the file, it's the human holding the key, who has gone to lunch.

What RCS could not do at all:
- **Version a project.** There was no such thing as "the state of the whole codebase." There were only per-file histories, each with its own version numbers, which drifted out of alignment immediately.
- **Work over a network.** Everyone had to be on the same machine.

## 1.3 CVS (late 1980s): many files, and the death of the lock

**CVS** (Concurrent Versions System) started as a set of shell scripts over RCS by Dick Grune, rewritten in C by Brian Berliner around 1989. Two genuinely great ideas:

**It versioned a whole project, over a network.** One central server held the repository; developers anywhere could `cvs checkout` a working copy and `cvs commit` changes back. This is the **centralized model**, and it defined the next fifteen years.

**It killed the lock.** CVS let everybody edit everything simultaneously, and dealt with collisions *after the fact* — when you committed, if someone else had changed the same file, CVS tried to merge their change with yours automatically, and asked you to sort it out only when the same *lines* disagreed. This is **optimistic concurrency control**: assume collisions are rare, let everyone work, reconcile at the end.

That switch — pessimistic to optimistic — is one of the genuinely load-bearing ideas in this whole history, and it's the one you inherited without noticing. Every time you and a teammate work on the same file at the same time without arranging it first, you are cashing in Brian Berliner's bet from 1989.

But CVS was still RCS underneath, and it leaked badly:

- **Commits were not atomic.** A CVS commit touching five files was five separate per-file operations. If your network died halfway through, the repository was left in a state that had never existed on anyone's machine. Worse: another developer could check out *right then* and get three-fifths of your change. There was no repository-wide "version 47" to ask for, because there was no repository-wide anything.
- **Renaming a file destroyed its history.** CVS tracked files by path. Move `parse.py` to `scrape/parse.py` and, as far as CVS was concerned, one file died and an unrelated one was born.
- **Directories weren't versioned.** They just sort of existed.
- **Branching was agony.** A branch was a tag applied to every file individually. Creating one on a large project took minutes; merging one was a manual, error-prone ritual that teams scheduled and feared. *You avoided branching.* Hold that thought — it's why W10D1 had to argue for branches at all. For a generation of programmers, branching genuinely was expensive, and the caution outlived the cause.

## 1.4 Subversion (2000): CVS done right, and still centralized

**Subversion** (SVN) was started at CollabNet in 2000 with the semi-official slogan *"CVS done right."* Its own developers described the goal as a compelling replacement that CVS users could switch to without rethinking anything. It fixed the leaks:

- **Atomic commits.** A commit either fully happens or doesn't. The repository has a single, global, increasing **revision number** — r1, r2, r3 — and every revision is a complete, consistent snapshot of the whole tree. "We're running r4021" became a sentence you could say.
- **Real renames, real directories, real file metadata.**
- **Cheap branches** — sort of. SVN branching is *copying a directory*, by convention into `/branches/my-feature`, and the server is smart enough to make that copy lazily instead of duplicating data. Cheap to create. Still a pain to merge: until SVN 1.5 (2008) the system didn't *remember* what had already been merged, so merging the same branch twice re-applied changes and produced conflicts out of thin air. Teams kept spreadsheets of which revisions had been merged. I am not making that up.

But SVN kept the architecture that mattered most: **the server is the truth, and you have a working copy.** Which meant:

- **No network, no version control.** Committing required the server. On a plane, in a hotel, at a conference — you were reduced to a folder full of `_FINAL_v2` files, the very thing the tool existed to abolish.
- **`log`, `diff`, `blame` all hit the network.** Every question about the past was a round trip.
- **One machine held the only complete history.** Back it up or lose the company.
- **Committing was social.** Your commit went straight into everyone's shared reality, so you batched changes into big, rare, nervous commits instead of small honest ones. Note the shape of that incentive — it's precisely backwards from what W10D1 taught you.

## 1.5 April 2005: a license gets revoked and Linus writes a thing

By 2002 the Linux kernel had roughly 20,000 files and a firehose of patches from thousands of contributors arriving by email. Linus Torvalds had been merging them by hand and it was crushing him. CVS and SVN were, in his blunt assessment, unusable at that scale — chiefly because kernel development is *massively* branched, and branch-and-merge is exactly what those tools were worst at.

So the kernel adopted **BitKeeper**, a commercial distributed system by Larry McVoy's company BitMover. It was very good and it was **proprietary**. McVoy offered a free-of-charge license for open-source work, with a condition: you must not work on a competing tool, and you must not reverse-engineer the protocol. Plenty of people in the free software world hated the arrangement on principle from day one. It held anyway, for three years, because it worked.

In April 2005 it stopped holding. Andrew Tridgell — the Samba author, a man whose entire career is reverse-engineering protocols — began analyzing BitKeeper's. McVoy considered that a breach and withdrew the free license.

*Gentlemen, you can't fight in here — this is the kernel mailing list.*

The kernel was abruptly left with no version control system it could tolerate. Linus took two weeks off maintainership and wrote a new one:

- **April 3, 2005** — development begins.
- **April 7, 2005** — git is self-hosting. Git's own source code is being tracked in git.
- **April 16, 2005** — the first kernel merge lands in git, two weeks after the first line was written.
- **December 21, 2005** — version 1.0.

Independently, Matt Mackall started **Mercurial** that same month, from the same crisis, on nearly the same design principles. It's a fine tool and it lost, mostly for reasons of network effect and GitHub rather than technique.

Linus's stated design requirements are worth reading, because you can see every one of them in the machine you use today:

1. **Speed.** Common operations should feel instant. Not "fast for a version control system" — instant.
2. **Simple design.** The *data model* simple, even if the command line ended up famously not.
3. **Strong support for nonlinear development.** Thousands of parallel branches. Branching and merging must be cheap and reliable, not a scheduled event.
4. **Fully distributed.** No central anything.
5. **Efficient on very large projects.** The kernel was the benchmark, and it had to be fast on it.
6. **Cryptographic integrity.** History must be verifiable and tamper-evident, because the kernel is a target and its maintainer does not personally know the several thousand people sending him code.

That last one is the one nobody expects, and it's the one that shaped the internals. Sitting 2 is essentially the story of requirement 6.

> The name: Linus, on why he called it "git" — British slang for an unpleasant person. *"I'm an egotistical bastard, and I name all my projects after myself. First 'Linux', now 'git'."*

## 1.6 Distributed, and what it actually buys you

Here is the whole difference in one line: **`git clone` doesn't give you a working copy. It gives you the entire repository — every commit, every version of every file, all of history — on your disk.**

Not a checkout. A complete, independent, equal copy. Your laptop's clone of brand-lens is not a lesser thing than the copy on GitHub; it is the same thing. Nothing in git's design makes GitHub special. GitHub is a clone that you and Vincent have *socially agreed* to treat as the reference — a convention, not a mechanism.

Consequences, all of which you have been enjoying without noticing:

| | Centralized (SVN) | Distributed (git) |
|---|---|---|
| `commit` | network round trip to server | local, milliseconds |
| `log` / `diff` / `blame` | network round trip | local, instant |
| branch | server-side directory copy | writes a 41-byte file |
| working offline | you can't commit | fully functional |
| history backup | server is a single point of failure | every clone is a complete backup |
| someone else's experiment | needs server permission | they clone; you never even hear about it |

Two second-order effects matter more than the table.

**Committing stopped being a social act.** In SVN, committing published to everyone, so people hoarded changes into rare, large commits. In git, committing is *private*, and publishing is a separate step (`push`). That separation is the entire reason "commit early, commit often, in small honest steps" is the norm now. The advice didn't change because programmers got more disciplined. It changed because the cost changed.

**Open source got a new shape.** Cheap full clones plus cheap branches plus cheap merges is precisely the "fork it, change it, propose it back" model — and the pull request you opened in W10D2 is that model wearing a web interface. GitHub didn't invent the workflow; it put a button on a workflow that git's architecture had already made free.

---

### Drill 1 (no AI, ~20 min)

In `journal/git-1.md`:

1. Explain **pessimistic** vs **optimistic** concurrency control in your own words, using the RCS lock and the CVS merge as your two examples. Then name one non-software system you've used that works each way (booking systems, shared docs, and library books are all fair game).

2. CVS commits were not atomic. Describe a specific bad outcome — an actual sequence of events involving two developers — that this makes possible and that SVN's global revision numbers prevent.

3. In one paragraph: why did SVN developers avoid branching, and why do you not have to? Answer in terms of *cost*, not preference.

4. Linus listed six design goals. Pick the one you think is least obvious to a beginner, and argue for why it mattered enough to make the list.

5. **Predict, then check.** Before running anything: if GitHub deleted your brand-lens repository this afternoon, what exactly would you lose, and what would you still have on your laptop? Write the prediction. Then run `git log --oneline | wc -l` locally, with your wifi off, and note in an `### Edit` what actually happened and whether it surprised you.

---

# Sitting 2 — What Git Actually Is: A Content-Addressed Object Database

## 2.1 The one-sentence version

Git is a **key-value store where the key is the cryptographic hash of the value**, with a small directed graph built on top of it.

That's it. That's the whole system. Linus has described git as fundamentally a content-addressable filesystem with a version control interface written on top — and he meant it literally, not as an analogy. Everything in this sitting is unpacking that sentence.

## 2.2 Hashing, and why a version control system needs cryptography

A **hash function** takes an input of any size and produces a fixed-size fingerprint. Git (historically) uses **SHA-1**, which produces 160 bits — 40 hexadecimal characters. Those 40-character strings you've been copying out of `git log` are not IDs someone assigned. They are *computed from the content*.

The properties that make this useful:

- **Deterministic.** Same input, same output, always, on every machine on Earth, forever.
- **Avalanche.** Change one bit of input and roughly half the output bits flip. There is no "similar" — outputs are either identical or unrelated-looking.
- **One-way.** Given a hash you cannot work backwards to the content.
- **Collision-resistant.** Finding two different inputs with the same hash should be computationally infeasible.

Try it right now, in your brand-lens repo:

```bash
echo -n "hello" | git hash-object --stdin
```

```
b6fc4c620b67d95f953a5c1c1230aaab5db5a1b0
```

Run it again — same answer. Run it on a different machine in a different country — same answer. Change `hello` to `hellp` and you get something with no visible resemblance.

(If you're the sort to check: that's not quite the SHA-1 of the five characters `hello`. Git prefixes a small header first — the object type, a space, the byte length, and a zero byte — then hashes `blob 5\0hello`. Confirm it with `printf 'blob 5\0hello' | shasum`, which prints the same digest. A small detail with a real payoff: because the type and length are *inside* the hashed bytes, a blob and a tree with identical contents can never collide.)

**Content-addressable** is the term for a store where an object's name *is* its fingerprint. It has one immediate consequence worth pausing on: identical content is automatically stored exactly once. If forty files across ten commits contain the same license header, that's one object. Git gets deduplication for free, as a side effect of how it names things.

> **On SHA-1's retirement.** SHA-1 is cryptographically broken — in 2017 researchers at Google and CWI Amsterdam produced two different PDFs with the same SHA-1 hash (the "SHAttered" attack). Git responded with hardened collision *detection* (it refuses objects that show the fingerprints of a collision attack), and has had experimental SHA-256 repository support since 2.29 in 2020, not yet interoperable with the SHA-1 world. In practice a targeted attack on your repo is not your threat model; the migration is a slow, careful, decade-long affair. Worth knowing the ground is moving.

## 2.3 The four object types

Everything git stores is one of four kinds of object, all living in the same hash-keyed store.

**blob** — the contents of a file. Just bytes. No filename, no permissions, no timestamp. `parse.py` and `README.md` with identical contents are the *same blob*.

**tree** — a directory. A list of entries, each with a mode, a type, a hash, and a name. Trees point at blobs (files) and at other trees (subdirectories). This is where filenames live.

**commit** — a pointer to one tree (the complete state of the project at that moment), plus the hash of its parent commit(s), plus author, committer, timestamps, and message.

**tag** — an annotated tag: a name, a target, a tagger, a message, optionally a GPG signature.

Walk it yourself. In brand-lens:

```bash
git cat-file -p HEAD
```

```
tree 63d1aa9e18101665488a1760f26ce9b5ebb5443d
parent 1aaeba0badc49d9b3f2744d3cef68e1abceeed15
author Tyler Xu <you@example.com> 1783524118 -0700
committer Tyler Xu <you@example.com> 1783524118 -0700

add render.py: brand brief to HTML
```

That is a commit object, in full. Not a summary of one — that is the entire stored content. It's about 250 bytes of text. Now follow the tree hash:

```bash
git cat-file -p HEAD^{tree}
```

```
100644 blob a43f456770f36cd479972672285fc10e61383037	brands.yaml
100644 blob d6812cad81ec53cdb4b477993014fb2e6a4a10a1	fetch.py
100644 blob bd1dbac53a76aaedb3055dda253ef9562ed9144e	parse.py
040000 tree c078ff0ba688539e4f00586dae9fb79eba9e7dbf	templates
```

A directory listing. Note that `templates` is another tree — recursion, all the way down. And follow one more step:

```bash
git cat-file -p bd1dbac53a76aaedb3055dda253ef9562ed9144e
```

...and out comes the full source of `parse.py` as of that commit.

Three commands and you've read the raw database. There is no fifth thing. Branches, tags, the staging area, remotes — all of it is built from these four object types plus some small files holding hashes.

## 2.4 Snapshots, not deltas — the break with everything before

Here is git's sharpest departure from RCS, CVS, and SVN, and it's the one that makes it feel fast.

Those systems thought in **deltas**: a file's history is version 1 plus a chain of changes. To get version 30 you apply thirty patches. History is a recipe.

Git thinks in **snapshots**. Every commit names a tree, and that tree is the *complete* state of the project. Not the changes — the whole thing.

The obvious objection is storage: surely storing the entire project on every one of your two hundred commits is insane? It isn't, because of content addressing. If `parse.py` didn't change between commit 40 and 41, both trees list *the same blob hash*. Nothing is duplicated. Only genuinely-changed files produce new objects. You get delta-like efficiency out of deduplication, without delta *semantics*.

And you get something valuable in exchange. **Any version is one lookup away, not thirty patch applications away.** That's why `git checkout` of a three-year-old commit is instant, why `log` and `diff` and `blame` don't touch the network, why branching is free. Requirement 1 on Linus's list falls straight out of this one decision.

Git *does* still delta-compress, but at the **storage** layer, not the model layer. New objects start as individual "loose" files under `.git/objects/`. Periodically (`git gc`, which also runs automatically) git rolls thousands of them into a **packfile**, where similar objects — often successive versions of one file — are stored as deltas against each other, and the whole thing is zlib-compressed. Packfiles are also what travels over the wire on push and fetch.

The distinction is the point: **deltas are a compression detail git can change whenever it likes, because the model doesn't depend on them.** In RCS, deltas *were* the model.

## 2.5 The DAG, refs, and where the pointers actually live

W10D3 taught you that commits point at their parents. The formal name for the resulting structure is a **directed acyclic graph** — a DAG.

- **Directed** — edges have a direction. Child points to parent; never the reverse. Git cannot walk forwards. (This is why "what commits came *after* this one?" is a surprisingly expensive question, answerable only by walking backwards from every branch tip.)
- **Acyclic** — no cycles. You can never follow parent pointers in a circle back to where you started.
- **Graph**, not a tree or a list — a commit can have multiple children (a branch point) and multiple parents (a merge).

The acyclic part isn't a rule git enforces; it's *cryptographically impossible to violate*. A commit's hash is computed from its content, which includes its parents' hashes. To make an old commit point at a new one you'd have to know the new commit's hash before creating it — and the new one's hash depends on the old one's. You'd need to solve a hash equation that has no solution. The data structure can't be corrupted into a cycle even deliberately.

So where do branches live? Look:

```bash
cat .git/HEAD
```
```
ref: refs/heads/main
```

```bash
cat .git/refs/heads/main
```
```
f1bdfbeee44bfe4b81670d3d67e29f287d04db71
```

That's it. `main` is **a file containing 40 characters**. A branch is a text file with a hash in it. When W10D3 said "a branch is a pointer, not a copy," this is the literal pointer. Creating a branch writes 41 bytes. Deleting one deletes 41 bytes. This is why the "one branch per change, throw it away" advice is not wasteful — it's less data than a single line of your journal.

And `HEAD` is a file that says which branch file you're currently on. "Detached HEAD," that scary phrase, means only that `.git/HEAD` contains a raw commit hash instead of a `ref:` line — you're pointing at a commit directly, with no branch label along for the ride, so new commits won't move any label. Alarming name, tiny fact.

**A commit is reachable if you can get to it by following pointers backwards from some ref.** That word — *reachable* — is the one that governs what exists, what gets fetched, what survives garbage collection, and what "main doesn't have that commit" means.

## 2.6 The Merkle chain: why history is tamper-evident

Requirement 6, delivered. Follow the logic:

- A commit's hash is computed over its content.
- Its content includes its **tree** hash, which is computed over the whole directory listing, which includes every **blob** hash, computed over every file's bytes.
- Its content also includes its **parent's** hash — which was itself computed over that commit's tree and *its* parent. And so on to the root.

Therefore **the hash of the newest commit is a fingerprint of the entire history of the project.** Every byte of every file in every commit ever made contributed to that one 40-character string.

Change one character in a commit message from 2005 and that commit's hash changes; its child now names a parent that no longer exists, so the child must change, so *its* hash changes, all the way to every branch tip. You cannot quietly alter history. You can only produce an obviously different history, which every clone on Earth will immediately disagree with.

This structure — a hash tree where each node's hash covers its children's — is a **Merkle tree**, named for Ralph Merkle, who patented the idea in 1979. If it sounds familiar it's because it is the same construction underneath every blockchain. Git got there first, in 2005, for the same reason Bitcoin did in 2009: it needed a shared history that mutually-untrusting strangers could verify without trusting the server it came from. Linus wasn't being paranoid — he was accepting patches from thousands of people he'd never met, into software that runs most of the world's infrastructure. Signed tags (`git tag -s`) put a cryptographic signature on the tip of that chain, and thereby on all of it.

Trust the hash, and you have verified everything behind it. *I'm sorry, Dave. I'm afraid that commit hash doesn't match.*

## 2.7 The three trees — the model that unlocks the commands

This is the single most useful diagram in git, and it's the thing that makes `add`, `reset`, `restore`, and the `diff` variants stop being folklore. Git juggles three states of your project at once:

```
    HEAD                index                working directory
 (last commit)      (staging area)          (your actual files)
      │                   │                        │
      │◄──── commit ──────│                        │
      │                   │◄─────── add ───────────│
      │──── reset --mixed ────►│                   │
      │──────────── reset --hard ────────────────►│
      │                   │──── restore <file> ───►│
```

- **HEAD** — the commit you're on. What the last `commit` recorded.
- **The index** (a.k.a. the **staging area**, a.k.a. the cache) — not a metaphor. It's a real binary file at `.git/index`, holding a full proposed next tree: paths, hashes, and metadata. `git add` doesn't "mark a file for commit"; it writes the file's blob into the object store *right now* and records its hash in the index. **Your content is already safely in git's database the moment you `add` it,** before you ever commit. That fact rescues people.
- **The working directory** — the files you actually edit, the only part you can see in Sublime.

Now every confusing command becomes a sentence about which two of the three you're comparing or synchronizing:

| Command | What it does, in three-trees terms |
|---|---|
| `git diff` | working directory vs **index** — "what have I changed but not staged?" |
| `git diff --staged` | index vs **HEAD** — "what will the next commit contain?" |
| `git diff HEAD` | working directory vs **HEAD** — "everything I've changed, staged or not" |
| `git add <f>` | copy working directory → index |
| `git commit` | write index → a new commit; move HEAD |
| `git restore <f>` | copy index → working directory (throws away unstaged edits) |
| `git restore --staged <f>` | copy HEAD → index (unstages; keeps your edits) |
| `git reset --soft <c>` | move HEAD only. Index and files untouched |
| `git reset --mixed <c>` | move HEAD **and** index. Files untouched *(the default)* |
| `git reset --hard <c>` | move HEAD, index, **and** files. Your edits are gone |

Read that table twice. The `--soft`/`--mixed`/`--hard` distinction that eats hours of beginner time is just *how many of the three trees does the move drag along*, in order, from left to right.

## 2.8 Merging, computed

W10D3 told you a merge commit has two parents. Here's how git decides *what to put in it*.

Git finds the **merge base**: the most recent commit reachable from *both* branch tips. In graph terms, the lowest common ancestor of the two nodes in the DAG. You can ask for it directly:

```bash
git merge-base main add-site-footer
```

Then it does a **three-way merge**, comparing three versions of each file: the base, yours, theirs.

```
        base (common ancestor)
        /                  \
     mine                 theirs
```

Per region of the file:
- Changed on **neither** side → keep the base.
- Changed on **one** side only → take that side. (Git doesn't need to ask.)
- Changed on **both** sides, identically → take it.
- Changed on **both** sides, differently → **conflict.** Git writes both versions into the file with `<<<<<<<` markers and stops.

Why three-way rather than just diffing the two branches? Because the base tells you the *direction* of each change. Without it, "this function exists in mine and not in theirs" is ambiguous — did I add it, or did they delete it? Opposite situations, opposite correct answers. The common ancestor disambiguates. That's the whole trick, and it's why SVN's pre-1.5 inability to remember merge history was so damaging: without a reliable base, it kept re-asking questions that had already been answered.

**Rebase** is the other option, and now you can say exactly what it is: take each of your commits, compute its diff, and re-apply that diff on top of a different base commit — producing *new commits with new hashes*. Same changes, different parents, different identity. Which yields the golden rule, now derivable rather than memorized: **don't rebase commits you've already pushed.** The rebased commits are new objects; anyone who fetched the old ones has a history that no longer exists, and their next pull is a mess of duplicates. Rebase your own unpushed work freely — it's yours, nobody's fetched it.

## 2.9 Reachability, garbage, and why your work is almost never lost

Objects that no ref can reach are **garbage**, and `git gc` eventually deletes them. Amend a commit and the original is orphaned. Reset `--hard` past three commits and those three become unreachable. Delete a branch and its unmerged commits lose their label.

Except git keeps a **reflog**: a local, per-repository journal of every position `HEAD` and each branch has held, with timestamps. Unreachable-but-reflogged objects are protected from collection for 90 days by default.

Which means: for roughly three months, **anything you have ever committed is recoverable, even if every branch pointing at it is gone.** `git reflog` prints the list. Sitting 3 has the recipe. Bank that now — it is the difference between an afternoon lost and thirty seconds lost, and the single most common false belief among beginners is that they've destroyed work that is, in fact, sitting right there in the object store waiting to be named again.

---

### Drill 2 (no AI, ~25 min)

In `journal/git-2.md`. Run the commands in your brand-lens repo.

1. Run `git cat-file -p HEAD`, then `git cat-file -p HEAD^{tree}`, then `git cat-file -p <hash of one blob>`. Paste the three outputs and label, in your own words, what each object type stores and what it points at.

2. Explain **snapshots vs deltas** to someone who's used SVN. Include the storage objection ("surely that's wasteful?") and the answer to it, and name one capability the snapshot model buys you.

3. `cat .git/HEAD` and `cat .git/refs/heads/main`. Paste both. Then explain, in two sentences, what physically happens on disk when you run `git commit` — what gets created, what file gets rewritten.

4. Why is it *cryptographically impossible* to alter an old commit without altering every commit after it? Answer using the words **hash**, **parent**, and **content**.

5. **Predict, then run.** Predict the output of each, then run all three and record what you actually got:
   - `git diff` right after you edit a file but before `git add`
   - the same `git diff` right after `git add`
   - `git diff --staged` at that same moment

   If you predicted wrong, write an `### Edit` explaining which of the three trees you had misplaced. That edit is the actual deliverable of this question.

6. From the three-trees table: your last commit had a bad message *and* you want to keep the changes staged. Which reset flag, and why the other two are wrong.

---

# Sitting 3 — The Commands Nobody Taught You

You know `add`, `commit`, `push`, `pull`, `status`, `switch`, `log`, `diff`. That's a working vocabulary and it will carry you a long way. Below is the rest of what's worth knowing, grouped by *the problem it solves* rather than alphabetically — because the reason git's command line has a reputation is that its names were chosen by implementers describing the machine, not by teachers describing the task.

Everything in section A is read-only and cannot hurt you. Section B can, and is ordered by how much.

## A. Looking around (all safe, all local, all instant)

**`git log`, properly.** The default is nearly useless. These are not:

```bash
git log --oneline --graph --decorate --all   # the whole DAG as ASCII art. Learn this one.
git log --oneline -10                        # last ten, compact
git log -p parse.py                          # every change ever made to one file, with diffs
git log --stat                               # which files changed, how many lines each
git log -S"fetch_page"                       # the "pickaxe": commits where that STRING's count changed
git log --since="2 weeks ago" --author=Tyler
git log main..add-footer                     # commits on the branch that main doesn't have
```

`--oneline --graph --decorate --all` is the picture from W10D3, drawn from your real repository. Run it once a week and the graph model stops being abstract. `-S` is the one nobody knows and everybody needs: *"when did this function first appear / who deleted it?"* — it searches the *content of every diff in history*, not the current files.

**`git show`** — inspect any single object:

```bash
git show HEAD~2                  # that commit: message + full diff
git show HEAD~2:parse.py         # the FILE as it existed at that commit
git show main:brands.yaml        # ...on another branch, without switching
```

`<commit>:<path>` is worth memorizing. Compare a file across versions without checking anything out.

**`git diff`, the full matrix** (from the three-trees table in §2.7):

```bash
git diff                     # working dir vs index — unstaged changes
git diff --staged            # index vs HEAD — what's about to be committed
git diff HEAD                # everything, staged and not
git diff main add-footer     # tips of two branches, directly compared
git diff main...add-footer   # THREE dots: what the branch added since it diverged
```

The two-dot/three-dot distinction bites everyone. `main..branch` compares the two *tips*, so if `main` moved on, its new commits show up as "removed" in your diff — noise you didn't cause. `main...branch` uses the **merge base** (§2.8) and shows only what *your branch* did. Three dots is what a GitHub pull request shows you. When your PR diff and your local diff disagree, this is why.

**`git blame`** — who last touched each line, and in which commit:

```bash
git blame parse.py
git blame -L 40,60 parse.py      # just those lines
git blame -w -C -M parse.py      # ignore whitespace; see through moves and copies
```

Not for assigning fault — for finding the *commit*, whose message and diff explain why the line is like that. Follow it with `git show <that hash>`. And `git log -L 40,60:parse.py` gives you the full evolution of a specific line range over time, which is `blame`'s better-informed cousin.

**`git grep`** — search tracked files, fast, with history superpowers:

```bash
git grep "requests.get"              # in your working tree
git grep "requests.get" HEAD~20      # in the tree as of 20 commits ago
```

Faster than normal grep and it skips `.gitignore`d junk automatically — no results from `venv/`.

**`git shortlog -sn`** — commits per author, sorted. One line, and you can see the shape of a project's contributor base.

## B. Undo, in ascending order of danger

**Level 0 — `git restore` (throw away uncommitted work on purpose).**

```bash
git restore parse.py            # discard unstaged edits to this file (index → working dir)
git restore --staged parse.py   # unstage, KEEP the edits (HEAD → index)
git restore --source=HEAD~3 parse.py   # bring back an old version of one file
```

`restore` and `switch` were added in git 2.23 (2019) specifically to split up `git checkout`, which had been overloaded to do about five unrelated things and was the single largest source of beginner accidents. Old tutorials and Stack Overflow answers will tell you `git checkout -- file`. It still works. Use `restore`; the name says what it does.

**Level 1 — `git commit --amend` (fix the last commit).**

```bash
git commit --amend -m "day 51: render.py — brief to HTML"
git add forgotten_file.py && git commit --amend --no-edit   # add a file to the last commit
```

Remember §2.5: this doesn't edit a commit, it *replaces* it with a new one that has a different hash. Perfectly safe before you push. After you push, it's a history rewrite — see `--force-with-lease` below.

**Level 2 — `git revert` (the safe undo, and the only one for pushed work).**

```bash
git revert <sha>
```

Creates a **new** commit that applies the inverse of the old one. History is preserved; the bad change is neutralized. Nobody else's clone breaks. **This is the correct undo for anything already pushed to a shared branch**, and it's the one professionals reach for by default.

**Level 3 — `git reset` (move labels around, per the §2.7 table).**

```bash
git reset --soft HEAD~1    # undo the commit; keep everything staged (redo the message)
git reset --mixed HEAD~1   # undo the commit and unstage; keep your edits (the default)
git reset --hard HEAD~1    # undo the commit AND destroy the changes
```

`--soft` and `--mixed` never touch your files and are effectively safe. `--hard` deletes uncommitted work with no confirmation and no undo — that work was never hashed into the object store, so the reflog can't save it either. It is the only command in this document that can genuinely lose something forever.

*This is my `--hard`. There are many like it, but this one is mine. My `--hard` is my best friend. It is my life. I must master it as I must master my life.* Sitting with that for a moment is the appropriate amount of respect.

**Level 3 — `git clean` (delete untracked files).**

```bash
git clean -nd    # DRY RUN. Shows what would be deleted. Always run this first.
git clean -fd    # actually delete untracked files and directories
```

Useful for clearing generated `site/` output or stray `__pycache__`. Untracked means git has never seen it, which means git cannot get it back. `-n` first. Every time.

**Level −1 — `git reflog`, the undo for your undo.**

```bash
git reflog
```
```
f1bdfbe HEAD@{0}: reset: moving to HEAD~2
9a3c1de HEAD@{1}: commit: add render.py
1aaeba0 HEAD@{2}: commit: zero-pad week numbers
```

Every position HEAD has occupied, most recent first. So the recovery recipe, in full:

```bash
git reflog                       # find the hash from before the disaster
git reset --hard 9a3c1de         # go back to it
```

Deleted the wrong branch? Its tip is in the reflog: `git switch -c recovered 9a3c1de`. Botched a rebase? `git reset --hard HEAD@{5}`. Committed to the wrong branch? Reflog, cherry-pick, reset.

**If you take one command from this entire sideline, take this one.** The panic reflex — "I've destroyed everything" — is nearly always wrong, and `git reflog` is how you find out. It's local, it's private, and it remembers ninety days.

## C. Moving work around

**`git stash`** — you're mid-edit and need a clean tree *right now*:

```bash
git stash push -m "half-done footer"
git stash list
git stash pop            # re-apply the most recent and remove it from the stash
git stash apply          # re-apply but KEEP it in the stash (safer)
git stash -u             # include untracked files (they are NOT stashed by default — a classic surprise)
git stash show -p        # what's actually in there
```

A stash is a commit that isn't on any branch. Nothing exotic. Don't treat it as storage — stashes are easy to forget and easy to lose track of. Days, not weeks.

**`git cherry-pick <sha>`** — take *one* commit from somewhere else and apply it here:

```bash
git switch main
git cherry-pick 9a3c1de
```

For "that bug fix on the experimental branch is needed on main today, but the rest of the branch isn't." Makes a new commit with a new hash (same content, different parent). Don't build a workflow out of it — a branch you cherry-pick from repeatedly will conflict with itself when you finally merge it.

**`git rebase`** — replay your commits onto a new base:

```bash
git switch add-footer
git rebase main          # replay my branch's commits on top of current main
```

Yields a linear history with no merge commit — as if you'd started your branch from today's `main`. Prettier logs, less accurate record. Teams hold religious positions on this; both work.

**`git rebase -i HEAD~3`** — interactive rebase, the history-tidying tool. Opens an editor:

```
pick 1aaeba0 add render.py
squash 9a3c1de fix typo
reword f1bdfbe add tempaltes dir
```

Change `pick` to `squash` (fold into the previous commit), `reword` (edit the message), `drop`, `edit`, or reorder the lines. Six messy WIP commits become one clean one before you open the PR. **Only on unpushed commits** — §2.8's golden rule.

**`git switch`** — the modern branch-changer:

```bash
git switch main
git switch -c new-feature      # create and switch
git switch -                   # back to the previous branch (like `cd -`)
```

**`git worktree`** — genuinely underused. Check out a *second* branch into a *second directory*, sharing one repository:

```bash
git worktree add ../brand-lens-hotfix main
```

Now `../brand-lens-hotfix/` is `main`, live, while your current directory stays on your feature branch — no stashing, no switching, and both are usable at once. Perfect for "review this PR while I'm mid-change," or running the test suite on `main` while you edit a branch. (This is also what Claude Code does when it works in an isolated worktree.) Remove with `git worktree remove ../brand-lens-hotfix`.

## D. The one that will genuinely impress you: `git bisect`

You have a test suite. This is what it's *for*, in a way W05D3 couldn't show you yet.

The situation: `pytest` passes on a commit from three weeks ago and fails today. Somewhere in the 60 commits between, something broke. Reading 60 diffs is a bad afternoon.

`git bisect` does a **binary search over the commit graph**. It checks out the midpoint, you (or a script) say "good" or "bad," and it discards half the remaining range. 60 commits → **6 checks**. 1,000 commits → 10. That's log₂(n), the same reason a phone book beats reading every name.

Manually:

```bash
git bisect start
git bisect bad                 # current commit is broken
git bisect good HEAD~60        # this one was fine
# git checks out the midpoint; you test it, then:
git bisect good                # ...or: git bisect bad
# repeat ~6 times
git bisect reset               # back to where you started
```

And then the version that makes it a party trick — because your tests are automated, the machine can answer its own questions:

```bash
git bisect start HEAD HEAD~60
git bisect run pytest -q
```

Walk away. Come back to:

```
9a3c1de1... is the first bad commit
    add brand-name normalization to parse.py
```

The exact commit, the exact diff, in seconds, with no thinking. This is the single highest-leverage git command in existence, it works better the more disciplined your commits are, and it is the concrete cash value of the small-commits habit W10D1 asked you to adopt on faith. Real horrorshow, that.

## E. Remotes and syncing

**`git fetch` vs `git pull`.** `pull` = `fetch` + `merge`, immediately, into your working branch. `fetch` downloads and stops.

```bash
git fetch origin            # get the new commits, change nothing local
git log HEAD..origin/main   # what did they do that I don't have?
git merge origin/main       # NOW integrate, having looked
```

Fetching first is the habit that separates people who are surprised by merges from people who aren't.

**What `origin/main` actually is.** It's a **remote-tracking ref** — a local file, `.git/refs/remotes/origin/main`, holding a hash. It is a *cached memory of where GitHub's `main` was the last time you talked to GitHub.* Not live. It updates only on `fetch`/`pull`/`push`. So `git log origin/main` after two days offline shows you two-day-old information, cheerfully and without warning. Most "but git said it was up to date!" confusion is this.

```bash
git remote -v                 # where does origin actually point?
git push -u origin my-branch  # push and set upstream (so future `git push` needs no args)
git fetch --prune             # delete local origin/* refs for branches deleted on GitHub
git clone --depth 1 <url>     # shallow clone: latest commit only, no history. Big repos, CI.
```

**`git push --force-with-lease`.** After an amend or rebase, your local history and GitHub's have diverged, and a normal push is refused. Plain `--force` says "overwrite whatever's there, I don't care what it is" — and if a teammate pushed in the meantime, their work is gone. `--force-with-lease` says "overwrite it *only if* it's still exactly what I last fetched," and aborts otherwise. Same convenience, checks first.

Use `--force-with-lease`. Never plain `--force`. There is no third option and no situation where the extra safety costs you anything.

## F. Housekeeping

**Tags** — a permanent name for a commit, for releases:

```bash
git tag -a v1.0 -m "Brand Lens MVP — Week 8"
git push origin v1.0
git tag -l                    # list
```

A branch label moves as you commit; a tag doesn't. `v1.0` means that exact commit, forever. Tagging your brand-lens MVP is a reasonable thing to do the day it works.

**`git rm --cached`** — the "I committed something I shouldn't have" fix. You added `brands.db` to `.gitignore`, but git still tracks it, because *`.gitignore` only applies to files git isn't already tracking*:

```bash
git rm --cached brands.db     # stop tracking it; keep the file on disk
git commit -m "stop tracking the database file"
```

You met the shape of this in W06D1. Now you know the rule underneath it. (Note: this removes it from *future* commits. It's still in history. For a leaked API key, removing it from history is a much bigger operation — and you should assume the key is burned and rotate it regardless.)

**`git check-ignore -v <file>`** — "why is git ignoring this?" Prints the exact file and line number of the rule responsible. Thirty seconds instead of twenty minutes.

**`git config`**:

```bash
git config --list --show-origin        # every setting AND which file it came from
git config --global alias.lg "log --oneline --graph --decorate --all"
```

That alias gives you `git lg`. Make it your first one.

## The short list

If you remember six things from this sitting:

1. **`git reflog`** — you did not destroy your work.
2. **`git log --oneline --graph --decorate --all`** — see the actual DAG.
3. **`git bisect run pytest -q`** — the machine finds the breaking commit.
4. **`git revert`** — the correct undo for anything already pushed.
5. **`git diff --staged`** — see exactly what you're about to commit, before you commit it.
6. **`git push --force-with-lease`** — never plain `--force`.

---

### Drill 3 (no AI, ~30 min)

In `journal/git-3.md`, in your real brand-lens repo.

1. Run `git log --oneline --graph --decorate --all`. Paste the last ~15 lines. Point at one place where the graph shows a branch or a merge, and say which W10 day produced it.

2. Use `git log -S` to find the commit that first introduced a function you wrote in Week 3 (`fetch_page`, or whatever you named it). Paste the command and the commit it found. Then `git show` that commit and note one thing about your own code from Week 3 that you'd write differently now.

3. **Predict, then run.** Pick a commit ~10 back. Predict what each of these will print *before* running them, then run all three:
   - `git show HEAD~10 --stat`
   - `git show HEAD~10:runner.py | head -20`
   - `git diff HEAD~10 --stat`

   Write an `### Edit` for any prediction that was wrong, naming which of the three trees or which comparison you'd mis-mapped. Getting one wrong here is the point of the exercise.

4. Safely, deliberately, break and recover. On a scratch branch (`git switch -c reflog-practice`), make a commit, then `git reset --hard HEAD~1` to destroy it. Now recover it using `git reflog`. Paste your reflog output and the exact command that got the commit back. Then answer: what *couldn't* the reflog have recovered, and why?

5. In your own words, the difference between `git revert` and `git reset --hard` — including which one you'd use on a commit already pushed to `main`, and why the other would be antisocial.

6. `git diff main..my-branch` and `git diff main...my-branch` can print different things. Explain when and why, using the term **merge base**.

7. Set up the `git lg` alias from §F. Paste the config command and one line of its output. (Yes, this is the easiest question. It's here because you'll use it a thousand times.)

---

## What you can now say that you couldn't before

- **Historically:** version control went per-file-with-locks (RCS) → whole-project-over-a-network-with-optimistic-merging (CVS) → atomic-and-correct-but-still-centralized (SVN) → fully distributed (git), and each step was a response to a specific, nameable failure of the last. You can say why SVN teams feared branching and why you don't have to.
- **Structurally:** git is a content-addressed key-value store of four object types (blob, tree, commit, tag), keyed by the SHA-1 of their content, with a directed acyclic graph of commits on top. A branch is a 41-byte file. HEAD is a file naming a branch file.
- **Snapshots, not deltas** — the break with everything before it, why it isn't wasteful (content addressing dedups), and why it makes checkout, log, and branching instant.
- **Merkle chains:** a commit's hash covers its tree and its parents, so the tip hash fingerprints all of history, so history is tamper-evident. The same construction that later showed up under every blockchain, four years earlier, for the same reason.
- **The three trees** (HEAD / index / working directory) — the model that makes `add`, `commit`, `restore`, `reset --soft|--mixed|--hard`, and every `diff` variant one coherent idea instead of a dozen memorized incantations.
- **Three-way merge and the merge base** — why the common ancestor is what makes automatic merging possible at all, and why rebasing pushed commits is antisocial rather than merely frowned upon.
- **And practically:** reflog, bisect, revert, stash, cherry-pick, worktree, `-S`, `--force-with-lease`, and the difference between two dots and three.

The commands were always in `git --help`. What was missing was the model that makes the list read as a system rather than a hundred and fifty unrelated verbs. You have the model now.

## Where to next

- **Back to the work:** [W10D3.md](../W10D3.md) — reread it and watch the hand-wavy bits ("a branch is a pointer," "HEAD," "a merge commit has two parents") snap onto the files and hashes underneath them.
- **The same shape, one floor down:** [assembly](assembly.md) — a humane surface over a precise machine, told at the bottom of the stack instead of the side.
- **The same shape, in data:** [the relational model](relational-model.md) — a working technology sitting directly on a piece of clean mathematics.
- **If the curiosity takes hold:** *Pro Git* by Scott Chacon and Ben Straub is free at [git-scm.com/book](https://git-scm.com/book) and is the standard reference. Chapter 10, "Git Internals," is Sitting 2 of this document at four times the length and with all the details I left out. Read it the week you're feeling brave.
