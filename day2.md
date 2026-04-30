# Day 2 — Git, GitHub, and Python with Shape
*v1.1.0*

**Goal for today:** your laptop and GitHub talk to each other, you understand what git is actually doing when you commit, and you've written Python that does more than print one line.

**Time budget:** ~90 min hands-on, ~15 min journal, ~15 min slack.

---

## 1. Warm-up: yesterday's commands (~10 min)

Open Ghostty (Option-Space, "ghos", return — keep practicing). Without looking at yesterday's notes, do this:

- Navigate to your home directory
- Go into `dev/brand-lens`
- List everything including hidden files
- Print the working directory to confirm where you are
- Run `python3 hello.py` again

If any of those tripped you up, that's normal — just look it up and do it again. Repetition is the point. Move on when it feels easy.

## 2. Install Homebrew and a modern Python (~15 min)

Homebrew is the package manager for macOS — it installs developer tools. The system Python is old; we want a current one.

In Ghostty, paste this command (this is the one time copy-paste is fine — it's a long install command from brew.sh):

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

It'll prompt for your Mac password and take a few minutes. When it finishes, it may print two lines about adding brew to your PATH — copy and run those lines exactly as shown. Then:

```
brew --version
brew install python@3.12
python3 --version
```

That last line should now show 3.12.something. If it still shows 3.9, close Ghostty entirely (Cmd-Q) and reopen it, then try again.

**What just happened:** you installed a tool (`brew`) whose job is installing other tools. You then used it to install a newer Python. The terminal is your interface to all of this — there's no app to click, no installer wizard. This is what "developer environment" means.

## 3. Make `subl` a terminal command (~5 min)

Right now opening a file in Sublime means typing `open -a "Sublime Text" hello.py` — that's a lot. Developers use `subl hello.py` instead. Let's wire that up.

Sublime ships with a command-line tool at a buried path inside the app bundle. We'll create an alias in your shell config so the terminal knows what `subl` means:

```
echo 'alias subl="/Applications/Sublime Text.app/Contents/SharedSupport/bin/subl"' >> ~/.zshrc
source ~/.zshrc
```

The first line appends the alias definition to `~/.zshrc` — a file Ghostty reads every time it opens, so the alias is permanent. `source` reloads that file immediately so you don't have to restart the terminal.

Test it:

```
subl hello.py
```

Sublime should open the file. From here on, use `subl` everywhere — it also accepts a folder (`subl .` opens the whole project) and multiple files at once.

**What just happened:** `~/.zshrc` is your shell's config file. It runs automatically on every new terminal session. Putting things in it is how you customize your environment — aliases, PATH changes, and other settings all live here. You'll add more to it over the course.

## 4. Install and configure git (~10 min)

```
brew install git
git --version
```

Now tell git who you are. Use the same name and email as your GitHub account:

```
git config --global user.name "Tyler Jones"
git config --global user.email "your-github-email@example.com"
git config --global init.defaultBranch main
```

The `--global` means "use this for every project on this machine." You only do this once per laptop.

## 5. Connect your laptop to GitHub (~15 min)

GitHub needs to know it's really you when you push code from your laptop. We'll use SSH keys — a cryptographic handshake that's both more secure and less annoying than passwords.

```
ssh-keygen -t ed25519 -C "your-github-email@example.com"
```

Press return three times to accept the defaults (no passphrase is fine for a learning machine). Then:

```
cat ~/.ssh/id_ed25519.pub
```

That prints your public key — a long line starting with `ssh-ed25519`. Select the entire line and copy it (Cmd-C).

In your browser, go to GitHub → click your avatar → Settings → SSH and GPG keys → New SSH key. Title: "MacBook" (or whatever your laptop is). Paste the key. Save.

Test it:

```
ssh -T git@github.com
```

It'll ask if you trust GitHub — type `yes`. You should see `Hi yourusername! You've successfully authenticated...`. If you see that, you're done. If not, raise it in the journal and we'll fix it Friday.

## 6. Make your first repository (~15 min)

A "repository" (repo) is just a folder that git is tracking. Let's turn `brand-lens` into one.

```
cd ~/dev/brand-lens
git init
git status
```

`git status` is the command you'll run more than any other. It tells you what git sees right now. Read its output carefully — it's saying you have untracked files (`hello.py`, your journal folder).

```
git add hello.py
git status
```

Notice the difference. `hello.py` moved from "untracked" to "staged" (ready to commit). Now:

```
git commit -m "first commit: hello world"
```

You just made a commit — a permanent snapshot of your code at this moment. Now do it again with the rest:

```
git add .
git commit -m "add journal"
git log
```

`git log` shows your history. Press `q` to exit.

Now push it to GitHub. In your browser, go to github.com, click the **+** icon top-right, "New repository." Name it `brand-lens`. Leave everything else default. **Don't** check any of the "initialize with" boxes. Create.

GitHub now shows you a page with commands. Use the "...or push an existing repository" block:

```
git remote add origin git@github.com:yourusername/brand-lens.git
git branch -M main
git push -u origin main
```

Refresh the GitHub page. Your code is there. **This is a big moment** — your work is now backed up, shareable, and version-controlled.

## 7. Python with shape (~25 min)

Open `hello.py` in Sublime:

```
subl hello.py
```

Replace it with:

```python
name = input("what's your name? ")
print("hello,", name)
```

Save, run with `python3 hello.py`. Type your name when it asks. That's interactive input — your script is now a tiny conversation.

Now extend it. Try to write each of these yourself before peeking. If you get stuck for more than 10 minutes, write down the exact error and what you tried, and bring it to Friday's review.

1. Ask for a name AND a favorite number. Print both back in a sentence.
2. If the number is greater than 100, print "that's a big one." Otherwise print "modest choice."
3. Print the number doubled, tripled, and squared.

These introduce: multiple inputs, type conversion (input is always a string — you'll need `int()`), `if`/`else`, and basic math. You'll hit errors. Read them. The first line of a Python error tells you which file and line; the last line tells you what went wrong. Most errors today will be either forgetting `int()` or a typo.

## 8. Commit your work (~5 min)

```
git status
git add .
git commit -m "interactive hello with name and number"
git push
```

Check GitHub — your changes are there. The four-command rhythm (`status`, `add`, `commit`, `push`) is something you'll do dozens of times a week.

## 9. Journal (~15 min)

`journal/day2.md`, same three sections. Specifically include in "what confused me":

- What did `git init` actually do? (Hint: try `ls -la` in the project folder and look for a hidden folder.)
- What's the difference between `add` and `commit`?
- Why did GitHub need an SSH key when you already had a password?

Don't Google these — write your best guess. We'll talk through them.

---

## What "done" looks like for Day 2

- `python3 --version` shows 3.12+
- `git config user.name` returns your name
- A `brand-lens` repo exists on GitHub with your hello.py code
- You can describe roughly what `git add` and `git commit` each do
- You wrote at least one Python script that uses input, an if/else, and printed math
