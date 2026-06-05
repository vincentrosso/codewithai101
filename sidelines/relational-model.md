# The Relational Model — Databases from Set Theory Up
*v1.0.0*

This is a sideline reference, not a paced lesson. It's the longest and most abstract piece of writing in the course, and it answers a question W6D3 deliberately ducked. On that day, talking about why the brand name lives in `brands` and not in every `pages` row, the lesson said: *"You don't need the formal theory. You need the instinct."* That's true. You can build good schemas on instinct alone, the way you can drive a car without knowing what a camshaft does.

But there *is* a formal theory under that instinct, and it is unusually beautiful — one of the few places in computing where a working, billion-dollar technology sits directly on top of a piece of pure mathematics you could explain to a 19th-century logician. A database table is not *like* a mathematical object. It *is* one. This sideline walks from the bottom — what a set is — up to the SQL you've already been writing, and shows you that the whole tower is one idea wearing different clothes at each floor.

There's no time budget and nothing to commit. But it's built as **three sittings**, and you should take them on three different days. Each one ends with a short no-AI drill. Read it the way the assembly sideline suggests reading assembly: not because you'll use it Monday, but because seeing the floor changes how you stand on it.

> A warning, O my brothers: this is real horrorshow once it clicks, but it does not click on the first read of the first paragraph. If a definition slides off, keep going to the concrete brand-lens example underneath it — the example is where it sticks.

**Companion:** [W6D3.md](../W6D3.md) is the instinct version of everything here. That day is the *what*; this sideline is the *why it's true*. Read this after W6D3, not instead of it.

---

# Sitting 1 — Sets, Tuples, and the Thing a Table Actually Is

## 1.1 A set is the simplest container in mathematics

Forget databases for a moment. A **set** is just a collection of distinct things, with two rules and no others:

- **No order.** `{1, 2, 3}` and `{3, 1, 2}` are the *same set*. There is no first element.
- **No duplicates.** `{1, 2, 2, 3}` is not a thing — it's just `{1, 2, 3}`. An element is either in the set or it isn't; "in it twice" is meaningless.

That's the whole definition. You've already met it in Python, where it's a built-in type:

```python
>>> {1, 2, 3} == {3, 1, 2}
True
>>> {1, 2, 2, 3}
{1, 2, 3}
>>> "innisfree" in {"innisfree", "laneige", "cosrx"}
True
```

The last line shows the one operation a set is really *for*: **membership.** Asking "is this thing in the set?" is the fundamental question. Everything else is built from it.

The things in a set are its **elements** (or **members**). The number of them is the set's **cardinality**, written `|S|`. So `|{1, 2, 3}|` is 3. A set can be empty (`{}`, the **empty set**, written `∅`), it can be finite, or it can be infinite (the set of all whole numbers).

A few operations you'll need, all of which Python's `set` does too:

| Idea | Notation | Meaning | Python |
|------|----------|---------|--------|
| Union | `A ∪ B` | everything in either | `A \| B` |
| Intersection | `A ∩ B` | things in both | `A & B` |
| Difference | `A − B` | in A but not B | `A - B` |
| Subset | `A ⊆ B` | every element of A is also in B | `A <= B` |

Hold onto **subset** especially. It's the word that, four pages from now, will turn out to *be* the definition of a database table. Keep an eye on it the way you'd keep an eye on a gun mentioned in act one.

## 1.2 Why "no order, no duplicates" should sound familiar

Go back to W6D3. It made a quiet, important claim about tables: *"rows have no reliable order."* You don't ask for "the third row"; you ask for the row *where* `slug = 'innisfree'`. And the schema's `PRIMARY KEY` and `UNIQUE` rules exist precisely to forbid two identical rows.

No order. No duplicates. Those are not database conveniences someone bolted on. They are the *defining properties of a set* — and that is the first clue that a table is a set of something. The question is: a set of *what*? The rows aren't numbers like `{1, 2, 3}`. Each row is a little bundle — a url, a title, a status, all together. We need a mathematical object for "a bundle of values in a fixed order." That object is a tuple.

## 1.3 A tuple is a bundle — and order matters here

A **tuple** is an ordered, fixed-length sequence of values. Written with parentheses:

```
("https://innisfree.com", "Innisfree", 200)
```

Tuples are the opposite of sets in the two ways that matter:

- **Order matters.** `(200, "Innisfree")` is a different tuple from `("Innisfree", 200)`. The first slot means one thing, the second another.
- **Duplicates are fine.** `(200, 200)` is a perfectly good 2-tuple.

You've met these in Python too — it's the `(brand_slug,)` with the lonely trailing comma that W6D2 made you write, and the records you've been passing around since Week 2. A tuple with *n* slots is an **n-tuple**; the number of slots is its **arity**. The row above is a 3-tuple of arity 3.

So now we can say the shape of the thing precisely. A row is a tuple. A table is a *set of tuples* — unordered (the set part: no row is "first"), with no duplicate tuples (the set part again: `UNIQUE`/`PRIMARY KEY`), where each tuple is an ordered bundle of one value per column (the tuple part). 

That sentence is the entire relational model in one breath. The rest of Sitting 1 is just making each word in it exact.

## 1.4 Domains — where each slot's values are allowed to come from

A slot in a tuple isn't allowed to hold *anything*. The `status` slot holds whole numbers; the `url` slot holds text. The set of all legal values for a slot is called its **domain**.

- The domain of `status` is (roughly) the integers — call it `ℤ`.
- The domain of `url` is the set of all possible strings — call it `Strings`.
- The domain of `slug` is also `Strings`.

A domain is itself just a set. This matters because it lets us say, with full precision, what "all possible rows of this shape" means — which is the last brick we need.

## 1.5 The Cartesian product — every possible bundle

Here is the one genuinely new construction, and it's the keystone. Given two sets *A* and *B*, their **Cartesian product** `A × B` is the set of *every possible pair* you can make with a first element from *A* and a second from *B*:

```
A × B  =  { (a, b)  :  a ∈ A  and  b ∈ B }
```

Read the `{ … : … }` as "the set of all (a, b) *such that* a is in A and b is in B." That colon means "such that"; it's the standard way mathematicians describe a set by a rule instead of by listing it.

A tiny concrete one. Let `Severity = {light, heavy}` and `Title = {ok, missing}`. Then:

```
Severity × Title  =  { (light, ok), (light, missing), (heavy, ok), (heavy, missing) }
```

Four pairs — every way to combine one thing from the left with one thing from the right. (If you've ever seen a times-table grid, that's literally a Cartesian product: every row-value crossed with every column-value. The name "product" is not a coincidence — `|A × B| = |A| × |B|`, two times two is four.)

You can take the product of more than two sets, and that's where rows come from. The product `Strings × Strings × ℤ` is the set of *every conceivable 3-tuple* whose first two slots are strings and whose third is an integer:

```
("https://innisfree.com", "Innisfree", 200)     ✓ a member
("a",                     "b",          7)        ✓ a member
("qw…asdf",               "garbage",   -9999)     ✓ a member, too — it's *every* combination
```

That last line is the point and the catch. The Cartesian product contains every possible row, including the absurd, nonexistent, never-happened ones. It is unimaginably huge — infinite, in fact, since `Strings` is infinite. It is the set of all rows that *could* exist for this shape.

Your actual table is not that. Your table is the handful of rows that *do* exist. Which is to say:

## 1.6 The punchline: a relation is a subset of a Cartesian product

A **relation** is a subset of a Cartesian product of domains.

```
R  ⊆  D₁ × D₂ × … × Dₙ
```

That's it. That's the definition that gives "relational database" its name. Let it land slowly:

- `D₁ × D₂ × … × Dₙ` is the space of *all possible rows* of the right shape (every combination of legal values).
- A **subset** of it (`⊆` — the act-one gun from §1.1) is a *selection* of some of those possible rows: the ones that are actually true, that you actually stored.
- That selected subset is a **relation**. And a relation is exactly what a database calls a **table**.

Your `pages` table is a subset of `(all possible ids) × (all possible brand_slugs) × (all possible urls) × …`. Out of the infinitely many rows that *could* exist with that column shape, your table is the finite handful that describe real fetched pages. Choosing which tuples are in the subset *is* the act of recording data.

Read it once more as plain English: **a table is the set of rows you've decided are true, drawn from the set of all rows that could be.** The schema names the domains (the shape); the data is the subset (the content).

This is why, in §1.1, I told you to watch the word "subset." A table is a subset. The whole edifice — SQLite, PostgreSQL, the query you wrote on W6D2, the parking-ticket database at City Hall — is built on "a subset of a Cartesian product of sets." Nothing more exotic than that.

## 1.7 The vocabulary, lined up

Now the formal words and the database words are the same words, and you can map them cleanly. This table is worth committing to memory:

| Set theory | Database (SQL) | In `pages` |
|------------|----------------|------------|
| relation | table | `pages` |
| tuple | row | one fetched page |
| attribute | column | `url`, `status`, … |
| domain | column type | `TEXT`, `INTEGER` |
| cardinality (`\|R\|`) | number of rows | how many pages you've stored |
| degree / arity | number of columns | 11 for `pages` |
| subset | the stored data | the rows that exist |

Two of these have proper names worth knowing because people use them in conversation:

- **Cardinality** — the row count. "This table has high cardinality" means lots of rows.
- **Degree** (or **arity**) — the column count. `pages` is a relation of degree 11.

One refinement, so the pedants don't get you. In *pure* set theory a tuple's slots are identified by *position* (first, second, third), so order of columns would matter. Codd's actual model improves on this: it identifies slots by *name* (`url`, `status`) rather than position, so a relation is really a set of mappings from attribute-names to values — closer to "a set of dicts" than "a set of ordered tuples." That's why W6D3 could say a row is a dict and be right. The set-of-tuples picture is the cleaner one to *learn* the idea from; the set-of-named-mappings picture is the one Codd actually shipped. Same skeleton, one wearing labels.

## 1.8 A bit of history, told with the appropriate gravity

It is worth a paragraph of arch, Barry-Lyndon-ish hindsight to appreciate how recent and how *contingent* all this is. Before 1970, databases did not work this way. The dominant designs — IBM's IMS (hierarchical) and the CODASYL network model — stored data as records wired together with explicit pointers, rather like the nested `results.json` you started with. To get an answer you wrote a program that *navigated* the pointers by hand: go to this record, follow this link, loop, follow that link. The structure of your question was welded to the physical layout of the data on disk. Change the layout, rewrite every program.

In 1970 a mathematician at IBM named **Edgar F. ("Ted") Codd** published a paper with the dry title *"A Relational Model of Data for Large Shared Data Banks."* His move was audacious in its simplicity: *stop navigating, start describing.* Model the data as relations — sets of tuples, the objects you just met — and let people ask for what they want using operations from set theory and logic, while the machine works out how to fetch it. Separate the *question* from the *plumbing*. IBM, which was selling the pointer-based systems, was in no hurry to listen to him; it took most of a decade and a rival (a young Larry Ellison, who read Codd's paper and founded what became Oracle) before the idea became the industry. Codd got the Turing Award for it in 1981.

Every time you write `SELECT … WHERE …` instead of writing a loop that walks pointers, you are cashing the check Codd wrote in 1970. The reason your W6D2 questions were each one line instead of "another little program" (as W6D1 put it) is that he moved the navigation into the machine. Hold that thought; it's the whole content of Sitting 3.

---

### Drill 1 (no AI, ~15 min)

Open `journal/relational-1.md`. Reason these out from the definitions above; check with the `sqlite3` tool only *after* you've written your answer.

1. In one sentence each, say why a **set** has no order and no duplicates, and why a **tuple** has both. Then say which one a table's *rows* collectively form, and which one a *single row* is.

2. `Severity = {light, moderate, heavy}` and `Title = {present, missing}`. Write out every element of `Severity × Title`. How many are there, and what's the rule (`|A × B| = ?`) that told you the count before you listed them?

3. Finish this sentence in your own words, no notes: "My `pages` table is a **subset** of ______, and choosing which rows are *in* that subset is the act of ______."

4. **Predict before you check.** Your `pages` table — is its *degree* (column count) likely to change much over the life of the project, or its *cardinality* (row count)? Which one grows every time `runner.py` fetches a page? Run `SELECT COUNT(*) FROM pages;` to see today's cardinality, then in your journal say whether the number you guessed was high or low, and why. (Guessing wrong and writing the correction *is* the exercise — same habit W6D3 praised.)

---

# Sitting 2 — Keys and the Theorems Hiding Inside Your Instinct

Sitting 1 told you what a table *is*. Sitting 2 is about what makes a table *good* — and here's the payoff promised at the top: the "instinct" W6D3 gave you ("when a value is copied across rows, it wants its own table") is not folk wisdom. It is a precise theorem with a precise name (Third Normal Form), built on a precise idea (functional dependency). You already obey the theorem. Today you learn that it *is* one.

## 2.1 Functional dependency — "this determines that"

Start with the single most important relationship between columns. Look at `brands`:

```
slug         name
-----------  --------------------------
innisfree    Innisfree
laneige      Laneige
cosrx        COSRX
```

Knowing the `slug` tells you the `name`. There is exactly one `name` for `innisfree`; you cannot have two `brands` rows that agree on `slug` but disagree on `name` — the schema's `PRIMARY KEY` forbids it. We say:

```
slug → name        ("slug determines name")
```

This is a **functional dependency**, written `X → Y`. The arrow has a single, exact meaning, and it's worth stating formally because the whole rest of the sitting is built on it:

> `X → Y` holds when **any two tuples that agree on all the X values must also agree on all the Y values.**

"Functional" because it's like a function in the math sense: feed in an `X`, get back exactly one `Y`. Feed in `innisfree`, get back exactly one name. The dependency is a fact about *meaning*, not about the rows you happen to have stored today — `slug → name` is a promise that holds for every brand that ever could exist, which is why it belongs in the schema.

Some FDs are trivial (`slug → slug` — knowing slug obviously tells you slug). The interesting ones cross from one attribute to a different one. And the most interesting ones of all are the ones that determine *everything*:

## 2.2 Keys, defined properly at last

W6D3 told you a primary key is "a column whose value is unique to each row." True, but loose. With functional dependencies we can say it exactly, and the precision pays off immediately.

A **superkey** is a set of attributes that functionally determines *every* attribute in the relation. In `pages`, `id` alone determines every other column — give me the `id`, I can tell you the url, title, status, all of it. So:

```
id → (every column in pages)        →  id is a superkey
```

`url` is a superkey too, because of the `UNIQUE` constraint: no two pages share a url, so a url pins down exactly one row and therefore all its values. A relation usually has many superkeys (any set of columns containing a superkey is itself a superkey — `{id, title}` determines everything too, redundantly).

A **candidate key** is a superkey with no waste — a *minimal* one, where dropping any attribute would break the determination. `id` is a candidate key (you can't drop anything; it's a single column). `url` is a second candidate key. `{id, title}` is *not* a candidate key, because `title` is dead weight — `id` alone already does the job.

A **primary key** is simply the candidate key you *elect* to be the official identifier. `pages` has two candidate keys, `id` and `url`; the schema crowned `id` as `PRIMARY KEY` and left `url` as a `UNIQUE` (a candidate key not chosen). Nothing mathematical distinguishes them; the choice is a design judgment — W6D3's natural-vs-surrogate decision, now with proper vocabulary under it.

So the hierarchy, smallest to largest: **candidate key** (minimal determiner) → one is chosen as **primary key** → any superset is a **superkey**. A key is just a functional dependency whose left side is the whole row's identity.

## 2.3 The anomalies — what duplication actually costs, formally

W6D3 waved at this: copy the brand name into every page row and "data goes wrong." Let's make "goes wrong" exact, because the formalization is what justifies the normal forms in §2.4. Imagine the *bad* design W6D3 warned against — one flat table that jams brand info into every page:

```
pages_flat
url                       title       brand_slug   brand_name
------------------------  ----------  -----------  ---------------------
https://innisfree.com/a   Page A      innisfree    Innisfree
https://innisfree.com/b   Page B      innisfree    Innisfree
https://innisfree.com/c   Page C      innisfree    Innisfree
https://laneige.com/x     Page X      laneige      Laneige
```

`Innisfree` is written three times. That redundancy is not just untidy; it creates three distinct, named failure modes — the **anomalies** that are the entire reason normalization exists:

- **Update anomaly.** Innisfree rebrands to "Innisfree by Amorepacific." You must now update the name in *three* rows. Miss one, and your table literally contradicts itself: it asserts the brand is two different names at once. A database that can hold a contradiction is a database you can't trust.

- **Insertion anomaly.** You sign a new brand but haven't fetched any of its pages yet. In `pages_flat` you *cannot record the brand at all* — there's no row to put it in without a page. The brand's existence is held hostage to having a page. You'd have to invent a fake page or stuff a `NULL`-riddled row.

- **Deletion anomaly.** You delete the last page of a brand (say it went out of business in the US but you want to keep the brand on file). The brand's name vanishes with that last page row — you've lost a fact you didn't mean to lose, because the only place it lived was attached to a page.

These three are not separate problems. They are three symptoms of one disease: **a fact (`brand_slug → brand_name`) is stored in more places than it has business being.** The brand's name depends on the brand, not on the page — yet it's living in the page table, copied once per page. Cure the redundancy and all three anomalies vanish at once. The cure has a name.

## 2.4 Normal forms — the instinct, written as a ladder

A **normal form** is a precise rule a table either satisfies or doesn't. "Normalizing" a schema means restructuring it (usually by splitting tables) until it satisfies the rule. There's a ladder of them; you need to genuinely understand the first three. Each rung assumes the one below it.

**First Normal Form (1NF): every value is atomic — one indivisible value per cell, no lists, no nested tables.**

This forbids cramming a *list* of pages into a single brand cell, or storing `"200, 404, 200"` as one comma-mashed string in `status`. It's the rule that says your old `results.json` — brands *containing* a nested list of pages — is not a relation at all: a cell held a whole list of pages, not one atomic value. Flattening "one brand with three pages" into three rows, each atomic, is the move into 1NF. Your `pages` table is 1NF: every cell holds a single url, a single status, a single title. (This is also the formal content of "a table is flat" — relations don't nest. To represent "a brand has many pages" you don't nest; you use a second table and a foreign key. Sitting 3 shows why that's enough.)

**Second Normal Form (2NF): 1NF, plus no non-key column depends on only *part* of a composite key.**

This one only bites when your primary key is made of *several* columns together (a "composite key"). A non-key column must depend on the *whole* key, not half of it. `pages` has a single-column key (`id`), so 2NF is automatic — there's no "part of the key" to partially depend on. Know the rung exists; you'll meet it the first time you build a table keyed on two columns at once (say a `page_visits` table keyed on `(url, date)`, where storing `brand_name` would depend only on `url`, half the key — a 2NF violation).

**Third Normal Form (3NF): 2NF, plus no non-key column depends on *another non-key column*.**

Here is the rung that holds your instinct. Look again at the bad `pages_flat`. Its key is `url`. Trace the dependencies:

```
url → brand_slug        (a page belongs to one brand — fine, key determines a column)
brand_slug → brand_name (a brand has one name — and brand_slug is NOT the key!)
```

That second arrow is the crime. `brand_name` depends on `brand_slug`, and `brand_slug` is *not* a key — it's an ordinary non-key column (many pages share a `brand_slug`). So `brand_name` depends on the key only *indirectly*, by hopping through another non-key column. That hop is called a **transitive dependency** (`url → brand_slug → brand_name`), and 3NF forbids it.

The fix *is the decomposition you already did.* Pull the transitively-dependent stuff into its own table, keyed by the thing it really depends on:

```
brands(slug PK, name)              -- brand_slug → brand_name now lives here, where slug IS the key
pages(id PK, brand_slug → brands.slug, url, title, …)  -- pages keeps only a pointer
```

That is precisely the `brands` / `pages` split from W6D1. You did 3NF normalization in Week 6 and called it "giving the brand its own table." The foreign key `pages.brand_slug → brands.slug` is the seam of the decomposition. **The instinct and the theorem are the same act.**

There's a famous one-line gloss of 3NF, and now you can read it as a sentence rather than a spell. Every non-key column must depend on:

> **the key, the whole key, and nothing but the key.**

- *the key* — depend on the primary key, not float free (that's having a key at all).
- *the whole key* — not just part of a composite key (that's 2NF).
- *nothing but the key* — not on some *other* non-key column (that's 3NF, the transitive-dependency ban).

Three clauses, three normal forms. (There's a tighter rung above 3NF called **Boyce–Codd Normal Form (BCNF)**: *every* functional dependency's left side must be a superkey, no exceptions even for key columns. For almost everything you'll build, a clean 3NF schema is already BCNF, and chasing past BCNF — 4NF, 5NF — is a specialist's errand. Know the names exist; spend your attention on 1NF through 3NF, which is where 99% of real schema bugs live.)

## 2.5 The map: instinct ⇄ theorem

The single most useful thing to carry out of this sitting:

| W6D3 said (instinct) | The theory says (formal) |
|----------------------|--------------------------|
| "name the row by a unique column" | candidate key: a minimal set of attributes that functionally determines all others |
| "the slug determines the brand" | the functional dependency `slug → name` |
| "don't copy a value across many rows" | eliminate redundancy to kill update/insert/delete anomalies |
| "when a value repeats, it wants its own table" | Third Normal Form — remove the transitive dependency |
| "the foreign key points at the one real copy" | the seam of a lossless 3NF decomposition |
| "a table is flat, not nested" | First Normal Form — atomic values only |

You were never operating on hunches. You were obeying theorems you hadn't been shown. That's the most reassuring thing this sideline has to tell you: good design *feels* like taste, but it's standing on math, and the math is checkable.

---

### Drill 2 (no AI, ~15 min)

In `journal/relational-2.md`:

1. Write the functional dependency `X → Y` for each, using real `pages`/`brands` columns: (a) "every page has exactly one status code," (b) "every brand has exactly one display name." Which side is the determiner?

2. `pages` has **two** candidate keys. Name both, and explain in one sentence each why each one functionally determines every other column. Which did the schema elect as `PRIMARY KEY`, and which is the un-chosen one wearing a `UNIQUE` badge?

3. Take the bad `pages_flat` table from §2.3. Invent a concrete, specific bad outcome for **each** of the three anomalies (update, insert, delete) using Innisfree or a brand of your choosing — your own example, not the one in the text.

4. **Predict, then reason.** Before re-reading §2.4: is your *real* `pages` table in 3NF? Write yes/no and one sentence of why. Then check your reasoning against the "the key, the whole key, and nothing but the key" gloss and edit your answer if you were wrong. (If you find a column in `pages` that depends on a non-key column, you've found a 3NF violation worth raising with Vincent — but look closely first.)

---

# Sitting 3 — Relational Algebra, and Where SQL Quietly Bends the Math

Sittings 1 and 2 were about data *at rest* — what a table is, what makes it well-formed. Sitting 3 is about data *in motion*: what happens when you ask a question. And it closes the loop, because the punchline is that the SQL you wrote on W6D2 is a thin coat of paint over a tiny algebra of set operations. Same move as the assembly sideline — peel one layer and find the next one down, all the way to the floor.

## 3.1 An algebra whose values are tables

You know algebra as a thing you do with numbers: take two numbers, `+` gives you a number back; `×` gives you a number back. The operations are *closed* — number in, number out — so you can chain them: `(2 + 3) × 4`.

**Relational algebra** is the same idea, but the values are *relations* instead of numbers. Each operation takes one or two tables and returns a *new table*. Because table in, table out, you can chain them — feed one operation's output straight into the next. That closure is the entire trick, and it's what makes a complex query just a stack of simple operations.

There are only a handful of operations. Three do almost all the work, and they map one-to-one onto SQL keywords you already type.

**Selection (σ) — keep some rows.** Written `σ_condition(R)`: from table `R`, keep only the rows where the condition is true, drop the rest. Columns unchanged, rows thinned.

```
σ_status=200 (pages)        keeps only the pages that returned HTTP 200
```

That is exactly your SQL `WHERE`:

```sql
SELECT * FROM pages WHERE status = 200;
```

(A naming trap worth flagging once: in relational algebra, "selection" means *picking rows*. In SQL, the keyword `SELECT` mostly picks *columns*. They're nearly opposite. Blame fifty years of accreted vocabulary; just hold the two meanings apart.)

**Projection (π) — keep some columns.** Written `π_columns(R)`: keep only the named columns, discard the others. Rows unchanged in count*, columns thinned.

```
π_url, title (pages)        keeps only the url and title of every page
```

Which is SQL's column list:

```sql
SELECT url, title FROM pages;
```

(*The asterisk: in *pure* algebra, projection also removes any duplicate rows the column-dropping created, because the result is a set and sets can't hold duplicates. SQL, by default, does *not* — it keeps the duplicates unless you write `SELECT DISTINCT`. File that under §3.4, where SQL stops being a perfect set.)

**Join (⋈) — stitch two tables together.** Written `R ⋈ S`: combine the rows of two tables wherever they match on shared values. This is the formal version of W6D3's "follow the foreign-key pointer." It's actually built from two even simpler steps:

1. Take the **Cartesian product** `pages × brands` — yes, the very operation from §1.5. Every page paired with every brand. A 40-page, 5-brand database produces 200 nonsense pairs, most of them mismatched (Innisfree's page glued to Laneige's brand row).
2. **Select** (σ) only the pairs that actually match: `σ_pages.brand_slug = brands.slug`. The nonsense pairs fall away; what remains is each page next to *its own* brand.

So a join is *just* "Cartesian product, then keep the rows that line up." Which is, letter for letter, the SQL you wrote on W6D2:

```sql
SELECT * FROM pages JOIN brands ON pages.brand_slug = brands.slug;
```

The `ON pages.brand_slug = brands.slug` is the selection condition; the `JOIN` is the product. The product builds every possible pairing (Sitting 1's Cartesian product, doing real work at last); the `ON` keeps the true ones (Sitting 3's selection). The foreign key from W6D3 is what guarantees each page's `brand_slug` finds exactly one matching `brands.slug`.

The remaining operations are the plain set operations from Sitting 1, lifted to tables of the same shape: **union** (`∪`, SQL `UNION`), **intersection** (`∩`, SQL `INTERSECT`), **difference** (`−`, SQL `EXCEPT`). They're the rows-in-either, rows-in-both, rows-in-A-not-B operations you already met on numbers, now on tables. Set theory, still load-bearing, three floors up.

## 3.2 A real query, dissolved into algebra

Take a question with some meat on it — the kind W6D2 had you write:

> "The display names of all brands that have at least one successfully-fetched page."

In SQL:

```sql
SELECT DISTINCT brands.name
FROM pages
JOIN brands ON pages.brand_slug = brands.slug
WHERE pages.status = 200;
```

Now watch it decompose into three algebra operations, applied inside-out — each one's table-shaped output feeding the next, exactly the closure from §3.1:

```
π_name ( σ_status=200 ( pages ⋈ brands ) )
└─pick─┘ └──keep 200──┘ └────join─────┘
 column      rows         the two tables
```

1. `pages ⋈ brands` — join: every page sitting beside its own brand.
2. `σ_status=200(…)` — selection: throw away the rows whose page didn't return 200.
3. `π_name(…)` — projection: keep only the brand name column.

Three operations, nested. The SQL keywords (`JOIN … ON`, `WHERE`, `SELECT`) are a *surface syntax* — a friendlier order of writing — for that nested stack of `⋈`, `σ`, `π`. When you wrote that query on W6D2 and it "just worked," this is the machinery it compiled down to. The query is to the algebra what your Python is to bytecode in the [assembly sideline](assembly.md): a humane layer over a precise, smaller machine underneath.

## 3.3 Declarative vs imperative — Codd's actual gift

Here's why this matters beyond elegance, and it pays off the history from §1.8. Notice what your SQL query did *not* say. It never said *how* to compute the answer. It didn't say "loop over pages, for each one look up its brand, check the status, collect the names." It only described *what* the answer is — a true description, in algebra-backed logic — and left the *how* to the database.

That's the distinction between **declarative** ("describe what you want") and **imperative** ("spell out each step"), and it's the entire reason the relational model won. With the old pointer-navigation databases from §1.8, you wrote the *how* by hand, welded to the data's physical layout. With Codd's model, you write the *what*, and a component called the **query planner** decides the *how* — which table to scan first, whether to use that index W6D3 mentioned, what order to join in. It can pick a *different* plan tomorrow if the data grows, without you changing a character of the query.

The relational algebra is what makes that possible: because every query is provably equivalent to some expression in this tiny algebra, and because the algebra has *laws* (e.g. `σ` can often slide inside a `⋈` to filter rows *before* the expensive join instead of after — "selection pushdown"), the planner can shuffle the operations into a faster-but-equivalent order, guaranteed to give the same answer. You get to think in *what*; the machine optimizes the *how*; the algebra is the contract between them that keeps the answer correct. That is Codd's 1970 promise, made of set theory, running every time you hit Enter.

## 3.4 Where SQL is *not* pure set theory — the honest footnotes

Engineering always compromises the math, and SQL is no exception. Three places where the tables you actually use depart from the clean relations of Sitting 1 — and each one is a wrinkle you've already half-noticed:

**SQL tables are bags, not sets.** A true relation has no duplicate tuples (Sitting 1). But run `SELECT title FROM pages;` and if two pages share the title "Home," you get "Home" *twice*. SQL allows duplicate rows in query results — it's working with **bags** (also called *multisets*: collections that, unlike sets, *do* count duplicates). That's why `DISTINCT` exists at all: it's the button that forces a bag back into a true set. The pure-relational default would be no duplicates ever; SQL chose duplicates-by-default for speed (removing them costs work) and made you opt into set-purity with `DISTINCT`. Your base tables stay duplicate-free because their keys forbid duplicates — but query *results* are bags.

**Columns are ordered, and you can ask by position.** In Sitting 1's clean model, attributes are named and unordered. SQL lets `SELECT *` lean on column *order* and even lets you `ORDER BY 1` (meaning "the first column"). A small impurity, mostly harmless, occasionally a footgun when someone reorders columns and a positional reference silently shifts.

**NULL breaks two-valued logic — and this is the formal answer to a W6D3 mystery.** W6D3 left you with a rule you had to half-take-on-faith: `WHERE description = NULL` returns nothing, and you must write `IS NULL` instead. It explained: "SQL treats NULL as unknown, and any comparison with unknown is itself unknown — not true." That explanation was the *whole truth in disguise*. Pure relational logic is **two-valued**: every condition is either TRUE or FALSE, full stop — the crisp world of Sitting 1's sets, where a tuple is in or out. NULL shatters that. To represent "value unknown / not applicable," SQL adopts **three-valued logic** (after the logician Stephen Kleene): every condition evaluates to TRUE, FALSE, *or* **UNKNOWN**. And the rules of this third logic are exact:

- `200 = NULL` → **UNKNOWN** (not FALSE — you genuinely can't say, because NULL might be anything).
- `NULL = NULL` → **UNKNOWN** (two unknowns might or might not be equal; you can't claim they're equal).
- `WHERE` keeps a row only when its condition is *exactly* **TRUE** — and UNKNOWN is not TRUE, so the row is dropped.

That is the entire reason `WHERE description = NULL` returns nothing: the comparison is UNKNOWN for every row, UNKNOWN is not TRUE, every row is dropped, you get the empty set. `IS NULL` exists as a separate operator precisely because it's the one test that returns honest TRUE/FALSE about nullness instead of collapsing to UNKNOWN. So the small annoyance W6D3 told you to memorize is the visible tip of a whole alternate logic that the presence of "unknown" forces on the system.

> Codd himself argued NULL was a necessary evil and three-valued logic the price of admitting "we don't know" into a database. Critics (notably C. J. Date) argue it was a mistake that bends the clean two-valued algebra out of true. The debate is decades old and unresolved. *I'm sorry, Dave — I'm afraid `NULL = NULL` can't be `True`.* HAL would have understood three-valued logic perfectly; it's the rest of us who trip over it.

These three departures don't demolish the theory — your tables are still relations, your queries still compile to algebra, the model still holds. They're the seams where a clean piece of mathematics got tailored to fit a fast, practical, imperfect world. Knowing where the seams are is what separates someone who *uses* a database from someone who *understands* one.

---

### Drill 3 (no AI, ~20 min)

In `journal/relational-3.md`:

1. For each SQL fragment, name the relational-algebra operation (`σ`, `π`, or `⋈`) it corresponds to: (a) `WHERE status = 404`, (b) `SELECT url, title`, (c) `JOIN brands ON pages.brand_slug = brands.slug`.

2. Take this query and, like §3.2, write it as a nested algebra expression `π…(σ…(…))` and label each layer: *"the urls of every Innisfree page that failed to fetch."*
   ```sql
   SELECT pages.url FROM pages JOIN brands ON pages.brand_slug = brands.slug
   WHERE brands.slug = 'innisfree' AND pages.status != 200;
   ```

3. In your own words: a `JOIN` is built from two simpler operations. Name them, and explain why the Cartesian-product step alone (without the second step) would give you mostly *wrong* page-brand pairings.

4. **Predict, then run.** Before executing: will `SELECT title FROM pages;` and `SELECT DISTINCT title FROM pages;` ever return a *different number of rows*? Say when they would and when they wouldn't, in terms of "bag vs set." Then run both and edit your journal with what you saw and whether your prediction held.

5. The closing one, the one that ties the three sittings together: explain, in three or four sentences, why `WHERE description = NULL` returns no rows — using the words **three-valued logic**, **UNKNOWN**, and **TRUE**. This is the W6D3 rule you took on faith, now derived from first principles. If you can write this paragraph cleanly, you've earned the whole sideline.

---

## What you can now say that you couldn't before

You came in able to *write* a `SELECT … JOIN … WHERE`. You leave able to say what each of those words *is*:

- A **table** is a relation: a subset of a Cartesian product of domains — the set of rows you've decided are true, out of all the rows that could be.
- A **key** is a functional dependency whose left side identifies the whole row; **normalization** (1NF–3NF) is the discipline of storing each fact exactly once, and "the key, the whole key, and nothing but the key" is its slogan.
- A **query** is an expression in a small algebra of set operations (`σ`, `π`, `⋈`); SQL is a declarative surface over it, and the query planner turns your *what* into a fast *how*.
- **NULL** drags the whole thing from two-valued into three-valued logic, which is the real reason for `IS NULL` — and the one genuine seam where the clean math meets the messy world.

None of this makes you faster at writing queries tomorrow. That's not what it's for. It's for the day a schema decision feels arbitrary and you realize it isn't — there's a theorem under it — and for the slightly larger respect you'll have for the fact that a 1970 math paper is quietly running underneath every `SELECT` you'll ever type. Real horrorshow, that.

## Where to next

- **Back to the work:** [W6D3.md](../W6D3.md) is the instinct version of all of this — reread it now and watch the hand-wavy parts ("rows have no order," "don't copy values," "NULL is unknown") snap into the formal ideas they were standing in for.
- **The layer below SQL's friendliness** is the same shape as the layer below Python: [assembly](assembly.md). Both sidelines are the same lesson — a humane surface sitting on a precise machine — told at opposite ends of the stack.
- **If the curiosity really takes hold:** the readable classic is *Database Design and Relational Theory* by C. J. Date (opinionated, rigorous, and not shy about where SQL bends the math). For the source itself, Codd's original 1970 paper, *"A Relational Model of Data for Large Shared Data Banks,"* is ten pages and more readable than its title — worth a look once just to see where the whole thing started. Save either for when the itch hits; neither is homework.
