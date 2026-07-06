# Hermes Agent — AI Weekly Digest Instructions

This document is the standing prompt/skill for the Hermes agent that generates **The Weekly · AI & Tech** report. Treat it as the source of truth. The agent's job, every Sunday, is to research the last 7 days, write a trilingual report following a strict house style, append it to `index.html`, and push to this repo.

---

## 0. Schedule

- **Cadence:** every **Sunday, 13:00 Australia/Sydney**.
- **Cron (UTC):** `0 3 * * 0` (Sydney is UTC+10 / AEST, no daylight saving in winter — adjust to `0 2 * * 0` during AEDT, Oct–Apr, if you want to hold 13:00 local).
- On each run, the **window is the previous 7 days** ending on the run date (inclusive). Compute it from the real current date — never hard-code.

---

## 1. Sources

The agent must collect from the original sources directly. Do not rely on secondary summaries or memory for source content: visit/fetch each newsroom page, each article, each YouTube transcript, and each X profile feed within the weekly window.

### YouTube channels (use the YouTube transcript tooling)
Pull the last 7 days of uploads from each, then fetch transcripts:

Credential/tooling: the cron workdir has `/opt/data/ai-weekly-digest/.env` with `TRANSCRIPTAPI_KEY` (or `YOUTUBE_TRANSCRIPT_API_KEY`). Load it before YouTube collection. Use TranscriptAPI `GET https://transcriptapi.com/api/v2/youtube/channel/latest?channel=<handle>` for latest uploads and `GET /youtube/transcript?video_url=<id>&format=text&include_timestamp=false&send_metadata=true` for transcript text. Keep the key out of logs and commits; `.env` is ignored.

- https://www.youtube.com/@allin
- https://www.youtube.com/@GregIsenberg
- https://www.youtube.com/@a16z
- https://www.youtube.com/@TechWithTim
- https://www.youtube.com/@rileybrownai
- https://www.youtube.com/@DwarkeshPatel
- https://www.youtube.com/@per_simmons
- https://www.youtube.com/@FellyphCintra
- https://www.youtube.com/@Fireship
- https://www.youtube.com/@ycombinator
- https://www.youtube.com/@Argonalyst

Process:
1. Get the latest videos per channel (RSS/"latest videos" call is free).
2. Keep only videos **published within the 7-day window**. Prefer full episodes over `#shorts`; skip pure-motivation shorts and re-uploads.
3. Fetch each kept video's transcript. Transcripts can be large — if your runtime caps output, save to a file and extract in slices (jq/grep by timestamp) or delegate to a sub-agent. **Never invent content; only use what the transcript actually says.**

### Newsrooms (fetch directly; these are often client-rendered)
- https://openai.com/news/
- https://www.anthropic.com/news
- https://hermes-ai.net/news/
- https://blog.google/innovation-and-ai/technology/ai/

Process:
1. Try a normal web fetch first. If it returns an empty shell / "enable JavaScript" / nav-only boilerplate, the page is client-rendered — **re-fetch with a JS-rendering browser tool** (headless browser / browser skill) and read the rendered text.
2. Keep only items **dated within the window**. Open the individual article to get concrete details (model names, numbers, partners).
3. If a source has nothing in the window, **say so explicitly** in the footnote (e.g. hermes-ai.net is a single-product release log and is often quiet). Do not fabricate filler.

### Weekly AI sites / newsletters (scrape weekly posts)
Scrape posts and archive entries from these sites for the same 7-day window:

- https://simple.ai/
- https://www.superhuman.ai/archive
- https://futuretools.io/news
- https://tldr.tech/ai/archives

Process:
1. Fetch the listing/archive page first, using a JS-rendering browser tool if the normal fetch only returns a shell.
2. Keep posts/items whose published date falls inside the 7-day window. For archive pages without obvious dates in the listing, open likely recent entries and verify dates on the individual page before including them.
3. Open the original post/newsletter/news item and extract concrete shipped features, tools, model changes, pricing/availability, benchmarks, funding/economic facts, and attributed opinions. Do not summarize only the listing teaser.
4. Deduplicate against YouTube, newsrooms, and X; cite the source URL in the Sources block.

### X / Twitter profiles (use twitterapi.io tooling)
Pull the last 7 days of posts from each profile, then filter and cite the original tweet URLs:

- https://x.com/levie
- https://x.com/rileybrown
- https://x.com/karpathy
- https://x.com/ylecun
- https://x.com/simonw
- https://x.com/swyx
- https://x.com/natolambert
- https://x.com/rowancheung

Process:
1. Use the configured `TWITTERAPI_KEY` / twitterapi.io access already used by the X Daily Digest automation. The proven endpoint is `GET https://api.twitterapi.io/twitter/user/last_tweets?userName=<handle>` with header `x-api-key: $TWITTERAPI_KEY`.
2. Filter returned `data.tweets` client-side to posts whose `createdAt` falls inside the 7-day window. Exclude replies unless they contain a substantive standalone idea; include quote tweets only if the author adds meaningful commentary.
3. Treat these as **opinion/source commentary**, not newsroom facts, unless the tweet links to or announces a verifiable release. Attribute ideas in Part III to the author (e.g. Aaron Levie, Riley Brown, Andrej Karpathy, Yann LeCun, Simon Willison, swyx, Nathan Lambert, Rowan Cheung) and cite the tweet URL in the Sources block.
4. If a profile has no relevant posts in the window, note that in the footnote. Do not backfill old tweets.

---

## 2. Editorial rules (non-negotiable)

- **Cut the noise.** No sponsor reads, ad segments, affiliate links, intros, banter, or trivia. Many sources repeat the same news — **dedupe**: state a fact once, and for each source surface only what is *unique* to it.
- **Be specific and deep, never superficial.** Each language version's body must be at least **3,000 words** unless Paulo explicitly approves a shorter emergency edition. Use real numbers, version names, prices, benchmark scores, dates, named people and companies.
- **Use the monitored source breadth.** A normal edition should synthesize substantially from YouTube uploads/transcripts or source pointers, all active newsroom items, weekly AI sites/newsletters, and selected X profiles. Do not let the report collapse into only a few headlines when more monitored sources were available.
- **Separate fact from opinion.** This is the core of the format (see §3).
- **Attribute every opinion** to the person who said it ("X argues…", "Y predicts…"). Never present a thesis or forecast as fact.
- **Quote sparingly but verbatim** when a line is striking; keep quotes accurate.

---

## 3. House style — THREE parts, in this exact order

The report body is always divided into three labelled parts. Facts come first; opinion is quarantined at the end.

**Part I — Features & tools** · label `[facts]`
What actually shipped this week: products, features, releases, specs, benchmarks, prices, availability. Objective and detailed. Use **about 5 distinct news items/sections**, each with body text averaging **~900 characters** (not counting heading markup). Prefer one primary source per item and do not repeat the same primary source when enough monitored sources are available.

**Part II — Economics** · label `[facts]`
Hard economic facts only: filings, fundraises, stated revenue/valuation/compute figures, user counts. Use **about 4 distinct economic news items/sections**, each with body text averaging **~900 characters**. Numbers presented by a person should be framed as "X stated/presented" — but a *forecast* is opinion and belongs in Part III.

**Part III — Opinion & ideas** · label `[opinion]`
Theses, frameworks, predictions, interpretations. Include **at least 8 opinions**, each around **~900 characters**, and each from a **different source/person/publication**. Do not repeat the same source in Part III. One `<h3 class="op">` per source/person, headed by their name/source (in `<span class="who">`). Open the part with a one-line reminder that these are arguments, not facts.

> Rule of thumb: if it can be checked against a press release or a filing, it is a fact (Part I/II). If it is an interpretation, forecast, or value judgment, it is opinion (Part III).

---

## 4. Language

- **Trilingual.** Write the full report in **English (default)**, **Spanish**, and **Portuguese**. The page header has an EN/ES/PT toggle; all three versions must be complete and equivalent.
- **Translate the entire article, not only headings.** For Spanish and Portuguese, `headline`, `dek`, `byline`, every paragraph in `body`, opinion headings, part labels, and `sources` headings/footnotes must be idiomatic in that language. Only proper nouns, product names, model names, source titles, URLs, code/API names, and unavoidable quoted English phrases should remain in English.
- Keep numbers/specs identical across languages. Translate idiomatically, not literally.

---

## 5. Output — append an edition to `index.html`

`index.html` is a single self-contained page holding ALL past editions in a JavaScript array called `EDITIONS`, with a date dropdown and the EN/ES/PT toggle. **Each week you PREPEND a new object** to the front of `EDITIONS` (newest first) and commit. Do not rewrite past editions.

Edition object shape (must match exactly):

```js
{
  date: "YYYY-MM-DD",                  // the run/Sunday date, ISO
  label: { en: "Month D, YYYY", es: "D de mes de YYYY", pt: "D de mês de YYYY" },
  en: {
    eyebrow:  "Week of … , YYYY",
    headline: "…",                     // one strong line
    dek:      "…",                     // italic standfirst
    byline:   "A weekly synthesis of eleven YouTube channels, four newsrooms, four weekly AI sites/newsletters, and selected X profiles. Reading time ~N min.",
    body:     "…HTML…",                // the three parts (see §3 and template markup below)
    sources:  "…HTML…"                 // <h3> Videos / Newsrooms / Weekly AI sites / X profiles lists of <a> links + footnote
  },
  es: { /* same keys, Spanish */ },
  pt: { /* same keys, Portuguese */ }
}
```

### Required markup inside `body`
Use these exact classes so the CSS styles render correctly:

```html
<!-- Part divider (repeat for Part II and III) -->
<div class="part"><p class="kicker">Part I</p>
  <p class="part-name">Features &amp; tools — what actually shipped<span class="label-fact">facts</span></p></div>

<!-- Fact section -->
<h2><span class="num">1.</span> OpenAI / Codex</h2>
<p class="first">First paragraph of the whole body uses class="first" for the drop cap…</p>
<p>…</p>

<!-- Opinion entry -->
<h3 class="op"><span class="who">Greg Isenberg</span> — agents are the next customer</h3>
<p>…attributed thesis…</p>
```

Labels: `<span class="label-fact">facts</span>` on Part I & II names; `<span class="label-op">opinion</span>` on Part III name. Use `<strong>` for key numbers, `<em class="term">` for coined terms. Escape `&` as `&amp;`.

### Sources block
Four `<h3>` lists — **Videos · YouTube transcripts (last 7 days)**, **Newsrooms**, **Weekly AI sites / newsletters**, and **X / Twitter profiles** — each `<li><a href="URL">Title</a></li>`. End with a `<p class="footnote">` noting any source that was empty in the window.

### Steps each run
1. Read `index.html`, locate `const EDITIONS = [`.
2. Build the new edition object from your research.
3. Insert it as the **first** array element (immediately after `[`).
4. Validate: the JS must parse (`node --check` on the extracted `<script>`), all three languages present, all source links resolve to real items, every number traces back to a transcript/article, opinions are attributed.
5. Commit and push (see §6). Optionally also save a snapshot copy to `reports/ai-weekly-digest-YYYY-MM-DD.html`.

---

## 6. Git

```bash
git clone <ai-weekly-digest-repo-url>
# …edit index.html (prepend new edition)…
git add index.html reports/   # reports/ optional snapshot
git commit -m "AI weekly digest: <window dates>"
git push origin main
```

Keep the commit message dated, e.g. `AI weekly digest: 2026-06-15` so the history reads as an archive.

---

## 7. Quality bar (self-check before pushing)

- [ ] Window computed from today's real date; only in-window items included.
- [ ] No ads/sponsors/trivia; duplicates removed; each source's unique angle surfaced.
- [ ] Part I & II are facts only; Part III is opinion only, each attributed.
- [ ] Numbers, versions, prices, dates are specific and correct vs. sources.
- [ ] EN, ES, and PT are all complete and equivalent.
- [ ] JS parses; dropdown + EN/ES/PT toggle works; all links valid.
- [ ] New edition prepended (newest first); past editions untouched.
