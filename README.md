# The Weekly · AI & Tech

A weekly, Dan Wang–style essay report on AI & tech, generated automatically every **Sunday 13:00 Australia/Sydney** by a Hermes agent.

The agent researches original sources directly: YouTube transcripts, newsroom/article pages, weekly AI sites/newsletters, and selected X/Twitter profile feeds.

Each edition synthesizes the past 7 days from twelve YouTube channels (transcribed when available), four newsrooms, four weekly AI sites/newsletters, and selected X/Twitter profiles. Every normal edition is a deep operating brief of **at least 3,000 words per language**, using the breadth of monitored sources rather than only a few headlines, and is written in a strict **facts-first** format:

1. **Part I — Features & tools** *(facts)* — about 5 distinct news items on what shipped, each with body text averaging ~900 characters.
2. **Part II — Economics & geopolitics** *(facts)* — about 4 distinct items on adoption, capital, infrastructure, industrial capacity, energy, export controls, public procurement, data-center constraints, and cross-border model access; each with body text averaging ~900 characters.
3. **Part III — Opinion & ideas** *(opinion)* — at least 8 attributed opinions, each around ~900 characters, with a different source/person/publication for every opinion.

Every edition is trilingual (**English** default, **Spanish**, and **Portuguese** toggles in the header). Spanish and Portuguese versions translate the full article body, not only titles/headings, while keeping product names, source titles, model names, URLs, and numbers intact.

## Repo layout

- **`index.html`** — the living report. A single self-contained page holding all editions in a JS `EDITIONS` array, with a date dropdown and EN/ES/PT toggle. Open it in any browser; no build step.
- **`HERMES_AGENT_INSTRUCTIONS.md`** — the standing prompt the Hermes agent follows each week (sources, editorial rules, the three-part house style, the edition-object schema, and the git flow).
- **`reports/`** *(optional)* — dated HTML snapshots, one per week, for archival.

## How it updates

Each Sunday the Hermes agent researches the week, builds a new edition object, **prepends** it to `EDITIONS` in `index.html` (newest first, past editions untouched), validates, and commits as `AI weekly digest: <date>`. See `HERMES_AGENT_INSTRUCTIONS.md` for the full procedure.

Runtime credentials are kept outside git in `/opt/data/ai-weekly-digest/.env` (mode `0600`). YouTube transcript collection uses `TRANSCRIPTAPI_KEY`/`YOUTUBE_TRANSCRIPT_API_KEY`; the `.env` file is ignored and must never be committed.

## Sources

YouTube: @allin, @GregIsenberg, @a16z, @TechWithTim, @rileybrownai, @DwarkeshPatel, @per_simmons, @FellyphCintra, @Fireship, @ycombinator, @Argonalyst, @LivePlayerswithSamoBurja.
Newsrooms: openai.com/news, anthropic.com/news, hermes-ai.net/news, blog.google AI.
Weekly AI sites/newsletters: simple.ai, superhuman.ai/archive, futuretools.io/news, tldr.tech/ai/archives.
Supplemental economics/geopolitics: Michael Burry’s public [Cassandra Unchained](https://substack.com/@michaeljburry) notes/posts (paywalled material is not summarized).
X/Twitter: @levie, @rileybrown, @karpathy, @ylecun, @simonw, @swyx, @natolambert, @rowancheung, @michaeljburry, @DavidSacks, @SamoBurja, @bismarckanlys, @palladiummag.
