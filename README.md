# The Weekly · AI & Tech

A weekly, Dan Wang–style essay report on AI & tech, generated automatically every **Sunday 13:00 Australia/Sydney** by a Hermes agent.

The agent researches original sources directly: YouTube transcripts, newsroom/article pages, weekly AI sites/newsletters, and selected X/Twitter profile feeds.

Each edition synthesizes the past 7 days from ten YouTube channels (transcribed), four newsrooms, four weekly AI sites/newsletters, and selected X/Twitter profiles, and is written in a strict **facts-first** format:

1. **Part I — Features & tools** *(facts)* — what actually shipped: products, specs, benchmarks, prices.
2. **Part II — Economics** *(facts)* — filings, raises, stated revenue/valuation/compute numbers.
3. **Part III — Opinion & ideas** *(opinion)* — theses and forecasts, each attributed to who said it.

Every edition is trilingual (**English** default, **Spanish**, and **Portuguese** toggles in the header) and selectable from a date dropdown.

## Repo layout

- **`index.html`** — the living report. A single self-contained page holding all editions in a JS `EDITIONS` array, with a date dropdown and EN/ES/PT toggle. Open it in any browser; no build step.
- **`HERMES_AGENT_INSTRUCTIONS.md`** — the standing prompt the Hermes agent follows each week (sources, editorial rules, the three-part house style, the edition-object schema, and the git flow).
- **`reports/`** *(optional)* — dated HTML snapshots, one per week, for archival.

## How it updates

Each Sunday the Hermes agent researches the week, builds a new edition object, **prepends** it to `EDITIONS` in `index.html` (newest first, past editions untouched), validates, and commits as `AI weekly digest: <date>`. See `HERMES_AGENT_INSTRUCTIONS.md` for the full procedure.

## Sources

YouTube: @allin, @GregIsenberg, @a16z, @TechWithTim, @rileybrownai, @DwarkeshPatel, @per_simmons, @FellyphCintra, @Fireship, @ycombinator.
Newsrooms: openai.com/news, anthropic.com/news, hermes-ai.net/news, blog.google AI.
Weekly AI sites/newsletters: simple.ai, superhuman.ai/archive, futuretools.io/news, tldr.tech/ai/archives.
X/Twitter: @levie, @rileybrown, @karpathy, @ylecun, @simonw, @swyx, @natolambert, @rowancheung.
