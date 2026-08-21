# AI Policy at a Glance

The full policy is in the syllabus. This is the version to check before a deadline.

**The whole thing follows from one idea: you can only safely delegate what you can verify.**

---

## The short version

| | |
|---|---|
| 🔴 **Never** | Any writing in plain English. During live coding. |
| 🟢 **Always fine** | Code on labs and the project — with conditions below. |
| 🟡 **Required** | One lab has you review AI-generated code for defects. |

---

## Where AI is not allowed

**Written responses, anywhere.** Every plain-English answer on a lab, every design rationale in Part B, every word of the project write-up. I want your thinking, in your words. This is the part I read most closely and it's the part that tells me whether the ideas landed.

**During live coding.** Class time works because typing the code teaches it in a way watching doesn't. Delegating that doesn't speed you up, it removes the exercise. Your GitHub check-ins are graded on whether you kept up by typing, so this one is somewhat self-enforcing.

---

## Where AI is allowed

**Code, on labs and on your project.** Use whatever tools you like. But the code is yours once you submit it, and it's graded as though you wrote it:

- Code that works and follows approaches we use in class, or another current best practice → **full credit.**
- Code that doesn't correctly answer the question → **loses points.** Same as if you'd written it yourself.
- Code that works but uses deprecated or needlessly complex approaches → **loses points.**
- Code that works but looks nothing like anything we've discussed → I may ask you to **annotate every line** explaining what it does, before I grade it.

That last one isn't a punishment. If you can explain it line by line, you've learned it, and that's all I was checking.

---

## Where AI is required

One lab includes a **code review exercise**: you'll get AI-generated pipeline code with deliberate defects in it and be asked to find them, explain how each one fails, and write the validation check that would have caught it.

You're graded on the review, not on rewriting the code. Reviewing generated code is now a large part of this job, and it's a skill worth practicing on purpose rather than picking up by accident.

---

## Marking AI-assisted commits

When AI helps write code you commit, say so in the commit message:

```
Add paginated FEC client

Assisted-by: Claude Code
```

That last line is a **trailer** — a `Key: Value` line in the final paragraph of a commit message. The blank line above it is what makes Git recognize it.

**Easy way:** pass `-m` twice and Git adds the blank line for you.

```
git commit -m "Add paginated FEC client" -m "Assisted-by: Claude Code"
```

**Easier way:** set this up once and forget the syntax.

```
git config --global alias.ai '!f() { git commit -m "$1" -m "Assisted-by: ${2:-AI assistant}"; }; f'
```

Then:

```
git ai "Add paginated FEC client"
git ai "Refactor the upsert" "Cursor"
```

**Forgot, and haven't pushed yet?**

```
git commit --amend --trailer "Assisted-by=Claude Code"
```

Don't amend something you've already pushed — rewriting shared history causes problems for anyone who pulled it. Just note it in the next commit and move on.

**Some tools do this automatically.** If yours already adds a trailer when it commits, you're done.

### What counts

Mark it when AI wrote or substantially rewrote code you're committing. Don't bother when you asked a question and then wrote the code yourself, or when autocomplete finished a variable name.

**When you're unsure, mark it.** Over-marking costs you nothing — it doesn't affect your grade — and under-marking is the thing I'd rather avoid.

### Why bother

It's provenance, the same reason we record where our data came from. Six months from now, when a function in your pipeline behaves strangely, knowing whether you reasoned it through or a tool produced it is useful information — mostly to you.

*(Why `Assisted-by:` rather than `Co-authored-by:`, which is what GitHub recognizes and what some tools emit by default? Co-authorship is a claim of authorship, and the journal policy this course follows holds that AI tools cannot be authors, because authorship carries accountability a tool can't bear. If your tool writes `Co-authored-by:` on its own, that's fine — just don't read it as meaning the AI is an author.)*

---

## For the project

The project and its write-up follow **Elsevier's policy for journal submissions**, since the point of the exercise is to produce something publishable.

- AI may be used to **improve readability and language**. It may not write any part of the work from scratch.
- **You are accountable for all of it.** AI produces authoritative-sounding output that is sometimes wrong, incomplete, or biased. That's your problem once you submit it.
- AI **may not create or alter images.** Using AI to write code that generates a figure is fine — that's just code. Using an image generator to produce a figure is not.
- **A statement describing your AI use is required** with the final submission. "I did not use generative AI" is a perfectly good statement if it's true.

Full Elsevier policy: <https://www.elsevier.com/about/policies-and-standards/publishing-ethics#4-use-of-ai-and-ai-assisted-tools>

---

## If you're not sure

Ask me. Genuinely — I would much rather answer a question in advance than have a conversation about it afterward. The policy is meant to keep the learning intact, not to catch anyone out, and the edge cases are mostly things I haven't thought of yet.
