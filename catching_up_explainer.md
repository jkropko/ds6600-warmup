# How to catch up after a missed class

If you missed a class, you don't have to reconstruct what we did. I save a snapshot of the course repository at the end of every session, and one command brings it into your own repo.

---

## The short version

```
git catchup 2026-09-15
```

Use the date of the class you missed. That's it. Your files now match the code exactly as it stood when that class ended, and you can pick up from there.

If that worked, you can stop reading. The rest of this page is for when it doesn't, or when you want to know what actually happened.

---

## What the command is doing

There are three repositories in play this semester, and it helps to keep them straight:

| Name | What it is |
|---|---|
| **`origin`** | Your fork on GitHub. Where you push your class work. |
| **`upstream`** | My copy. You never push here — you only read from it. |
| your laptop | Your local clone, where you actually type. |

At the end of every class I create a **tag** in my repository. A tag is a permanent name for one specific snapshot: `2026-09-15` means "the code as it stood when class ended on September 15," and it will still mean that in December, no matter how much I add afterward.

`git catchup` does three things:

**1. Fetches my tags.** Your fork was copied from my repo back in week one, so it doesn't automatically know about tags I made afterward. This step goes and gets them.

**2. Replaces your files with that snapshot's version.** Not a merge — a straight overwrite, so there's nothing to resolve.

**3. Commits the result**, so the catch-up is recorded in your history like any other work.

Written out, that's:

```
git fetch upstream --tags
git checkout 2026-09-15 -- .
git commit -m "Catch up to 2026-09-15"
```

The alias just saves you typing it.

---

## What happens to your own work?

Every commit you've already made is still in your history, exactly where it was. You stay on `main` the whole time — no new branch, nothing to merge later, and no possibility of a merge conflict, because this overwrites rather than combines.

What *does* get replaced is whatever is currently sitting in your files. If you missed the class, that's the point: those files were behind, and now they aren't. But if you'd started the work and got partway, commit it first so it's saved in your history:

```
git add .
git commit -m "My partial work from 9/15"
git catchup 2026-09-15
```

Then push as normal:

```
git push
```

No force, no flags, nothing unusual. To check where things stand at any point:

```
git status
```

---

## Setting it up

We do this together in our first class, so it should already be done. If you're on a new laptop or something went sideways, here it is.

**Tell your repo where mine is** (run inside your dashboard fork):

```
git remote add upstream https://github.com/jkropko/ds6600-dashboard.git
```

**Create the shortcut** (run anywhere, only needed once per computer):

```
git config --global alias.catchup '!f() { git fetch upstream --tags && git checkout "$1" -- . && git commit -m "Catch up to $1"; }; f'
```

Check the first one worked:

```
git remote -v
```

You should see `origin` pointing at your fork and `upstream` pointing at mine.

---

## Seeing what's available

To list every class snapshot with a note on what it covered:

```
git fetch upstream --tags
git tag -n
```

That's a dated table of contents for the whole semester, which is also a decent way to find the day something was introduced when you can't remember it.

---

## When it doesn't work

**`fatal: 'upstream' does not appear to be a git repository`**
The remote isn't set up. Run the `git remote add upstream` command above, then try again.

**`fatal: invalid reference: 2026-09-15`**
That tag doesn't exist. Two likely reasons: it isn't a date we had class, or I forgot to push the tag. Run `git tag -n` to see what's actually there, and email me if the date you need is missing — that one's on me.

**`nothing to commit, working tree clean`**
Your files already match that snapshot — you were up to date. Nothing went wrong and there's nothing to do.

**`git: 'catchup' is not a git command`**
The alias isn't set on this computer. Run the `git config` command above.

**Something else, or nothing makes sense**
Delete the folder, clone your fork again, and run the setup commands. Everything you pushed is safe on GitHub. Then message me — this is worth ten minutes of my time and none of your weekend.

---

## The one-sentence version

I snapshot the code at the end of every class, and `git catchup <date>` makes your files match any snapshot, as a normal commit on your normal branch.
