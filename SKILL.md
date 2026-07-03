---
name: competitor-recon
description: "Collect and analyze competitors' live ads, products, and landing pages, then report the pricing, selling points, positioning, and strategy that are working — as a side-by-side comparison the user can act on. Use whenever the user wants to research a market, benchmark competitors, do market research before starting a business or campaign, scrape competitor sites, compare products/pricing, study positioning, or prep the 'competitive landscape' section of a pitch, plan, or brief. Triggers on market research, competitor analysis, competitive landscape, benchmarking, competitor pricing, positioning research, 시장조사, 경쟁사 분석, 경쟁사 데이터, 벤치마킹, 크리에이티브 레퍼런스."
allowed-tools: ["Bash", "Read", "Write", "WebSearch"]
---

# Competitor Recon

One skill that runs the whole market-research loop: **interview the user first → fetch real web data → analyze what's working → report a source-backed, side-by-side comparison the user can act on.**

It merges a reliable web-collection layer (Firecrawl) with a structured competitive-analysis framework, so "how do we grab the data," "is the data trustworthy," and "what does it mean" all live in one place.

Use this for anyone — founder, art director, strategist, marketer — who wants to see what competitors actually do and turn it into direction for their own business, campaign, or pitch.

## Guiding principles
- **Interview before researching.** A generic scrape produces a generic report. Understand what decision the user is trying to make *first* (see Step 0). This is the biggest driver of usefulness.
- **Breadth matters.** Three competitors is a sketch, not a landscape. Aim for a named set of **5–7 direct competitors plus 2–3 adjacent/indirect ones**.
- **Every number needs a source.** A benchmark nobody can trust is worthless. Cite the URL for each data point and mark whether it's confirmed or estimated (see Step 3).
- **The deliverable is a comparison, not a pile.** The user should be able to scan one table and see how everyone differs. Lead with the table (see Step 4).

---

## STEP 0 — Interview the user (do this FIRST, always)

Before pulling any data, ask a short, focused set of questions. Don't skip this even if the user gave a one-liner — a one-liner is a starting point, not a brief. Ask these, adapting to what they already told you (don't re-ask what's answered):

1. **Goal / decision** — "What decision will this research inform?" (e.g., whether to enter the market, how to price, how to position, what to put in a pitch). This shapes everything.
2. **Category & scope** — "What exact product/category, and which geography/market?" (e.g., art-toy keyrings, US + Korea). Narrow scope = sharper data.
3. **Competitors** — "Any specific competitors you already have in mind?" Then propose additions to reach 5–7 direct + a few adjacent, and get a quick confirm.
4. **What to compare** — "Which dimensions matter most to you?" Offer a default set (pricing, product strategy, positioning, audience, channels, messaging, visual identity) and let them add/drop.
5. **Output & depth** — "How deep, and in what form?" (quick one-page comparison vs. deep per-competitor teardown; table, slide-ready, or doc).

Then reflect back a one-paragraph research plan and get a go-ahead before spending Firecrawl credits. If the user says "just go," proceed with sensible defaults but state the assumptions you're making.

> Why this matters: the same category ("art toys") means completely different research for someone pricing a $15 keyring vs. someone pitching a gallery collab. The interview is what makes the report *theirs*.

---

## STEP 1 — Build the competitor set

From the interview, assemble and briefly confirm:
- **5–7 direct competitors** (same thing, same buyer).
- **2–3 adjacent/indirect** players (different form, same need, or a category-adjacent brand the buyer also considers). These are where the best "gap" insights come from.

If the user only named a few, use `WebSearch` and the `search` command to discover the rest ("top [category] brands", "alternatives to X"). List them so the user can veto before you scrape.

---

## STEP 2 — Collect (Firecrawl layer)

All collection uses the bundled script. It needs `FIRECRAWL_API_KEY` (see README) — if it's missing, stop and tell the user how to set it rather than guessing.

**As you collect, record the source URL and access date for every page** — you'll need these for the credibility layer.

Fetch a page as clean markdown (strip nav/footer with `--main-only`):
```bash
python3 ~/.claude/skills/competitor-recon/scripts/fc.py markdown "https://competitor.com" --main-only
```

Screenshot a page (visuals matter — always capture landing/product pages):
```bash
python3 ~/.claude/skills/competitor-recon/scripts/fc.py screenshot "https://competitor.com" -o recon/competitor/home.png
```

Find pages/prices you don't have URLs for:
```bash
python3 ~/.claude/skills/competitor-recon/scripts/fc.py search "brand blind box single price" --limit 5
```

Extract structured fields (headline, CTA, price) across pages with a JSON schema:
```bash
python3 ~/.claude/skills/competitor-recon/scripts/fc.py extract "https://competitor.com/pricing" --schema schema.json --prompt "Extract headline, primary CTA, and price tiers"
```

Crawl a whole site when breadth matters (each page costs one credit — set sane `--limit`):
```bash
python3 ~/.claude/skills/competitor-recon/scripts/fc.py crawl "https://competitor.com" --limit 30 --output recon/competitor/
```

Save everything under `recon/<competitor>/` so the user keeps a reusable reference library.

---

## STEP 3 — Analyze + verify (credibility layer)

For every competitor, read the markdown and look at the screenshots, then pull out: **pricing, product/selling strategy, positioning, target audience, channels, messaging, visual identity.** Always say *why* something works, not just *what* it is.

**Trust discipline — this is non-negotiable, it's what makes the report credible:**

- **Cite every data point.** Each price, claim, or stat gets a source URL. No URL, no number.
- **Tag confidence.** Mark each fact:
  - ✅ **Confirmed** — read directly from the brand's own official page.
  - 🟡 **Estimated / third-party** — from a marketplace (Amazon/Walmart), press, or inference. Say so.
  - ⚠️ **Unverified** — couldn't confirm; flag it rather than presenting it as fact.
- **Cross-check anything surprising.** If a price or claim looks off, confirm it from a second independent source (use `WebSearch`) before reporting it.
- **Prefer primary sources.** The brand's own site > marketplace listing > blog/press > social hearsay. When sources conflict, say which you trust and why.
- **Note freshness.** Record the access date; prices and campaigns change.
- **Never invent.** If data can't be found, write "not found" — do not fill gaps with plausible-sounding guesses.

---

## STEP 4 — Report (lead with the comparison table)

Structure below unless the user asked otherwise. **The at-a-glance comparison table is mandatory and comes first** — it's the payoff for "I can't see the differences clearly."

```markdown
# {Category} Competitor Recon — {N direct + M adjacent competitors}

*Research goal: {from interview}. Scope: {category / geography}. Data as of {date}.*

## 1. At-a-glance comparison  ← ALWAYS a table, ALWAYS first
| Brand | Positioning | Core selling point | Price range | Target buyer | Key channel | Signature visual |
|-------|-------------|--------------------|-------------|--------------|-------------|------------------|
| A     | ...         | ...                | $x–$y ✅    | ...          | ...         | ...              |
| B     | ...         | ...                | $z 🟡       | ...          | ...         | ...              |
(one row per competitor; use ✅/🟡/⚠️ tags on numbers)

## 2. Selling points & differentiation
Per competitor: the ONE thing they win on, and how they're different from the rest.

## 3. Pricing & business-model breakdown
How each makes money (blind-box randomness, size ladders, collabs, subscription…), with sourced prices.

## 4. Positioning map
Place competitors on 2 axes that matter for THIS user's decision.

## 5. Messaging & visual patterns
Recurring hooks, copy formulas, visual archetypes — with why they work.

## 6. Gaps & opportunities (the important part)
Angles nobody owns, over-saturated spaces to avoid, concrete directions for the user.

## 7. Sources & confidence
Every data point → its URL and ✅/🟡/⚠️ tag. Note anything unverified.

## 8. Reference library
Paths to saved screenshots and markdown.
```

The **Gaps & opportunities** section is where the value lands: the point of studying competitors is to find the space they've left open, not to copy them.

## Legal & ethical guardrails
Use this for research and inspiration only. Study patterns and adapt them into original work — never copy competitor copy, characters, or designs verbatim (some IP, e.g. major toy characters, is strongly protected). The deliverable is *direction for original work*, not a clone.

---
**Credits / inspiration:** collection layer built on the public
[Firecrawl](https://www.firecrawl.dev) SDK; the analysis framework is inspired by
ComposioHQ's `competitive-ads-extractor` skill and Sumant Subrahmanya's
competitor-ads use case (Lenny's Newsletter). Rewritten as original work for this skill.
