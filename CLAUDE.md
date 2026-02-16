# CLAUDE.md — FOOTYJAE Repository Guide

## What This Repository Is

This is the **governance and configuration repository** for FOOTYJAE, a football (soccer) content site. It does not contain application source code — the live site runs on **WordPress** hosted on **Cloudways (Vultr 2GB, KR region)**. This repo stores rules, baselines, and operational documentation that all AI-assisted work must follow.

## Repository Structure

```
.
├── AGENTS.md    # V1 Stable Baseline rules (LOCKED) — the authoritative rule set
├── CLAUDE.md    # This file — guidance for Claude/AI assistants
├── README.md    # Points to AGENTS.md as the compliance source
└── .gitkeep     # Placeholder to keep the repo initialized
```

## Critical File: AGENTS.md

`AGENTS.md` is the **single source of truth** for all implementation decisions. It defines the V1 Stable Baseline (LOCKED). Every change proposal, code suggestion, or operational recommendation **must** comply with it. Read it in full before doing any work.

## Tech Stack

| Layer | Technology |
|---|---|
| CMS | WordPress |
| Hosting | Cloudways (Vultr 2GB, KR region) |
| Cache | Varnish (DO NOT touch) |
| Analytics | GA4 (gtag) only — GTM removed until April 2026 |
| API | OpenAI API (EN meta auto-generation) |
| Multilingual | Polylang (EN + KO) |
| Schema | VideoObject (active) |

## Key Rules (Summary)

These are derived from AGENTS.md. When in doubt, defer to AGENTS.md.

### Language
- Default output language is **English**.
- Korean is secondary/connected (for linking/coverage), not default output.

### Operating Priority (fixed order)
1. Speed (Core Web Vitals)
2. Stability
3. Scalability
4. Maintenance cost

This is a **solo operation** — automation and regression prevention come first.

### Hard Bans (non-negotiable)
- **No cache structure changes** (Varnish settings, headers, cache keys, caching plugins)
- **No optimization plugins** introduced or swapped
- **No Redis** adoption (out of V1 scope)
- **Do not remove** the mobile Nectar Post Grid fix CSS
- **Do not reintroduce GTM** before April 2026
- **No CDN, Lite YouTube embeds, Critical CSS tooling, advanced caching changes, or full bilingual translation** in V1

### Performance Baseline (LOCKED)
- Mobile LCP: 2.3s / CLS: 0.012 / TBT: 50ms
- Performance work is **complete** unless regression occurs. Do not propose extra optimizations.
- The following mobile CSS fix **must be preserved**:

```css
@media (max-width: 768px) {
  .nectar-post-grid-wrap .nectar-post-grid {
    transition: none !important;
    animation: none !important;
    opacity: 1 !important;
    transform: none !important;
  }
}
```

### Tag System (whitelist only)
Allowed categories: Teams, Leagues, Countries, Iconic Events.
Forbidden: Korean tags, generic football words, 4-digit years, "vs" patterns, low-information tokens.

## Development Workflow

### Before Proposing Any Change
1. Read `AGENTS.md` in full.
2. Verify the change does not violate the V1 Stable Baseline.
3. Keep diffs minimal — do not mix feature + refactor + performance in one change.

### Code Proposal Requirements
Every code proposal must include:
1. **Exact target location** — mu-plugin vs child theme vs functions.php vs plugin snippet
2. **Copy-paste-ready code**
3. **Rollback plan** — how to revert cleanly
4. **Side-effect checklist** — performance, Polylang linking, tags, schema, YouTube embeds
5. **Data flow description** (if touching EN auto-generation / Polylang linking / slug logic) — input → transform → store → link, plus failure case handling

### Quality Standards
- Avoid new dependencies unless absolutely necessary.
- Follow WordPress security conventions: sanitize/escape, nonces for actions, capability checks.
- Preserve video hub stability: YouTube embed reliability + VideoObject schema continuity.

## Current Priorities (in order)
1. Auto-create EN summary posts from originals
2. Auto-link EN ↔ KO pairs in Polylang
3. EN slug validation / slug logic locking
4. V1 Launch Checklist finalization

## V1 "Must Work" Checklist
- 1-minute publishing flow
- EN meta auto-generation
- EN whitelist tags
- Stable YouTube embed
- VideoObject schema
- Stable mobile Core Web Vitals

## Common Mistakes to Avoid
- Suggesting performance optimizations when CWV metrics are already stable
- Proposing CDN, caching changes, or plugin swaps
- Adding non-whitelist tags
- Outputting responses in Korean by default
- Mixing multiple concerns in a single change
- Forgetting rollback plans or side-effect checklists in code proposals
