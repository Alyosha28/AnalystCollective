---
name: equity-research-analyst/research
description: >
  Shared research agent — the single point for all web research, data gathering,
  and fact-checking in the equity-research-analyst pipeline. Invoked by any sub-skill
  that needs sourced, dated information. Centralizes research methodology so analysis
  sub-skills focus on analysis, not data hunting.
license: MIT
---

# Research Agent (Shared Utility)

**Pipeline position:** Shared — invoked by any sub-skill that needs external data.
Centralizes all web research so analysis sub-skills stay focused on analysis.

## Why a dedicated research agent

Without this, every analysis sub-skill duplicates research instructions ("use
WebSearch," "cite sources," "date every fact"). That leads to inconsistent
sourcing quality and makes it hard to upgrade research methodology.

This agent is the **single point** for:
- Web searching and fact-gathering
- Source annotation and tiering
- Data recency verification
- Cross-referencing claims against primary sources

## Input

The calling sub-skill provides a **research brief**:

```json
{
  "topic": "What to research (specific, not vague)",
  "depth": "quick" | "standard" | "deep",
  "time_range": "last 2 quarters" | "last 5 years" | "10+ years",
  "required_data_points": ["list", "of", "specific", "numbers", "needed"],
  "sources_preferred": ["filings", "industry reports", "news"],
  "calling_skill": "analyze-industry"
}
```

## Research modes

| Depth | When to use | Tools | Expected output |
|-------|------------|-------|----------------|
| **quick** | Single fact check, current price, latest quarter revenue | WebSearch × 1-2 | 1-2 sourced data points |
| **standard** | Sub-skill research (industry data, company history, TAM) | WebSearch × 3-5 + WebFetch × 1-2 | Multiple sourced data points with cross-references |
| **deep** | Material facts for valuation drivers, contested claims | `deep-research` skill + WebSearch verification | Comprehensive, multi-source, cross-verified |

## Research workflow

### 1. Parse the brief
- What exactly is being asked? (Not "NVIDIA" but "NVIDIA's Data Center segment revenue Q1 FY2025")
- What depth is required?
- What's the acceptable data vintage?

### 2. Execute the search

**Standard path (most sub-skill research):**
```
WebSearch × 2-3 (different angles)
    ↓
Identify primary sources (filings, earnings calls, industry reports)
    ↓
WebFetch primary sources for exact numbers
    ↓
Cross-reference: do two independent sources agree on the key numbers?
    ↓
If numbers diverge >10%: flag the divergence, use the more reliable source
```

**Deep path (valuation-critical facts):**
```
Invoke deep-research skill with the specific question
    ↓
Extract the key data points + source list
    ↓
WebSearch to independently verify the 2-3 most critical numbers
    ↓
Flag any deep-research claims that can't be independently confirmed
```

### 3. Annotate every finding

Every data point returned MUST carry:
```json
{
  "value": 26914,
  "unit": "$ millions",
  "source": "NVIDIA 10-K FY2024",
  "source_url": "https://...",
  "tier": "audited",
  "as_of_date": "2024-01-28",
  "cross_referenced": true,
  "cross_reference_source": "Bloomberg terminal"
}
```

**Source tiers (mandatory — same as the main skill's data confidence system):**
- `audited` — From company filings (10-K, 20-F, annual report)
- `management-guidance` — Company's forward guidance (earnings call, investor day)
- `sell-side-consensus` — Consensus estimates (Visible Alpha, Bloomberg consensus)
- `third-party-aggregator` — yfinance, Wind, Capital IQ, Statista
- `industry-report` — Gartner, IDC, McKinsey, SemiAnalysis, etc.
- `news-reporting` — Reuters, Bloomberg, CNBC, Caixin, etc.
- `own-estimate` — Your inference/judgment (explicitly labeled)

### 4. Flag what's NOT found

Research that finds nothing useful is still useful — it tells the analyst the
data gap exists. Always report:
- What was searched for but NOT found
- What data exists but is stale (beyond acceptable vintage)
- What data is behind paywalls (exists but inaccessible)

## Output format

```markdown
## Research: [topic]
**Depth:** standard | **Date of search:** [today]

### Findings
| # | Data point | Value | Unit | Source | Tier | As-of date | Cross-ref |
|---|-----------|-------|------|--------|------|-----------|-----------|
| 1 | ... | ... | ... | ... | ... | ... | ... |

### Key narrative
[2-3 sentences synthesizing what the data says, with sources woven in]

### Data gaps
- [What was searched for but not found]
- [What exists but is stale]

### Source reliability note
[If any source has a reliability concern (biased, secondary reporting, paywalled preview), state it]

### For the calling sub-skill
[Specific implications: which numbers are solid enough for the valuation, which need
further confirmation, what low/base/high range is supported by the data]
```

## Which sub-skills invoke this

| Calling sub-skill | Typical research brief | Depth |
|-------------------|----------------------|-------|
| `/analyze-industry` | "Industry aggregate revenue 2015-2025, top-5 market shares each 5 years, historical EV/Revenue multiples" | deep |
| `/analyze-company` | "Company 10-year financials, historical stock price drawdowns, segment revenue breakdown, SBC dilution" | deep |
| `/analyze-theme` | "TAM estimates for [segment], competitor revenue and market share, hyperscaler capex guidance" | deep |
| `/build-assumptions` | "Current riskfree rate, industry beta, sector average cost of capital, peer margins" | standard |
| `/run-valuation` | "Current stock price, shares outstanding (latest filing), peer multiples for comps" | quick |
| `/refresh-valuation` | "Last 2 quarters: earnings, guidance changes, competitor moves, analyst revisions" | standard |
| `/critique-report` | "Verify key claims in the report being audited: are the TAM numbers real? Is the margin target plausible?" | standard |
| `/fetch-data` | "Company financials skeleton" | standard |

## Adversarial Review Gate

### Review criteria
- [ ] **Every data point annotated:** value, unit, source, tier, as-of date.
  Missing annotation → REVISE.
- [ ] **Cross-referencing attempted:** At least 2 independent sources for
  valuation-critical numbers (>5% of intrinsic value sensitivity). Single-source
  critical data → REVISE (flag as single-source risk).
- [ ] **Recency:** Data is within the acceptable vintage for the brief.
  Stale data → flag prominently, do not silently use.
- [ ] **Tier accuracy:** `audited` only for actual filing data. Aggregator data
  tagged as `audited` → REVISE.
- [ ] **Data gaps reported:** What was searched for but not found is stated.
  Silent omission → REVISE.
- [ ] **Source reliability:** Paywalled, biased, or secondary sources flagged.

### Common failure modes
- Numbers without sources (most common — "the AI market is $500B")
- Aggregator data passed off as audited
- Stale data used without flagging
- Single-source critical numbers without cross-reference
- Search too narrow (one angle, missing the obvious counter-source)

### Verdict thresholds
- **PASS:** All data annotated, cross-referenced for critical numbers, gaps reported.
- **REVISE:** Missing annotations, stale data, no cross-reference on critical data.
- **BLOCK:** Fabricated sources, completely wrong data, or search so shallow it
  missed publicly available filings.

## Self-check (run before submitting to review)
- [ ] Every data point has source + tier + date
- [ ] Critical numbers (>5% value sensitivity) cross-referenced with 2+ sources
- [ ] Data recency is within acceptable vintage
- [ ] Data gaps explicitly reported
- [ ] Source reliability concerns flagged
- [ ] Output explicitly states implications for the calling sub-skill
