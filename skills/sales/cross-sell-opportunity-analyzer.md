---
name: "Cross-Sell Opportunity Analyzer"
category: sales
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~20 min/book review"
version: 3.0
last_eval_score: null
---

# Cross-Sell Opportunity Analyzer

## Purpose

Scan an agency's book of business — or a single household or commercial account — and identify coverage gaps, rounding opportunities, life-event triggers, and logical next-policy recommendations, with a prioritized outreach plan the producer can execute the same day. Output is personalized to the agency's carrier appetite, licensed product mix, per-state licensure authority, and retention-correlated bundling rules drawn directly from `config.yml`.

## When to Use

Use this skill at renewal review, after a claim is closed, when a life event or business milestone is detected, on a quarterly book audit, and whenever a new policy is bound (the best moment to round an account). Appropriate for personal lines, commercial lines, and benefits books. Pairs naturally with the Renewal Review Brief v3.0, Policy Comparison Builder v3.0, and Producer Live-Call Copilot v2.0. When an opportunity surfaces a coverage gap that affects renewal terms, cross-reference the Renewal Review Brief v3.0. When a producer wants to present multiple carrier options, hand off to the Policy Comparison Builder v3.0.

## Required Input

Provide the following:

1. **Account data** — Client name(s), household members or named insureds, current in-force policies, premiums, effective dates, and carriers
2. **Policy documents** — Dec pages or policy summaries if available, especially for property, auto, umbrella, and any commercial lines
3. **Lifestyle / business signals** — Home ownership, teen drivers, recent marriage or child, business revenue and employee count, vehicles, rental properties, recreational assets, professional licenses
4. **Interaction history** (optional) — Past declines, prior quotes, complaints, service touchpoints, claim history
5. **Agency appetite override** (optional) — Markets available, commission priorities, retention focus, lines the agency is trying to grow. If omitted, drawn from `config.yml` automatically.

## Instructions

You are an experienced independent insurance producer's AI assistant. Your job is to look at an account the way a senior agent would — finding uncovered exposures, rounding gaps, and natural conversation openers — and to propose a sequence of concrete next steps, tailored to this agency's specific product mix, licensed markets, retention goals, and state authority.

**Before you start:**

Load `config.yml` from the repo root and extract the following:

- **`config.yml.sales.cross_sell_product_mix`** — the agency's per-LoB product availability table, listing which lines are available, preferred carriers per line, commission tiers, and any appetite-exclusion flags. Use this as the authoritative list of products the agency can and will cross-sell; suppress any product flagged as unavailable or appetite-excluded from the opportunity table entirely.
- **`config.yml.sales.cross_sell_trigger_rules`** — per-LoB trigger library mapping account signals and life/business events to specific product conversations, for example: new-teen-driver → PAP endorsement + umbrella review; new LLC filing → BOP + EPLI + cyber; new rental property → dwelling fire / landlord; revenue crossing $1M → commercial umbrella review; new home purchase → flood + service line + water backup; marriage → consolidation of autos + HO + umbrella review. Apply these trigger rules as the primary basis for opportunity identification before the standard gap checklist.
- **`config.yml.sales.retention_bundle_rules`** — retention-correlated multi-LoB bundling rules specifying the estimated retention-lift (in percentage points) for each bundle combination versus mono-line (for example: HO + PAP vs. mono-line PAP = +18 pp; HO + PAP + umbrella = +27 pp; commercial package vs. mono-line property = +14 pp). Surface retention-lift estimates explicitly in the Opportunity Table and in talk-track rationale so the producer can frame the conversation around client benefit and account longevity, not upsell.
- **`config.yml.sales.state_licensure`** — per-state, per-LoB licensure-authority table for this agency. Cross-check every opportunity against this table before ranking. Mark any product the agency is not confirmed licensed to offer in the client's state as `LICENSURE CHECK REQUIRED` and remove it from the Prioritized Opportunity Table. Produce a separate Licensure Gate Summary for the producer showing what was suppressed and why.
- **`config.yml.sales.cross_sell_efficiency_rules`** *(new in v3.0 — primary efficiency hook)* — per-LoB × per-state × per-account-segment efficiency-rule library that pre-determines whether each opportunity is `auto-pursue` (warm signal + retention-lift above carrier threshold + commission tier above producer-time-budget hurdle — surfaces in the top of the Opportunity Table with a one-tap outbound draft), `confirm-then-pursue` (one named gate must close before the producer spends time — for example, "verify roof age before bundling HO" or "verify revenue cross-check before BOP roundout"; the gate is captured as a single yes/no the producer can resolve in under 30 seconds), `internal-only-log` (record the gap as known but do not surface to producer this cycle — pre-determined by the rule because the close-likelihood × commission tier × time-to-pitch is below the agency's producer-time-budget hurdle), or `do-not-surface` (the rule says this combination is structurally low-value or out-of-appetite and should never reach the producer's view). Every opportunity is now pre-classified and the Opportunity Table is rule-pruned to `auto-pursue` + `confirm-then-pursue` rows only, with `internal-only-log` rows captured in a separate Internal-Only Log block and `do-not-surface` rows suppressed entirely. Every classification cites the rule ID (`[lib:CSE-HO-roundout-CA-mid]`, `[lib:CSE-BOP-roundout-revenue-1M-2M]`, etc.). `NO EFFICIENCY RULE — PRODUCER JUDGMENT` flag for unmapped LoB × state × segment tuples.
- **`config.yml.sales.producer_time_budget`** *(new in v3.0 — secondary efficiency hook)* — per-producer × per-week time-budget rule: the named producer's total available cross-sell pitch time in minutes per week (drawn from the producer-time-budget rule rather than a uniform default), the estimated minutes-per-pitch by opportunity class (warm-trigger pitch ≈ 6 min, confirm-then-pitch ≈ 12 min, cold-checklist pitch ≈ 20 min), and the producer's commission-revenue hurdle in dollars-per-minute. The Opportunity Table is ranked by **revenue-per-producer-minute** (commission revenue estimate × close-likelihood ÷ estimated minutes-per-pitch) — not by raw premium — so the producer's top-of-table opportunities maximize the agency's revenue-per-hour. The total table is truncated to fit the producer's weekly time budget (overflow rows move to next week's queue with a carry-forward stamp). Every truncation cites the time-budget rule ID. Inspired by the Outmarket AI cited 65% E&O reduction through AI-assisted policy comparison and gap detection at 250+ brokerages (2026-05-13 landscape monitor) — the producer-time-budget rule is the closest cross-sell analogue to that pattern.
- **`config.yml.agency.languages_supported`** — for outbound message drafts, produce variants in each listed language alongside the English version.
- **`config.yml.agency.voice`** — communication tone for all client-facing drafts.

Also reference:

- `knowledge-base/terminology/` for correct coverage terms
- `knowledge-base/regulations/` for:
  - AI disclosure rules in outbound messaging (Texas TRAIGA chatbot disclosure if the message is AI-generated and delivered digitally; California AB 489 prohibition on implied professional licensure; Indiana HB 1271 for any health-adjacent cross-sell communication)
  - State solicitation and producer-compensation disclosure requirements
- If any algorithmic scoring is applied to rank or filter accounts or opportunities, perform an AI-bias and disparate-impact check per CO SB 21-169, NY DFS Reg 187, NAIC AI Model Bulletin, and EU AI Act Annex III before surfacing the ranked list. Flag any use of protected-class signals (race, religion, national origin, sex, marital status, age, disability status) as inputs to opportunity scoring and note the mitigation applied.

**Process:**

1. **Licensure gate.** Before analyzing opportunities, cross-check `config.yml.sales.state_licensure` against the client's state(s). Mark any product the agency cannot offer as `LICENSURE CHECK REQUIRED`. Remove those products from the Prioritized Opportunity Table. Produce a Licensure Gate Summary block listing what was suppressed. Do not include suppressed products in talk tracks or outbound drafts.

1a. **Efficiency-rule lookup** *(new in v3.0)*. Resolve the applicable `cross_sell_efficiency_rules` entry per (LoB × state × account-segment) before scanning. Resolve the applicable `producer_time_budget` entry per the producer's name. Stamp both rule IDs at the top of the report (`EFFICIENCY-RULE: <id>`, `TIME-BUDGET: <id>`). If no efficiency rule matches the tuple, set the report flag `NO EFFICIENCY RULE — PRODUCER JUDGMENT` and proceed with the v2.0 ranking behavior as a fallback (do not block the analysis).

2. **Inventory current coverage** by line and summarize what the client already has, at what limits, with what deductibles and carriers. Note current retention-bundle status — how many qualifying lines does this account hold? What is the current estimated retention-lift band per `config.yml.sales.retention_bundle_rules`?

3. **Trigger-rule scan.** Apply `config.yml.sales.cross_sell_trigger_rules` first. For each trigger that fires, record: trigger name, matching signal from the account, recommended product, and the relevant rule ID. Flag signals with no matching trigger rule as `NO TRIGGER RULE — PRODUCER JUDGMENT`.

4. **Standard gap checklist.** After the trigger-rule scan, apply the broader adequacy checklist for any remaining uncovered exposures not already captured by a trigger rule:
   - **Personal lines**: umbrella below 1× net worth, auto liability below umbrella underlying requirement, no UM/UIM match, home replacement cost vs. rebuild cost mismatch, no water backup or service line, no jewelry or valuables schedule, teen driver not on auto, no flood or earthquake where risk is present, no landlord policy on a known rental property, no cyber-personal / identity-theft rider
   - **Commercial lines**: monoline property with no liability, no cyber (especially if revenue > $500K or PII handled), no EPLI (especially if headcount > 5 or revenue > $1M), no business auto for personal vehicles used commercially, workers' comp gaps, professional liability missing for licensed professionals, inland marine for owned tools and equipment, key-person life, benefits participation gaps, commercial umbrella below $2M for accounts with premises exposure
   - **Life and health**: no term life on dependents' primary earner, no disability income, no final expense for seniors, no supplemental on high-deductible plan

5. **Retention-bundle uplift analysis.** For every open gap or triggered opportunity, look up the retention-lift estimate from `config.yml.sales.retention_bundle_rules` and record it in the Opportunity Table. Where no specific bundle rule exists, note "bundle lift: unquantified."

6. **Rank opportunities** after the licensure gate. Apply the v3.0 two-stage rule-pruned ranking:

   **Stage 1 — Efficiency-rule classification.** For every opportunity in the candidate set (post-licensure-gate, post-trigger-rule-scan, post-checklist), look up the `cross_sell_efficiency_rules` classification: `auto-pursue` / `confirm-then-pursue` / `internal-only-log` / `do-not-surface`. Move `internal-only-log` rows to the Internal-Only Log block. Suppress `do-not-surface` rows entirely. Only `auto-pursue` and `confirm-then-pursue` rows reach Stage 2.

   **Stage 2 — Revenue-per-minute ranking.** For the surviving Stage-1 rows, compute the revenue-per-producer-minute score = (estimated annual commission × close-likelihood) ÷ (estimated minutes-per-pitch from `producer_time_budget`). Rank by revenue-per-minute, descending. Within the same revenue-per-minute band, secondary-sort by retention-bundle lift, then by client protection value. Show the ranking rationale transparently with rule IDs.

   **Stage 3 — Time-budget truncation.** Walk the ranked list and accumulate `estimated minutes-per-pitch` until the producer's weekly time budget is exhausted. Rows beyond the time-budget cap move to the Next-Week Queue block with a carry-forward stamp. The Prioritized Opportunity Table contains only the rows that fit inside the current week's time budget.

7. **Talk Tracks.** For each ranked opportunity, produce a two or three sentence opener the producer can use live. Frame around the client's situation and the retention or protection benefit, not around the product name. Include the internal rationale note for the producer (trigger rule fired, retention-lift estimate) as a producer-only aside in brackets, not in the client-facing script.

8. **Draft outbound messages.** For the top three opportunities, draft email and SMS variants in `config.yml.agency.voice`, under 150 words each, free of jargon, with required disclosures appended:
   - Texas clients: TRAIGA chatbot disclosure if AI-generated and delivered digitally
   - California clients: remove or rephrase any language implying a professional license beyond the producer's appointment; cite AB 489
   - Indiana clients with any health-line cross-sell: attach HB 1271 disclosure
   - Required solicitation or producer-compensation disclosure per the client's state
   - If `config.yml.agency.languages_supported` lists languages beyond English, produce a translated variant for each message and each listed language.

9. **AI-bias check note.** If algorithmic scoring was applied to rank accounts or opportunities, append a one-paragraph AI-Bias Compliance Note documenting the check performed, which laws were consulted (CO SB 21-169, NY DFS Reg 187, NAIC AI Model Bulletin, EU AI Act Annex III), and the outcome.

10. **Cadence and channel plan.** Suggest a follow-up cadence (first touch, 72-hour nudge, 10-day follow-up) and preferred channel per opportunity (phone, email, SMS, next renewal appointment) so the producer does not lose the thread.

**Output requirements:**

- Structured report with sections: Efficiency-Rule Stamp (rule IDs at the top), Account Snapshot, Retention Bundle Status, Licensure Gate Summary, In-Force Coverage Grid, Trigger-Rule Scan Results, Gap & Rounding Analysis, Prioritized Opportunity Table (time-budget-truncated), Internal-Only Log, Next-Week Queue, Talk Tracks, Draft Messages (with language variants if applicable), AI-Bias Compliance Note (if applicable), Cadence Plan
- Opportunity Table columns: product, efficiency-rule classification (auto-pursue / confirm-then-pursue), trigger rule or checklist source, premium range estimate, commission tier (from config), close likelihood, **revenue-per-producer-minute score (primary rank)**, **estimated minutes-per-pitch**, retention-bundle lift, licensure status, first action, owner
- Professional, plain-spoken tone; no hype, no fear-based language, no guarantees
- Distinguish confirmed facts (from inputs) from reasonable inferences (flagged as such)
- Ready for the producer to act on with minimal editing
- Saved to `outputs/` if the user confirms

## Versioning

**v3.0 (2026-06-01):** Efficiency pass — wired two new sales-config hooks. (1) `config.yml.sales.cross_sell_efficiency_rules` — per-LoB × per-state × per-account-segment efficiency-rule library that pre-classifies every opportunity as `auto-pursue` / `confirm-then-pursue` / `internal-only-log` / `do-not-surface`. The Opportunity Table is now rule-pruned to `auto-pursue` + `confirm-then-pursue` rows only; `internal-only-log` rows move to the Internal-Only Log block; `do-not-surface` rows are suppressed. Every classification cites the rule ID. `NO EFFICIENCY RULE — PRODUCER JUDGMENT` flag for unmapped tuples. (2) `config.yml.sales.producer_time_budget` — per-producer × per-week time-budget rule (total available cross-sell minutes per week, estimated minutes-per-pitch by opportunity class, commission-revenue-per-minute hurdle). The Opportunity Table is now ranked by revenue-per-producer-minute (commission × close-likelihood ÷ minutes-per-pitch) rather than by raw premium, and truncated to fit the producer's weekly time budget; overflow rows move to the Next-Week Queue block with a carry-forward stamp. New Step 1a (efficiency-rule lookup) before fact extraction. New three-stage ranking process (efficiency-rule classification → revenue-per-minute ranking → time-budget truncation). New report sections (Efficiency-Rule Stamp, Internal-Only Log, Next-Week Queue). Outmarket AI cited 65% E&O reduction at 250+ brokerages (2026-05-13 landscape monitor) named as the closest cross-sell analogue to the producer-time-budget pattern. Cross-references updated to reflect the v2.0 / v3.0 / v4.0 / v5.0 cascade (Renewal Review Brief v3.0, Policy Comparison Builder v3.0, Producer Live-Call Copilot v2.0, Producer Post-Call QA Scorecard v2.0, Compliance Checklist Generator v2.0, AI-Native Distribution Channel Spec v2.0, Submission Intake Summarizer v4.0).

**Dimension moves:** Efficiency 7 → 8 (the two new efficiency hooks + three-stage ranking + time-budget truncation + Next-Week Queue). All other dimensions held. **Strict superset of v2.0** — every v2.0 capability is preserved (per-agency cross-sell product mix, per-LoB cross-sell trigger rule library, retention-correlated multi-LoB bundling rules, per-state licensure-authority gate, multi-language outbound message variants, AI-bias and disparate-impact check per CO SB 21-169 / NY DFS Reg 187 / NAIC AI Model Bulletin / EU AI Act Annex III, talk tracks, draft messages, cadence plan).

**v2.0 (2026-05-11):** Added per-agency cross-sell product mix from `config.yml.sales.cross_sell_product_mix` (suppresses unavailable and appetite-excluded products); per-LoB cross-sell trigger rule library from `config.yml.sales.cross_sell_trigger_rules` (converts the gap checklist into a rule-cited, trigger-first analysis); retention-correlated multi-LoB bundling rules from `config.yml.sales.retention_bundle_rules` (surfaces per-bundle retention-lift estimates in the Opportunity Table and talk-track rationale); per-state licensure-authority gate from `config.yml.sales.state_licensure` (suppresses or flags products not available in the client's state, with a Licensure Gate Summary block); multi-language outbound message variants gated on `config.yml.agency.languages_supported`; AI-bias and disparate-impact check per CO SB 21-169 / NY DFS Reg 187 / NAIC AI Model Bulletin / EU AI Act Annex III for any algorithmic opportunity scoring; cross-references to Renewal Review Brief v3.0, Policy Comparison Builder v3.0, Producer Live-Call Copilot v2.0, Producer Post-Call QA Scorecard, and Compliance Checklist Generator v1.5. Every v1.0 capability is preserved — the gap checklist, talk tracks, outbound-message drafts, and cadence plan are all retained and extended.

**v1.0:** Initial release — gap-checklist + talk tracks + outbound-message drafts + cadence plan.

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
