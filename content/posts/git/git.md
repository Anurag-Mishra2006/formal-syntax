---
title: "Git: A Practical Roadmap"
date : 2026-06-03
featured: true
author : "Anurag Mishra"
tags : ['git', "opensource", "beginner"]
---


**Holaa! New post, new topic....** What am I bringing this time? A learning roadmap for `git`.

This is for those who want to contribute to `open source` or `collaborate on projects` — learning the very basics of this essential tool is where it all starts, and that tool is `git`.

This post is like a curriculum, covering everything from scratch to advanced, and also includes some exercises for practice. So, tighten your seatbelt — we are about to take off!

---

# Here is full overview for this curriculum : 
### Phase 1 - Fundamentals
-What Git is  -repos   -commit -working tree -staging -branches
> Goal : Understand Git's mental model before touching a command

### Phase 2 -  Daily Development Workflow
-Init -clone -add -commit -log -diff -stash -branch -merge
>Goal : survive and thrive in day-to-day Git usage

### Phase 3 - Remote Collaboration
-remote -origin vs upstream -fetch -pull -push -forks -PRs
>Goal: contribute to projects on Github/ GitLab confidently

### Phase 4 - Intermediate Git
-cherry-pick -reset -revert -reflog -tags -interactive rebase
> Goal: rewrite history cleanly, recover from mistake

### Phase 5 - Advanced Git
-bisect -worktrees -submodules -hooks -Git internals
> Goal: understand from the inside out

---

# Phase 1
## Lesson 1: What is Git, and Why Does It Exist?

Before you ever type a single Git command, you need to understand the _problem Git was built to solve._ If you understand the problem deeply, every Git concept will make intuitive sense.

---
### The Problem: Tracking Change Over Time

Imagine you're writing a research paper. You start with `essay.docx`. A week later you're not sure about your changes, so you save `essay_v2.docx`. Then `essay_final.docx`. Then `essay_FINAL_actual.docx`. Then your friend edits it and sends back `essay_final_Johns_edits.docx`.

You've just invented a terrible, manual version control system.

Now imagine this — but with thousands of files, dozens of people, working simultaneously, on code where a single wrong character can break everything.

This is the problem that **version control systems (VCS)** were built to solve.

---
### A Brief History (Why Git Specifically)

Before Git, the most popular VCS was called **SVN (Subversion)**. It had a critical flaw: there was one central server. If it went down, nobody could work. If it got corrupted, history was gone.

In 2005, the Linux kernel project — one of the largest collaborative software projects in human history — lost access to their VCS tool (BitKeeper) due to a licensing dispute. **Linus Torvalds**, the creator of Linux, sat down and wrote a new one in about 10 days.

His design goals were:
- Speed
- Simple design
- Strong support for non-linear development (thousands of parallel branches)
- Fully distributed — every copy is a complete backup
- Able to handle projects the scale of the Linux kernel

That tool was Git.

---
### The Core Mental Model: A Time Machine for Your Project

![[Pasted image 20260602224055.png]]

**The key insight:** Git doesn't save "what changed" like a diff — it saves a **complete snapshot** of every file at that moment in time. (It's clever about not duplicating identical files, but conceptually: each commit is a full photograph of your project.)

Each snapshot knows which snapshot came before it. This creates a chain. That chain _is_ your project's history.

---
### What Git Is (Precisely)

Git is a **distributed version control system**. Let's break that down:
- **Version control** — it tracks every change to every file, forever, with the ability to go back
- **Distributed** — every person who has a copy of the project has the _entire history_, not just the latest version. There is no single point of failure.
Compare this to Google Docs, which is _centralised_ — if Google's servers go down, you can't work. With Git, you can work completely offline and sync later.

---
### What Git Is NOT

This is important, because people confuse these constantly:
- **Git ≠ GitHub.** Git is the tool. GitHub is a website that hosts Git repositories and adds social/collaboration features on top. GitHub could disappear tomorrow and Git would still exist. Others like GitLab, Bitbucket, and Codeberg also host Git repos.
- **Git ≠ a backup system.** Though it functions like one, its purpose is _collaborative history tracking_, not file backup.
- **Git ≠ automatic.** Git only saves what you explicitly tell it to save, when you tell it to.
---
### ✅ Exercises — Complete These Before We Move On

These require no Git installation yet. They're about building your mental model.

**Exercise 1.1 — Identify the problem in your own words.** Without looking at notes, write 2–3 sentences answering: _"What problem does Git solve, and why is it better than saving files manually?"_ Post your answer here.

**Exercise 1.2 — Git vs GitHub.** A classmate says: _"I'm going to upload my project to Git."_ What's wrong with that sentence? Correct it.

**Exercise 1.3 — The snapshot model.** If Git stores snapshots, and you have 500 commits, and each snapshot contains the full state of 50 files — does Git store 25,000 copies of files? What's the flaw in that reasoning? (Think about it — I'll explain after you answer.)

---

## Lesson 2: The Repository, Working Tree, and Staging Area
This is the most important conceptual lesson in all of Git. Developers who struggle with Git for years usually have a fuzzy picture of what you're about to learn. Get this right and everything else clicks.
### The Three Zones of Git
At any moment when working with Git, your files exist in one of **three distinct places**. Understanding these three zones is the foundation of everything.
![[Pasted image 20260602225311.png]]

---
### Zone 1 — The Working Tree
This is simply **your project folder** — every file and subfolder you can see and edit on your computer. When you open a file in VS Code and type, you're working in the working tree. Git can see that the file changed, but it has not recorded anything yet.

Think of it as your **desk** — messy, in progress, real work happening.
### Zone 2 — The Staging Area (also called "the Index")

This is the most misunderstood concept in Git. There is **no equivalent** in most other tools.

The staging area is a waiting room between your working tree and the repository. You explicitly place changed files here using `git add`. It holds exactly what your _next commit_ will contain.

**Why does this exist?** Because you might change 10 files but only want to save 3 of them in this commit. The staging area lets you compose a precise, meaningful snapshot — not just "everything I changed today."

Think of it as your **draft** — you're deciding what goes into the next photograph.
### Zone 3 — The Repository

This lives inside a hidden folder called `.git/` at the root of your project. It contains the **entire history** of every commit ever made. You never edit this folder manually. Git manages it entirely.

Think of it as your **photo album** — permanent, organised, safe.

---
### The `.git/` folder

When you run `git init`, Git creates one hidden folder: `.git/`. This single folder _is_ the repository. If you delete it, you lose all history. If you copy it to another machine, you have the entire project history.
```
my-project/
├── .git/          ← the entire repository lives here
├── index.html     ← working tree
├── style.css      ← working tree
└── app.js         ← working tree
```
### Common Mistakes at This Stage

**Confusing "saved" with "committed."** Pressing Ctrl+S in your editor saves to the working tree. That is completely invisible to Git. Only `git commit` saves to the repository.

**Skipping the staging area.** Beginners often want to skip straight from editing to committing. Resist this. The staging area is one of Git's most powerful features — you'll appreciate it enormously in Phase 3 when crafting clean PRs.

**Panicking about `.git/`.** Never manually edit or delete anything in `.git/`. Just know it exists and what it is.

---
### ✅ Exercises — Do These Now

You'll need Git installed. Check with `git --version` in your terminal. If not installed, get it at [git-scm.com](https://git-scm.com).

**Exercise 2.1 — Create a real repository.**
```bash
mkdir my-first-repo
cd my-first-repo
git init
ls -la
```
Report back: what do you see after `ls -la`? What is the `.git` folder?

**Exercise 2.2 — Observe the three zones.**
```bash
echo "Hello, Git" > hello.txt
git status
```
Report back: what does `git status` say about `hello.txt`? Which zone is it currently in?

**Exercise 2.3 — Move a file to staging.**
```bash
git add hello.txt
git status
```

Report back: what changed in the `git status` output? What zone is `hello.txt` in now?

**Exercise 2.4 — Conceptual question (no commands).** 
You edit `login.js` and `dashboard.js`. You want to commit only the login work, leaving dashboard for later. Which zone do you use to control this, and how would you do it?

---
## Lesson 3 : Your First Commit - What a Commit Really Is
You know the three zones. Now we go deep on the most fundamental unit in all of Git : the commit.
Most people think a commit is just "saving your work." It's far more than that.

### What a Commit Actually Contains

Every commit is a permanent record that contains **five things:**
 ![[Pasted image 20260602230715.png]]


---
### The Hash — Git's Fingerprint System

Every commit gets a unique ID called a **SHA-1 hash** — a 40-character string like `3e7a9f2d1c8b...`. This is computed from the _contents_ of the commit itself: the snapshot, the author, the date, the message, and the parent's hash.

This has a profound consequence: **you cannot change history silently.** If you alter even one character in any past commit, its hash changes — and so does every commit after it. Git detects this instantly. This is why Git history is trustworthy.

In practice you only need the first 7 characters: `3e7a9f2` is enough for Git to identify it uniquely in most projects.

---
### The Parent Pointer — How History is a Chain

Every commit (except the very first) points to its parent. This is what creates the timeline you saw in Lesson 1. Git can reconstruct your entire history by following parent pointers backward from the newest commit.

This also means: branches are incredibly cheap in Git. A branch is nothing more than a **label that points to a commit.** Moving a branch label costs almost nothing. More on this in Lesson 4.

---
### Writing Good Commit Messages

A commit message is a letter to your future self and your collaborators. In open source, this matters enormously — maintainers of projects like the Linux kernel or CPython will reject commits with poor messages, no matter how good the code is.

Commit messages exist to tell you _what changed and why_ — even months later. That's why they should be written in **imperative mood**, meaning write it like a command. Not `"Fixed the bug"` or `"Fixes the bug"` — write `"Fix null pointer in login handler"`. Clean, direct, unambiguous.

This isn't just a style preference either — it's how Git itself writes messages internally: `"Merge branch..."`, `"Revert commit..."`. You're simply following the same language the tool already speaks.

---
### Your First Commit — Commands
```bash
# Tell Git who you are (one-time setup)
git config --global user.name "Your Name"
git config --global user.email "you@example.com"

# Make a commit from what's in the staging area
git commit -m "Add hello.txt greeting file"

# View the commit you just made
git log
```

`git log` shows you the full hash, author, date, and message of every commit. Press `q` to exit.

---
### ✅ Exercises

**Exercise 3.1 — Configure Git and make your first commit.**
```bash
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
git commit -m "Add hello.txt greeting file"
```
Report back: what does the output say? (It will show you the short hash and your message.)

**Exercise 3.2 — View your history.**
```bash
git log
```
Report back: what is the first 7 characters of your commit hash?

**Exercise 3.3 — Make a second commit.**
```bash
echo "Learning Git properly" >> hello.txt
git add hello.txt
git commit -m "Expand greeting with learning note"
git log
```
Report back: how many commits do you see now? What does the parent relationship look like in the log?

**Exercise 3.4 — Commit message quiz.** Classify each message as _good_ or _bad_, and briefly say why:
- `Update stuff`
- `Fix infinite loop in search indexer`
- `june 2 changes`
- `Remove unused CSS variables from dashboard`

---
## Lesson 4: Branches
This is where Git becomes genuinely revolutionary. Branches are the reason Git won against every competing version control system. Once you truly understand what a branch is, you'll understand why open source development at scale is even possible.

---
### The Problem Branches Solve

Imagine you're working on a web app. The live version is stable and users depend on it. You need to build a new feature — but it'll take two weeks and the code will be messy while in progress. Meanwhile a critical security bug is found in the live version that needs fixing _today._

Without branches: chaos. You can't fix the bug without touching your half-finished feature code.

With branches: you work on the feature in complete isolation. The bug fix happens on a separate line of history. They never interfere with each other. When both are ready, you merge them in.

---
### What a Branch Actually Is

Here is the mental model most people never get clearly:

**A branch is just a label — a pointer to a single commit.**

That's it. It's not a copy of your files. It's not a separate folder. It is a text file containing a 40-character hash.

When you make a new commit on a branch, the label moves forward to point at the new commit. The old commits don't move — they're permanent and immutable.

![[Pasted image 20260602232330.png]]

---
### HEAD — Where You Are Right Now

`HEAD` is a special pointer that answers one question: **which commit am I currently looking at?**

Usually HEAD points to a branch, and that branch points to a commit. When you make a new commit, the branch label moves forward — and HEAD moves with it because HEAD points to the branch.

In your `git log` output earlier you saw `HEAD -> master`. That means: HEAD is on the master branch, which points to your latest commit.

---
### Key Commands for Branches
```bash
# See all branches
git branch

# Create a new branch
git branch feature/login

# Switch to it (modern syntax)
git switch feature/login

# Create AND switch in one step
git switch -c feature/login

# See which branch you're on
git status
```

The naming convention `feature/login` uses a prefix. In open source you'll see: `feature/`, `fix/`, `docs/`, `chore/`. This keeps branches organised in large projects.

---
### Real-World Example

In the **CPython** repository (Python's source), at any given time there are branches like `main`, `3.12`, `3.11`, `3.10` — one for each supported release — plus hundreds of contributor feature branches. Every contributor works on their own branch. Nobody ever works directly on `main`. This is universal practice in serious open source.

---
### Common Mistakes

**Committing on the wrong branch.** Always check `git status` or `git branch` before you start work to confirm which branch you're on.

**Thinking branches copy files.** They don't. A branch is a 41-byte text file containing a hash. Creating 100 branches takes essentially no disk space.

**Deleting a branch and thinking commits are gone.** Commits are permanent. Deleting a branch only removes the label. The commits still exist (and are reachable via `git reflog` — Phase 4).

---
### ✅ Exercises

**Exercise 4.1 — Create and switch to a feature branch.**
```bash
git switch -c feature/about-page
git branch
```
Report back: what does `git branch` show? Which branch has the `*` next to it?

**Exercise 4.2 — Commit on the feature branch.**
```bash
echo "About page content" > about.txt
git add about.txt
git commit -m "Add about page placeholder"
git log --oneline
```
Report back: paste the `git log --oneline` output.

**Exercise 4.3 — Switch back to master and observe.**
```bash
git switch master
ls
```
Report back: is `about.txt` visible? Why or why not?

**Exercise 4.4 — Conceptual quiz.** In one sentence each: what is HEAD? What is a branch? What is the difference between the two?

---

## Lesson 5 : Merging
You know how to create parallel lines of history. Now you need to know how to bring them back together. This is **merging** — and it has two completely different behaviours depending on the situation.

---
### What Merging Does

Merging takes the work from one branch and integrates it into another. You always merge _into_ the branch you're currently on.
```bash
git switch master        # get onto the branch you want to merge INTO
git merge feature/login  # bring feature/login's work INTO master
```
Git has two strategies for doing this. Understanding both is critical.

---
### Strategy 1 — Fast-Forward Merge

This happens when the branch you're merging into hasn't moved since the feature branch was created. There's no divergence — the feature branch is simply ahead.

In this case Git doesn't create a new commit. It just **slides the master label forward** to where the feature branch is.
![[Pasted image 20260602235546.png]]

---
### Strategy 2 — Three-Way Merge

This happens when both branches have new commits since they diverged. Git cannot simply slide a label — it must combine two different lines of work. So it creates a brand new **merge commit** that has _two parents_.
![[Pasted image 20260602235610.png]]

Git finds the **common ancestor** (C2 — the point where the two branches diverged), compares it against both branch tips, and combines the differences. If the two branches changed _different_ files or _different parts_ of the same file, Git merges automatically. If they changed the _same lines_ of the same file — you get a **merge conflict**, which you must resolve manually.

---
### Merge Conflicts — The Mental Model

A conflict is not an error. It's Git saying: _"Two people edited the same spot and I don't know which version to keep — you decide."_

Git marks the conflict in the file like this:
```
<<<<<<< HEAD
This is the line from master
=======
This is the line from feature/login
>>>>>>> feature/login
```
You edit the file to the correct final state, remove the markers, then `git add` and `git commit` to complete the merge. We'll do deep hands-on conflict resolution in Phase 4.

---
### Real-World Example

In projects like **React** or **Django**, the `main` branch is protected — nobody commits directly to it. All work happens on feature branches. When a feature is ready, a pull request is opened, reviewed, and merged. Every merge you see in a project's history is the result of exactly this workflow.

---
### Common Mistakes

**Merging into the wrong branch.** Always confirm with `git branch` which branch you're currently on before running `git merge`.

**Panicking at a conflict.** Conflicts are normal and expected in collaborative work. They are not failures — they are Git asking for your judgement.

**Not understanding fast-forward.** Many developers are confused when they see "Fast-forward" in the output and no merge commit appears. Now you know exactly why.

---
### ✅ Exercises — Phase 1 Final

**Exercise 5.1 — Perform a fast-forward merge.**
```bash
git switch master
git merge feature/about-page
git log --oneline
```
Report back: does the output say "Fast-forward"? How many commits do you see, and where is master pointing now?

**Exercise 5.2 — Create a three-way merge scenario.**
```bash
# Make a new commit on master
echo "Home page" > home.txt
git add home.txt
git commit -m "Add home page"

# Create a new branch from the CURRENT master, go back one step
git switch -c feature/contact
echo "Contact page" > contact.txt
git add contact.txt
git commit -m "Add contact page"

# Switch to master and add another commit there
git switch master
echo "Footer content" >> home.txt
git add home.txt
git commit -m "Add footer to home page"

# Now merge — both branches have diverged
git merge feature/contact
git log --oneline --graph
```
Report back: paste the `git log --oneline --graph` output. Do you see a merge commit? What does the graph look like?

**Exercise 5.3 — Conceptual quiz. Answer in your own words:**
- What is the difference between a fast-forward and a three-way merge?
- When does a merge conflict occur?
- Why does a merge commit have two parents?
---
