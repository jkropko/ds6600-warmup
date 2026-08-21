# DS 6600 — Git Card

Keep this open. Five commands run this whole course.

---

## The five

| | What it does |
|---|---|
| `git clone <url>` | Copy a repo from GitHub to your computer. **Once per repo, ever.** |
| `git pull` | Get changes from GitHub. **Run at the start of every class.** |
| `git add <file>` | Mark a file to be saved in the next commit. |
| `git commit -m "message"` | Save a snapshot of everything you added. |
| `git push` | Send your commits to GitHub. **Run at the end of every class.** |

### The end-of-class ritual

```
git add .
git commit -m "Session 9: paginated member pull"
git push
```

`git add .` means "everything I changed." Write a message that says what you did — you'll be reading these in December.

---

## Three errors you will hit

**`rejected` / `non-fast-forward` on push**
GitHub has something you don't. Fix:
```
git pull
git push
```

**`CONFLICT (content): Merge conflict in <file>`**
You and GitHub both changed the same lines. Open the file and look for:
```
<<<<<<< HEAD
your version
=======
the other version
>>>>>>> abc1234
```
Delete the parts you don't want **and all three marker lines**, save, then:
```
git add <file>
git commit -m "Resolve conflict"
git push
```

**`You are in 'detached HEAD' state`**
You're viewing an old snapshot rather than working on the current one. Get back with:
```
git switch -
```

---

## When you're lost

```
git status
```

Run this any time you're unsure. It tells you where you are, what's changed, and usually what to type next. It is never a bad idea and it never breaks anything.

---

## The escape hatch

**If Git breaks in a way you can't fix: delete the folder, re-clone, message me.**

Everything you pushed is safe on GitHub. This is a legitimate fix, not a failure. Use it freely — a nuked local folder costs you ten minutes, while quietly not pushing for three weeks costs you a check-in grade and a lot of stress.

The one thing to avoid: **going silent.** If you stop pushing because something broke and you're embarrassed, I can't help. Nobody has ever broken Git in a way that impressed me.

---

## If you miss a class

One command. You won't need it often.

```
git catchup 2026-09-15
```

Use the date of the class you missed. This replaces your files with my code as it stood when that class ended, and commits it. You stay on `main` — nothing branches, nothing conflicts, and your earlier commits stay in your history. Push as normal and keep going.

Set this up once, in our first class:

```
git remote add upstream <course repo URL>
git config --global alias.catchup '!f() { git fetch upstream --tags && git checkout "$1" -- . && git commit -m "Catch up to $1"; }; f'
```

I tag the course repository by date at the end of every class. To see them all with what each one covered:

```
git fetch upstream --tags
git tag -n
```

---

## Later in the semester

Introduced when we need them — don't learn these yet.

| | |
|---|---|
| `git log --oneline` | See every commit you've made *(9/1)* |
| `.gitignore` | Keep files out of the repo — secrets, data, caches *(9/15)* |
| `git diff` | See exactly what changed before committing *(9/22)* |
| `git switch -c my-branch` | Work on a copy, then open a pull request *(11/12)* |

---

## Two notes

**VS Code has a Source Control panel** (the branch icon in the left sidebar) that does all of this with buttons. Use it if you prefer. I'll type commands in class because they're the same on every machine and they're what you'll find in documentation.

**Anything you commit, you can get back.** That's the whole point. Commit early and often — a commit costs nothing and buys you a place to return to.
