---
name: competitor-recon
description: "Collect and analyze competitors' live ads and landing pages, then report the messaging, hooks, visual patterns, and positioning that are working — and how to beat them. Use whenever the user wants to benchmark competitor creative, research ad strategies, scrape competitor ads or landing pages, study a competitor's positioning, gather campaign references, or prep the 'competitive landscape' section of a pitch or brief. Triggers on competitor ads, ad benchmarking, creative teardown, scrape competitor, competitor landing page, positioning research, 경쟁사 광고, 경쟁 분석, 벤치마킹, 크리에이티브 레퍼런스."
allowed-tools: ["Bash", "Read", "Write"]
---

# Competitor Recon

One skill that does the whole competitive-creative loop: **fetch real web data → analyze what's working → report benchmarks the user can act on.** It merges a reliable web-collection layer (Firecrawl) with a creative-analysis framework, so the "how do we grab the pages" problem and the "what does it mean" problem live in one place.

Use this when a user — often an art director, strategist, or marketer — wants to see what competitors are actually running and turn that into direction for their own campaign, pitch, or brief.

## The workflow (follow in order)

The value is in doing collection and analysis as one continuous pass. Don't stop after scraping — raw pages aren't the deliverable, the insight is.

### 1. Scope the recon
Confirm with the user before pulling data:
- Which competitors (names or URLs)? A named competitive set of 3–5 is ideal.
- Which surfaces? Ad libraries (Meta/LinkedIn), landing pages, pricing pages, campaign microsites.
- What's the goal? (pitch section, creative direction, positioning gap-finding.) The goal shapes what you look for.

### 2. Collect (Firecrawl layer)
All collection uses the bundled script. It needs a `FIRECRAWL_API_KEY` (see README) — if it's missing, stop and tell the user how to set it rather than guessing.

Fetch a page as clean markdown (strip nav/footer with `--main-only`):
```bash
python3 ~/.claude/skills/competitor-recon/scripts/fc.py markdown "https://competitor.com" --main-only
```

Screenshot a page (visuals matter for creative analysis — always capture these for landing/ad pages):
```bash
python3 ~/.claude/skills/competitor-recon/scripts/fc.py screenshot "https://competitor.com" -o recon/competitor/home.png
```

Search for a competitor's ad-library or campaign pages when you don't have direct URLs:
```bash
python3 ~/.claude/skills/competitor-recon/scripts/fc.py search "Notion Meta ad library" --limit 5
```

Extract structured fields (headline, CTA, offer) across pages with a JSON schema:
```bash
python3 ~/.claude/skills/competitor-recon/scripts/fc.py extract "https://competitor.com/pricing" --schema schema.json --prompt "Extract headline, primary CTA, and pricing tiers"
```

Crawl a whole campaign site or docs area when breadth matters:
```bash
python3 ~/.claude/skills/competitor-recon/scripts/fc.py crawl "https://competitor.com" --limit 30 --output recon/competitor/
```

Save everything under `recon/<competitor>/` so the user ends up with a reusable reference library. Each crawled page costs one credit — set sane `--limit` values.

### 3. Analyze (creative-analysis layer)
For every competitor, read the markdown + look at the screenshots and pull out:

- **Problems they lead with** — the pain points in their headlines. Note frequency; a pain point repeated across many ads is a validated angle.
- **Messaging & copy patterns** — headline structures, CTA verbs, body length/tone, recurring value props.
- **Visual & creative patterns** — layout archetypes (before/after split, product-UI showcase, social proof), color/branding, static vs. video.
- **Positioning** — who they target, what category they claim, how they differentiate.
- **Audience segmentation** — different messages aimed at different segments (founders vs. enterprise vs. students).

Always answer *why* something likely works, not just *what* it is — that's what makes it usable as direction.

### 4. Report
Use this structure unless the user asks otherwise:

```markdown
# {Competitor / Set} Recon

## Overview
- Sources pulled, ad/page count, format split, common CTAs

## Problems they lead with
1. {Pain point} — copy example, why it resonates, frequency

## Winning creative patterns
- {Pattern name}: what it is, where it appears, why it works

## Copy that's working
- Best headlines / body formulas with the reason each lands

## Positioning & audience
- Where each competitor sits; which segments get which message

## Gaps & opportunities (the important part)
- Angles nobody owns, over-saturated messages to avoid,
  concrete directions for OUR next campaign

## Reference library
- Paths to saved screenshots and markdown
```

The **Gaps & opportunities** section is the payoff — the point of studying competitors is to find the space they've left open, not to copy them.

## Legal & ethical guardrails
Use this for research and inspiration only. Study patterns and adapt them into original work — never copy competitor copy or designs verbatim. Respect intellectual property. When in doubt, the deliverable is *direction for original creative*, not a clone.

---
**Credits / inspiration:** collection layer built on the public
[Firecrawl](https://www.firecrawl.dev) SDK; the ad-analysis framework is inspired by
ComposioHQ's `competitive-ads-extractor` skill and Sumant Subrahmanya's
competitor-ads use case (Lenny's Newsletter). Rewritten as original work for this skill.
