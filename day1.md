# Day 1 — Setup & First Contact

**Goal for today:** by the end of two hours, you have a working development environment, a GitHub account, and you've written and run your first Python program. Nothing fancy — just the toolchain working end to end and you knowing where everything lives.

**Time budget:** ~90 min hands-on, ~15 min journal, ~15 min slack/buffer.

---

## About AI tools this week

You'll be using AI assistants (Claude, ChatGPT, Cursor, Copilot) heavily starting Week 2 — they're a core part of this curriculum. But not this week. Week 1 is for building the muscle that lets you evaluate AI output later. If you can't read an error message without help now, you won't catch the AI making mistakes later.

Sit with the discomfort. Google is allowed (and encouraged — knowing how to search is a skill). Stack Overflow is allowed. AI assistants are off-limits until Monday of Week 2.

---

## 1. Install Sublime Text (~5 min)

Go to sublimetext.com, download the macOS build, open the .dmg, drag Sublime Text to your Applications folder. Open it once so macOS registers it as a known app. Don't configure anything yet — we'll come back to it.

## 2. Install Ghostty (~5 min)

Go to ghostty.org, download, drag to Applications, open it. This is your terminal — for the rest of this course, when something says "in the terminal," it means here. Take a second to notice what you're looking at: a blank screen with a prompt. That prompt is waiting for a command. That's the whole interface.

## 3. Remap Spotlight to Option-Space (~5 min)

Spotlight is already installed — it's the macOS built-in launcher. By default it's bound to Cmd-Space, but Option-Space is a more comfortable reach and frees Cmd-Space for other things later.

Open System Settings → Keyboard → Keyboard Shortcuts → Spotlight. You'll see "Show Spotlight search" with Cmd-Space next to it. Click the shortcut, then press **Option-Space**. Close settings.

Now test it: hit Option-Space, type `subl`, hit return — Sublime should open. Hit Option-Space again, type `ghos`, hit return — Ghostty opens. Practice this four or five times with different apps until your fingers know the motion without thinking.

The rule for the rest of this course: **don't use the dock, don't use Finder to open apps**. Option-Space, type, return. Keyboard fluency is the foundation everything else sits on, and breaking the mouse habit early is worth the small friction now.

## 4. Mac terminal basics in Ghostty (~20 min)

In Ghostty, run each of these and notice what happens. Don't just copy-paste — type them.

```
pwd          # "print working directory" — where am I?
ls           # list files in this directory
ls -la       # list everything, including hidden files, with details
cd ~         # go to home directory (~ means home)
cd Desktop   # go into Desktop
cd ..        # go up one level
cd -         # go back to where I just was
```

Now make and destroy some stuff:

```
mkdir test-folder
cd test-folder
touch hello.txt
ls
echo "some text" > hello.txt
cat hello.txt
mv hello.txt renamed.txt
ls
cd ..
rm -r test-folder
```

A few tricks: **Tab** auto-completes filenames and commands. **Up arrow** cycles through previous commands. **Cmd-K** clears the screen. **Ctrl-C** cancels whatever's running. Use these constantly — typing full filenames repeatedly is a beginner tell.

## 5. Set up your development directory (~10 min)

```
cd ~
mkdir dev
cd dev
mkdir brand-lens
cd brand-lens
mkdir journal
pwd
```

That last `pwd` should print `/Users/yourname/dev/brand-lens`. From now on, all your code lives under `~/dev/`, and the capstone project lives in `~/dev/brand-lens/`. When in doubt, `cd ~/dev/brand-lens` gets you home.

## 6. Verify Python is installed (~5 min)

Run:

```
python3 --version
```

If you see something like `Python 3.9.6` or higher, you're done. If macOS pops up a dialog asking to install "Command Line Developer Tools," click Install and let it run — it'll take 5–10 minutes. Use that wait time for the next step.

## 7. Create a GitHub account (~10 min)

Go to github.com, sign up. Two notes:

- **Pick your username carefully.** It's effectively permanent and will show up on every project you contribute to for the rest of your career. Use something professional — your real name or a clean handle, not a 2008 gamer tag.
- Turn on 2FA when it asks. Use your phone's authenticator app.

That's all for today. We'll connect git on your laptop to GitHub on Day 2.

## 8. Your first Python program (~15 min)

In Ghostty:

```
cd ~/dev/brand-lens
touch hello.py
open -a "Sublime Text" hello.py
```

That last line opens the file in Sublime. In Sublime, type exactly this:

```python
print("hello, world")
```

Save (Cmd-S). Switch back to Ghostty (Cmd-Tab) and run:

```
python3 hello.py
```

You should see `hello, world` print. Now go back to Sublime and try modifications — change the message, add more `print()` lines, try `print(2 + 2)` and `print("two plus two is", 2 + 2)`. Save after each change, switch to terminal, run it again. The save-and-run loop is what you'll be doing thousands of times. Get used to the rhythm.

## 9. Journal entry (~15 min)

In Sublime, create a new file and save it as `~/dev/brand-lens/journal/day1.md`. Three sections:

```
# Day 1

## What I did
(list of things)

## What I learned
(in your own words)

## What confused me or felt magical
(this is the most important section — be honest)
```

Don't skip the "confused me" section. If a command worked but you don't know why, that's a "confused me." If you typed `python3 hello.py` and a message appeared and it felt like sorcery, write that down. Vincent uses this to know what to dig into during our review.

---

## What "done" looks like for Day 1

- Sublime and Ghostty open via Option-Space
- You can navigate the filesystem in the terminal without Googling each command
- `~/dev/brand-lens/` exists and you're comfortable getting back to it
- A GitHub account exists with your real-ish name
- `python3 hello.py` prints something you wrote
- A `day1.md` journal entry exists
