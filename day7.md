# Day 7 — Foundations: HTTP, Types, and the Web Beneath the Code
*v1.0.0*

**Goal for today:** no new code. Today is about understanding what's actually happening when your scripts run. The hands-on work of Days 1–6 gave you patterns; today gives you the model the patterns sit on. Without the model, you'll plateau the moment your scripts get more complicated.

**Time budget:** ~75 min reading & quizzing, ~30 min journal, ~15 min slack.

**Rule for today:** no AI. Not Claude, not the AI summary at the top of Google, not Copilot. The mini-quiz at the end is for you to answer from memory and reasoning. Wrong-and-explained beats right-and-googled.

---

## 1. Why this day exists (~5 min)

You've made HTTP requests, parsed HTML, written to files, looped over lists, called functions. You can do these things, but if someone asked "what is HTTP, actually?" — you'd hesitate. That hesitation is fine on Day 7; it's not fine on Day 30. Today's job is to remove it.

There's no script to push tonight. The deliverable is a written journal entry with quiz answers. That's the work product.

## 2. The web in five layers (~15 min)

When you typed `requests.get("https://example.com")`, here's the actual chain of events:

**Layer 1 — Your script.** Python calls into the `requests` library. The library builds an HTTP request: a few lines of text describing what you want.

**Layer 2 — DNS lookup.** `example.com` isn't an address; it's a name. Your computer asks a DNS server: "what's the IP address for example.com?" Gets back something like `93.184.216.34`. (Try `dig example.com` in your terminal — it shows you exactly this.)

**Layer 3 — TCP connection.** Your computer opens a TCP connection to that IP address on port 443 (the standard port for HTTPS). TCP is a protocol that guarantees data arrives in order, without errors. You don't see this; the OS handles it.

**Layer 4 — TLS handshake.** Encryption gets negotiated. The server proves its identity with a certificate. From here on, everything sent over the connection is encrypted. (The "S" in HTTPS is this layer.)

**Layer 5 — HTTP request and response.** Your computer sends the HTTP request (a small block of text) over the encrypted connection. The server sends back a response (a status line, headers, and a body). The connection closes. `requests.get` hands you a response object.

That's it. Every webpage, every API call, every "the internet is down" frustration — all of it is layers 1–5.

## 3. What's actually in the response (~10 min)

When you wrote `response = requests.get(url)`, the `response` object has three things worth knowing about:

```python
response.status_code   # an integer like 200, 404, 500
response.headers       # a dict of metadata about the response
response.text          # the body — usually HTML for webpages
```

**`response.text` is the body of the HTTP response.** For a webpage, the body is the page's full HTML — every tag, every line of script, every `<div>`. It is *not* the title. The title is one tag inside the HTML body. To get the title, you parse the HTML with BeautifulSoup and ask for the `<title>` tag specifically:

```python
soup = BeautifulSoup(response.text, "html.parser")  # parse the body
title = soup.title.string                            # extract just the title tag
```

Read that twice. This is the misconception that crept into a lot of Week 1 journals — and it's one that gets expensive when scripts get bigger.

## 4. Status codes you'll actually see (~10 min)

The status code is a 3-digit number that tells you what happened. Whole books are written about them; you only need a handful:

| Code | Meaning | What you do |
|------|---------|-------------|
| **200** | OK | Use the body |
| **301 / 302** | Redirect | `requests` follows automatically by default |
| **403** | Forbidden | Site blocked you (often: you look like a bot) |
| **404** | Not found | URL is wrong or page is gone |
| **429** | Too many requests | You're being rate-limited; slow down |
| **500 / 502 / 503** | Server error | Their problem, not yours; retry later |

The 2xx range is success, 3xx is "go look elsewhere," 4xx is "you did something wrong," 5xx is "they did something wrong." That mental grouping is enough most of the time.

## 5. Types in Python (~15 min)

Every value in Python has a **type**. The common ones:

| Type | What it is | Example |
|------|-----------|---------|
| `str` | text (a "string") | `"hello"`, `"42"` |
| `int` | whole number | `42`, `0`, `-7` |
| `float` | decimal number | `3.14`, `0.5` |
| `bool` | true or false | `True`, `False` |
| `list` | ordered collection | `["a", "b", "c"]` |
| `dict` | key-to-value mapping | `{"name": "Tyler"}` |
| `NoneType` | the absence of a value | `None` |

You can check a type at runtime with `type(x)`. Try it in your venv:

```
python3
>>> type("42")
<class 'str'>
>>> type(42)
<class 'int'>
>>> "42" + 1
TypeError: can only concatenate str (not "int") to str
>>> int("42") + 1
43
>>> exit()
```

That `TypeError` is the one you hit on Day 2. `input()` always returns a string — even if the user types `99`. To compare it to 100 with `>`, you have to convert: `int(number) > 100`. Now you know *why*, not just *that*.

**`None` is a real type.** When `soup.title` doesn't exist on a page, `soup.title` returns `None`, and `None.string` raises `AttributeError`. That's why you wrote `if soup.title else "(no title)"` on Day 3 — to check for None before reaching for `.string`.

## 6. Mini-quiz (~20 min, no AI, no Google)

Open a new file: `journal/day7.md`. Answer each question from memory or reasoning. Wrong-and-explained beats right-and-googled.

1. In one sentence each: what does DNS do, what does TCP do, and what does TLS add on top?
2. If `response.text` is the full HTML body, what's the difference between `response.text` and `response.text[:500]`?
3. You hit a URL and get back `status_code = 403`. Whose "fault" is that — yours or the server's? What might be happening?
4. Predict the output (don't run it):
   ```python
   x = "5"
   y = 3
   print(x * y)
   ```
   What does this print? Why? (Hint: think about what the `*` operator does for each type involved.)
5. Why does `soup.title.string` crash on some pages but not others, and how did you guard against it on Day 3?

After you've written your answers, *then* you may run the code in question 4 to check.

## 7. Commit (~5 min)

Even though no script changed, the journal did:

```
git add journal/day7.md
git commit -m "day 7: foundations — http, types, mini-quiz"
git push
```

## 8. Journal — the rest of it (~10 min)

Below your quiz answers, add:

```
## What I don't fully understand
Pick one or two specific concepts from today — a layer of the web, a type, a status code — that still feel fuzzy. Be specific about which part is fuzzy.

## How my mental model changed
What did you believe before today that you now think differently about? One paragraph is enough.
```

Today's journal *is* the deliverable. Make it real.

---

## What "done" looks like for Day 7

- A `day7.md` journal exists with all five quiz answers, written from memory
- You can describe in plain language what `response.text` actually contains
- You can name at least four HTTP status codes and what each tells you
- You can explain why `int(number)` was needed in your Day 2 script
