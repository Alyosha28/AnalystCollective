---
name: equity-research-analyst/write-report
description: >
  Compose the final equity research report in Damodaran-style investor-facing prose.
  Consumes all prior analyses and produces a ~3,500–5,000 word long-form document.
  Governed by report-voice.md — no second person, no emoji, no AI-answer tells.
  Step 9 of Mode A.
license: MIT
---

# Write Report

**Pipeline position:** STEP 9 of Mode A. Consumes ALL prior steps. Produces the
final research report — the deliverable.

## Input
- All prior step outputs: archetype (1), industry (2), company (3), theme (4),
  assumptions (5), valuation results (6), durability (7), triangulation (8)
- Output templates: `references/output-templates.md`
- Style guide: `references/report-voice.md`
- Worked example: `templates/report.example.md`

## Register (NON-NEGOTIABLE — read `references/report-voice.md` first)

The report is a **document published for investors** — anonymous third parties who
never saw the conversation. It is NOT a reply to the person who asked.

- **Never address the reader as "you" / "你".** Use restrained authorial voice
  (Damodaran's "I", or institutional "this report" / "本报告" / "笔者").
- **Treat the requester's views as market hypotheses.** Attribute impersonally:
  "bulls argue…", "a popular framing is…", "看多者认为…"
- **Long-form prose, not an AI answer.** Paragraphs that develop argument → numbers
  woven into sentences → sparse, purposeful exhibits.
- **Lead with a stance.** Rating + value/target + current price + thesis paragraph.
- **No AI-answer tells.** Banned: bullet-fragment dumps, bold-keyword stuffing, emoji,
  "Here's…" openers, "校准命题①"-style callouts.

## Report structure (Template A)

### 1. Header + Thesis (compact block)
```
[NVIDIA Corporation (NVDA)]
Rating: REDUCE · Intrinsic value ≈ $240 · Price $409 (10 Jun 2023) · ~40% downside
[3-5 sentence investment thesis, impersonal]
```

### 2. Opening (~200 words)
The core thesis as a hook — what the prevailing market debate gets wrong, in the
author's voice. Damodaran opens with a framing, not a table.

### 3. Industry & macro (~500 words)
Lifecycle stage, profit cycles, structure, leader rotation — prose that sets the
field. Source: step 2 output.

### 4. The company (~600 words)
Business model, moat, 10-year financial trajectory, drawdowns, structural risks.
Source: step 3 output.

### 5. The driver / theme (~400 words)
The big swing factor, value chain position, TAM size and growth. Source: step 4.

### 6. Story → numbers (~500 words)
Four drivers, each justified in a sentence with basis and range → intrinsic value.
Include: % of value in terminal, accounting adjustments made.
Source: steps 5 + 6.

### 7. SOTP (holding-co only, ~300 words)
Core + portfolio + net cash → per share. Source: step 6 SOTP output.

### 8. What the market is pricing (~400 words)
Reverse DCF + breakeven → the growth/margin the price implies, plausibility.
Source: step 6 reverse/breakeven.

### 9. Scenarios (~300 words)
2–4 internally-consistent futures (bull/base/bear), each a full driver set,
with value ranges. Source: step 6 + 7.

### 10. Evaluation of prevailing narratives (~300 words)
Take the bull/bear views circulating on the name (attributed impersonally) and
adjudicate each on the evidence.

### 11. Recent developments & catalysts (~300 words)
Dated prose section: what changed, driver delta (CONFIRMS/STRENGTHENS/BREAKS),
catalyst calendar, monitoring triggers. Source: `/refresh-valuation` or
step 11 currency sweep.

### 12. Conclusion & rating rationale (~300 words)
The judgment stated plainly, why the rating follows from the analysis. Separate
value call from any momentum note. Stamp: **valid-as-of [date] · revisit-by [catalyst].**

### 13. Limitations / what could prove this wrong (~200 words)
Self-falsification — the assumptions that carry the result, biases, data vintage.

### 14. Key risks & disclaimer
Monitorable risks + one-line not-advice disclaimer with data dates.

### Appendix: Numbers ledger
Compact table: load-bearing figures → source → as-of date. The one permitted
"wall of data."

## Depth standards (what makes a report "Damodaran-rich")

Every Mode A report must contain, developed in paragraphs:
1. Industry quantitative history (10+ yr revenue, margin cycles, leader rotation)
2. Company 10-year financial trajectory (including near-death drawdowns)
3. Segment TAM × share revenue build
4. Competitive landscape (named competitors, named battlefields)
5. Accounting adjustments with actual numbers (R&D cap, SBC dilution)
6. Historical valuation context (multiples over time)

## Output

A complete research report as a Markdown file: `TICKER_report.md`, ~3,500–5,000 words.

## Adversarial Review Gate

The write-report review is the most thorough — this IS the deliverable.

### Review criteria
- [ ] **Voice — zero tolerance:** grep for "you"/"你" outside quoted market views.
  Any second-person address in body → REVISE (fix before proceeding).
- [ ] **Voice — no AI tells:** grep for emoji, "Here's…", "校准命题", "I'll back you up",
  bullet-fragment dumps. Any → REVISE.
- [ ] **Led with stance:** Rating + value/target + current price + thesis in header.
  Missing any → REVISE.
- [ ] **Depth — all 6 Damodaran elements present:** (1) industry quant history,
  (2) company 10yr trajectory + drawdowns, (3) segment TAM×share build,
  (4) competitive landscape with named players, (5) accounting adjustments
  with numbers, (6) historical valuation context. Missing any → REVISE.
- [ ] **Prose-dominant:** Body is paragraphs, not bullet lists. Exhibits are few
  and purposeful. Wall of bullets → REVISE.
- [ ] **Numbers woven in:** Numbers live in sentences, not isolated in tables.
  "Revenue compounded to $2.4bn by 2005" not a lone cell.
- [ ] **Numbers ledger appended:** Load-bearing figures → source → as-of date table
  at end of report. Missing → REVISE.
- [ ] **MoS buy-band stated:** Accumulate-below / fair / rich-above in conclusion.
  Missing → REVISE.
- [ ] **Self-falsification:** Specific assumption that carries the result + breakeven
  at which thesis flips, woven into conclusion. Missing → REVISE.
- [ ] **Disclaimer + stamps:** One-line not-advice disclaimer + valid-as-of /
  revisit-by stamps. Missing → REVISE.
- [ ] **Length:** ~3,500–5,000 words. <2,000 → REVISE (too thin). >7,000 → note
  (may need trimming).

### Common failure modes
- Second-person "you" in body (most common — grep and fix)
- Bullet dumps instead of paragraphs
- Missing drawdowns (copy-paste gap from company analysis)
- No MoS buy-band (extract from valuation results)
- No numbers ledger (compile from source annotations)
- Report reads like an AI answer to a user, not a published research document

### Verdict thresholds
- **PASS:** All criteria met; reads like a research report for investors.
- **REVISE:** Voice violations, missing depth elements, or thin prose.
- **BLOCK:** Fabricated numbers, completely wrong register, or structurally
  broken (missing multiple required sections).

### Style checklist (run before submitting to review)
- [ ] grep for "you"/"你" → only inside quoted market views
- [ ] grep for emoji → none
- [ ] Leads with rating + value + price + thesis
- [ ] Prose-dominant; exhibits are few and purposeful
- [ ] Every material number is tier-tagged (audited / guidance / consensus / aggregator / estimate)
- [ ] Numbers ledger appended
- [ ] MoS buy-band stated
- [ ] Disclaimer present with data dates
- [ ] valid-as-of / revisit-by stamped
