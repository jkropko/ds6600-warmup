# Data Sources and Credentials

Everything the Congress Transparency Dashboard is built from, what each source is for, and how to get access.

**Start the requests early.** Three of these are instant. One can take weeks.

---

## At a glance

| Source | Access | Lead time | First used |
|---|---|---|---|
| [Congress.gov API](#congressgov-api) | API key | Instant | 8/25 |
| [openFEC](#openfec) | API key via api.data.gov | Instant | 9/24 |
| [NewsAPI](#newsapi) | API key | A day or two | 10/8 |
| [OpenSecrets bulk data](#opensecrets-bulk-data) | Account + manual approval | **Possibly weeks** | 9/17 |
| [Voteview](#voteview) | None | — | 9/17 |
| [GPO govinfo bulk data](#gpo-govinfo-bulk-data) | None | — | 9/29 |
| [congress-legislators](#unitedstatescongress-legislators) | None | — | 10/1 |

---

## Sources that need a key

### Congress.gov API

**What it gives us:** members of Congress, biographical detail, party, committee assignments, bills, sponsorship and cosponsorship. The backbone of the dashboard.

**Get a key:** <https://api.congress.gov/sign-up/> — arrives by email in seconds.

**Notes.** The rate limit is 5,000 requests per hour, which is generous, but responses are paginated at 250 records maximum, so a full pull is many requests. We build caching on 9/24 precisely so you're not re-requesting the same data all semester.

This API returns bill *metadata*. It does **not** serve full bill text — it returns links to documents hosted elsewhere. We get the text from GPO instead; see below.

---

### openFEC

**What it gives us:** campaign finance. Committees, contributions, and independent expenditures — the money spent to support or oppose each member of Congress.

**Get a key:** <https://api.data.gov/signup/> — instant. One api.data.gov key works across many federal APIs, so you may find you already have one.

**Notes.** 1,000 requests per hour at 100 results per page. That's 100,000 records an hour, which is plenty once you're caching. If you genuinely hit the ceiling, tell me before emailing the FEC — fifteen separate requests from one university in one week is not a good look, and I can ask on behalf of the class.

For large result sets the FEC recommends keyset pagination using `last_indexes` rather than page numbers, because page-based paging can skip or duplicate records as data shifts underneath you. We cover this on 9/24.

**Documentation:** <https://api.open.fec.gov/developers/>

---

### NewsAPI

**What it gives us:** news articles mentioning members by name. This is also the source that forces us to learn fuzzy matching, since articles say "Sen. Warner" rather than a bioguide ID.

**Get a key:** <https://newsapi.org/register> — usually approved within a day or two.

**Notes.** The free tier only reaches back about a month. That makes this the one source where *failing to run your pipeline destroys data permanently* — an article you don't capture in November is not available in December. We use it as the motivating example for scheduling on 11/10.

The free tier also restricts production use, which is worth thinking about when you deploy. We'll discuss it.

---

### OpenSecrets bulk data

**What it gives us:** industry and sector classification of contributors. The FEC tells you a person gave money and where they work; OpenSecrets tells you that employer is pharmaceuticals. Nothing else provides this.

**Register:** <https://www.opensecrets.org/bulk-data/signup>

**⚠️ Start this in early August.** OpenSecrets retired its API in April 2025, so bulk download is the only route. It requires agreeing to educational-use terms, registering an account, confirming your email, and then **waiting for manual approval with no published turnaround.**

**Filling in the form:**

- **Register with your `virginia.edu` address.** OpenSecrets asks students at U.S. institutions to use their `.edu` email, and a personal Gmail is the easiest way to get held up.
- **Under PROJECT AREA, check these five:** Federal Data, Candidate/Officeholder, Candidate Contribution, Independent Expenditures, PAC Contributions. Add **527** if you want fuller coverage of outside spending. Leave the rest alone — nineteen boxes ticked reads as a bulk scrape rather than a project.
- **Watch out for "Candidate Expenditures."** It sits next to the boxes you want but covers how candidates *spend* money, not what they receive. We don't use it.
- **TELL US ABOUT YOUR PROJECT has a 100-word minimum.** Write something specific, in your own words, about your project and why OpenSecrets in particular. A dozen identical paragraphs arriving from `virginia.edu` in one week is the fastest way for all of us to end up in a review queue.

**If approval hasn't come through by mid-September, that's fine.** OpenSecrets contributes exactly one thing to the dashboard, and everything else comes from the FEC. Tell me if you're still waiting and we'll work around it.

**License:** CC BY-NC-SA. Attribution required, non-commercial only. The files also lag the live site by months.

---

## Sources that need nothing

### Voteview

**What it gives us:** every roll-call vote in congressional history, plus DW-NOMINATE ideology scores. Bulk CSV, no key, no auth.

**Data and codebooks:** <https://voteview.com/data>

**One thing to know.** NOMINATE scores are **re-estimated over the full history** whenever new votes are added. That means a member's score can change even though they cast no new votes. It's not an error — it's the method working as designed — but it does mean a reload can silently alter values you already stored, and a row-count check won't notice. We build the check that catches it on 11/12.

---

### GPO govinfo bulk data

**What it gives us:** the official full text of bills, as structured XML. This is where bill text actually comes from — not the Congress.gov API, and not scraping.

**Repository:** <https://www.govinfo.gov/bulkdata/BILLS>
**User guides:** <https://github.com/usgpo/bulk-data>

**Why bulk rather than scraping.** Congress.gov's developer guidance is explicit that ignoring its robots.txt disallow rules gets you blocked, and fifteen of us pulling at once from one campus network is exactly the pattern that triggers it. GPO publishes the same content organized by Congress and bill type, one ZIP per type. Roughly twenty thousand requests become about eight downloads. We work through this on 9/29, and the principle generalizes: **when a bulk distribution exists, use it.**

`BILLSTATUS` bulk XML at <https://www.govinfo.gov/bulkdata/BILLSTATUS> refreshes every four hours and is a useful cross-check on metadata from the API.

**Public domain.** No license concerns.

---

### unitedstates/congress-legislators

**What it gives us:** the crosswalk. Every source above identifies members differently — bioguide IDs, ICPSR numbers, FEC candidate IDs — and this project maintains the mapping between them.

**Repository:** <https://github.com/unitedstates/congress-legislators>

**One wrinkle worth knowing early.** The FEC entry for each member is a *list*, not a single value, because members accumulate multiple candidate IDs across cycles and offices. A House member who later ran for Senate has several. Any campaign finance total has to sum across all of them. We handle this on 10/1.

---

## Handling your keys

**Right now:** paste them into a plain text file on your computer. For the first three weeks we will do this the wrong way on purpose.

**From 9/15 on:** keys live in a `.env` file, `.env` goes in `.gitignore`, and nothing is ever hardcoded again.

**Why the Congress key on day one is safe, and why we still fix it.** That key is read-only access to public data with no billing attached. The worst anyone could do with it is burn your hourly request limit, which clears itself. It has no resale value, because anyone can get one free in thirty seconds. That is exactly why it's the right credential to be careless with: the habit needs to form somewhere the stakes are zero, so that it holds somewhere they aren't. If you'd rather not expose even this one, use a `.env` from day one — you'll simply have done the fix early.

**If you ever commit a key by accident**, tell me. Don't quietly delete the line and hope. Once a secret is in a pushed commit it's in the history permanently — forks keep it, anyone who cloned has it — so removing the line changes nothing. **The only real fix is to make the old key stop working.**

Some providers make that easy: GitHub and DockerHub let you revoke a token in one click. Others don't. **api.data.gov, which issues both the Congress.gov and openFEC keys, has no self-service revocation at all** — you'd have to email the agency and prove the key is yours. Where revocation isn't available, the practical substitute is abandonment: generate a fresh key, switch to it, and stop using the exposed one. That's weaker, and worth knowing it's weaker.

---

## Deployment accounts

Not needed until December; I'll send instructions in November.

- **Render** — free hosting for the dashboard app
- **Neon** — free Postgres that doesn't expire

A note on why two services: Render's free Postgres is deleted 30 days after creation, which would take your database out over winter break, right when you might want to show someone. Neon's free tier is permanent.
