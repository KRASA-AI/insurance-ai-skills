---
name: "Renewal Review Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~30 min/renewal"
version: 3.0
last_eval_score: null
---

# Renewal Review Brief

## Purpose

Produce a renewal review brief that drives **a specific retention decision** — Renew As-Is, Re-Negotiate, Restructure, Remarket, or Decline-to-Renew — backed by a quantified retention tier, a config-driven carrier-appetite read, a loss-experience verdict, a state non-renewal-notice clock, and a Producer-ready talk-track tuned to whatever rate change the brief recommends.

The deliverable is the operational document a producer or account manager hands to a senior leader, marketer, or carrier underwriter 60–90 days before expiration. It pairs a long-form brief, a one-page executive summary, and an AMS/CRM-ready activity-log block so nothing falls through.

## When to Use

Use any time a renewal is approaching — typically T-90 to T-60 — for either side of the transaction:

- **Agency / broker side** — preparing to market, negotiate, restructure, or non-renew an account
- **Carrier side** — evaluating renewal terms, declining-to-renew with cause, or restructuring program
- **MGA / wholesaler side** — producer-facing renewal package
- **Captive / large-risk side** — board-level renewal review

Pairs with: **Loss Run Analyzer** (deep loss analysis fed into Section 2), **Underwriting Risk Profile Builder** (risk score fed into Section 3), **Submission Intake Summarizer v3.0** (when the verdict is Remarket — hand off appetite-prioritized targets), **Producer Live-Call Copilot** (when the brief's recommendation triggers a producer-and-insured strategy call), **COI Compliance Reviewer** (when the COI requirement matrix has changed), **Coverage Explanation Letter** (when restructuring requires a coverage-position letter), **Email Drafter** (for the renewal-strategy email + the broker-/insured-facing note), **Compliance Checklist Generator** (for state non-renewal notice deadlines and AI-disclosure obligations).

## Required Input

Provide the following:

1. **Account overview** — Named insured, NAICS / SIC, lines of coverage, current carrier(s), MGA or wholesaler if surplus-lines, effective and expiration dates, account tenure (years with agency / carrier), prior remarket history, prior non-renewal flags
2. **Expiring program details** — Premiums by line, limits, deductibles / SIRs, key endorsements (additional insured forms, waivers, primary-and-non-contributory wording, follow-form umbrellas), expiring credits or surcharges, any captive layer or fronting arrangement, audit posture
3. **Loss history** — Loss runs for the current and 3–5 prior policy periods; large-loss list with reserve and recovery posture; any open litigation, subrogation, or stop-loss recoveries
4. **Account notes** (optional) — Relationship notes, service issues, coverage change requests, M&A activity, operational changes (new locations, new products, new payroll bands), risk management improvements made, complaints
5. **Market intelligence** (optional) — Rate trend for class / line, carrier appetite changes, competitor quotes already received, recent reinsurance treaty resets affecting capacity
6. **Producer-side inputs** (optional) — Producer's gut read, target retention price, restructure ideas, broker-of-record letter risk
7. **Insured-side inputs** (optional) — Stated budget, dissatisfaction items, board pressure for cost reduction, M&A pipeline, captive-formation interest

## Instructions

You are a senior account-management AI assistant. Your job is to drive a defensible renewal decision — not to summarize prettily.

### Before You Start

- Load `config.yml` from the repo root for:
  - `agency.name`, `agency.signer_block`, `voice` (tone, do-not-use words, signature)
  - `agency.retention_tiers` (custom tier names if defined; defaults below if not)
  - `agency.carrier_appetite_map` (carrier × class → appetite level: PREFERRED / STANDARD / RESTRICTED / OFF / SURPLUS)
  - `agency.contingency_posture` (which carriers have profit-share / contingency programs and the loss-ratio / volume thresholds at risk this renewal)
  - `agency.remarket_triggers` (rate-increase % threshold, loss-ratio threshold, coverage-form-degradation triggers, service-level triggers — defaults below if not defined)
  - `agency.commission_schedule` (so the brief can show producer / agency revenue impact of each strategy option)
  - `agency.state_of_domicile` and `agency.licensed_states` (drives state non-renewal-notice timelines and AI-disclosure obligations)
  - `agency.preferred_markets` and `agency.specialty_partners` (for the Remarket option's target list)
- Reference `knowledge-base/terminology/` for correct terms — never use "policy holder" (one word: "policyholder"), never use "insurance company" when "carrier" or "insurer" is correct, prefer "expiring program" over "current policy" when discussing multi-line accounts
- Reference `knowledge-base/regulations/` for state non-renewal notice timelines and AI-disclosure rules:
  - **Non-renewal notice (illustrative — verify against the carrier's own filing)**: CA 75-day, NY 60-day, FL 45-day mid-term cancellation rule, TX 30-day, IL 60-day, MA 45-day, NJ 30-day, PA 60-day. Audit-class WC and TX windstorm-zone property have separate rules
  - **AI-content disclosure** when a consumer-facing version of the brief or talk-track will be sent to the insured: TX TRAIGA, CA AB 489, IN HB 1271 if any AI-driven coverage decision is described
  - **Producer licensing footer** on insured-facing emails per state rules; surplus-lines disclosure on E&S placements
- Use the company's communication tone from `config.yml` → `voice`

### Process

#### 1. Account Snapshot

Build a **one-glance** account snapshot:

- Header line: Named insured · NAICS / class · total expiring premium · effective / expiration · years as client · producer / CSR
- Coverage table: `Carrier · Line · Premium · Limits · Deductible / SIR · Expiring Credits · Notes`
- Key relationship notes (2–4 bullets max): service issues, coverage change requests, M&A or operational changes, complaints, BOR letter risk
- **Strategic flag** (if any): Tier-1 account, board-level relationship, multi-line cross-sell already in play, producer-of-record dispute, claims-driven account, audit-class with material payroll volatility

#### 2. Loss Experience Verdict

Lead with a one-sentence **loss verdict** ("The 5-year loss ratio of 73% trends adverse on auto liability driven by two large bodily-injury losses; property and WC remain favorable"), then the supporting build:

- Loss ratio by year and overall — show net (after recoveries / subrogation) and gross
- Frequency and severity trend by line; flag any line where frequency × severity is accelerating
- Large losses (≥ 25% of expiring premium for that line OR config-defined threshold): each with current status (open reserves, settlement posture, subrogation recovery, stop-loss treaty trigger)
- Risk-management activities since prior renewal that should change the underwriting view
- Industry / class benchmark comparison where available (loss ratio vs. class median; frequency vs. class median)
- **Loss-Drive Verdict**: `FAVORABLE` / `NEUTRAL` / `MIXED` / `ADVERSE` / `SEVERELY ADVERSE` — drives the rate-change band in Section 4

If the Loss Run Analyzer has run on this account, **cite its output and do not re-derive metrics**; surface only the verdict and the deltas.

#### 3. Market Conditions & Carrier Appetite

For each line of coverage, produce:

- Market-cycle posture: `HARD` / `FIRMING` / `STABLE` / `SOFTENING` — with a one-line reason ("reinsurance treaty resets January 2026 are sustaining property rate firming in CAT-exposed coastal zones")
- **Incumbent carrier read** — pulled from `config.yml.agency.carrier_appetite_map` for this carrier × class × premium-band: `PREFERRED` / `STANDARD` / `RESTRICTED` / `OFF` / `SURPLUS`. If `RESTRICTED` or `OFF`, the brief's recommendation cannot be Renew As-Is — flag explicitly.
- **Contingency / profit-share impact** — if this account's loss ratio threatens the carrier's contingency tier per `config.yml.agency.contingency_posture`, surface the agency-level revenue-at-risk number (not just the account premium)
- Capacity / form-change signals (new exclusions in the renewal form, mandatory deductible increases, mandatory CAT sublimits)
- Estimated rate-change band — cross product of the Loss-Drive Verdict (Section 2) and the market posture, ±the carrier-appetite read

#### 4. Renewal Strategy — The Decision

This is the section the reader actually wants. Lead with one sentence: **the recommended verdict and why**.

##### Retention-Tier Rating

Score the account against a five-tier rubric (overrideable in `config.yml.agency.retention_tiers` with custom names / weights):

| Tier | Defaults to | Action implication |
|---|---|---|
| **PLATINUM (Must-Retain)** | Top-decile premium OR strategic / referral-source OR multi-line cross-sell-active | Hold price aggressively; restructure before remarket; producer + agency leadership engaged |
| **GOLD** | Top-quartile premium AND profitable | Re-negotiate; remarket only if rate change exceeds remarket trigger; producer-led |
| **STANDARD** | Median band, neutral profitability | Renew As-Is if rate change ≤ trigger; remarket if above; CSR-led |
| **AT-RISK** | Loss-driven, service-issue, or rate-shock candidate | Restructure or remarket; pre-decline conversation with insured; producer + claims leadership engaged |
| **DECLINE-TO-RENEW** | Below underwriting floor OR losses confirm carrier appetite is `OFF` OR ethical / E&O / regulatory disqualifier | Issue non-renewal per state notice timeline; producer-led with compliance counsel |

Score using the weights in config (defaults: premium 25 / profitability 25 / strategic 20 / tenure 15 / cross-sell 15). Show the score, the band, and the rule that drove the band.

##### Strategy Decision Matrix

Pick one verdict per line and one composite verdict for the account:

| Verdict | When | What changes | Who leads |
|---|---|---|---|
| **RENEW AS-IS** | Loss verdict ≤ NEUTRAL, market posture STABLE / SOFTENING, incumbent appetite PREFERRED / STANDARD, rate change ≤ remarket trigger | Nothing structural; producer touchpoint only | CSR |
| **RE-NEGOTIATE** | Rate change > trigger but ≤ 1.5× trigger; losses NEUTRAL / MIXED; appetite STANDARD | Specific levers: deductible increase, sublimit acceptance, scheduled-rating credit, loss-control commitment | Producer |
| **RESTRUCTURE** | Material loss adversity OR coverage-form degradation OR captive-formation opportunity OR multi-line consolidation | Layer rebuild, captive layer, fronting, monoline-to-package consolidation, deductible buy-back removal | Producer + UW |
| **REMARKET** | Rate change > 1.5× trigger OR appetite RESTRICTED OR new market openings beat incumbent | Submit to N markets per `config.yml.agency.preferred_markets` for the class; appetite-first prioritized using Submission Intake Summarizer v3.0 | Producer + Marketing |
| **DECLINE-TO-RENEW** | Tier = DECLINE-TO-RENEW per Section 4 rubric | Issue non-renewal notice per state timeline; producer talk-track for the conversation | Producer + Compliance |

##### Premium / Coverage Outcomes — Quantified

For each verdict considered, show:

- **Estimated renewal premium band** (low / target / high) — use the Loss-Drive Verdict, market posture, and carrier appetite to set the band
- **Coverage delta** — what the insured loses or gains under that verdict
- **Insured budget fit** — vs. stated budget if provided; flag the gap
- **Producer / agency revenue impact** — commission delta vs. expiring; contingency / profit-share impact pulled from `config.yml.agency.contingency_posture`
- **Risk-of-loss-of-account probability** — `LOW` / `MEDIUM` / `HIGH` / `CERTAIN-IF-NOT-EXECUTED`

##### Cross-Sell & Account-Rounding Triggers

Flag any cross-sell that the renewal cycle should pull forward (cyber for a manufacturer adding e-commerce, EPLI for an insured crossing 50 employees, umbrella for an insured whose net worth has crossed the auto-policy underlying limit). Hand off to **Cross-Sell Opportunity Analyzer**.

#### 5. Action Plan & Timeline

Generate an explicit T-minus calendar driven by the renewal effective date and the state non-renewal-notice timeline:

| Milestone | Default | Owner | Trigger |
|---|---|---|---|
| **T-90** | Brief delivered to producer; account-data refresh requests sent | Producer + CSR | Always |
| **T-75** | State non-renewal-notice latest-fire-date if Decline-to-Renew | Compliance + Producer | If state requires 75-day notice (e.g., CA) |
| **T-60** | Submission to market if Remarket; renewal questionnaire to insured if Re-Negotiate / Restructure | Marketing + Producer | If Remarket / Re-Negotiate / Restructure |
| **T-45** | Quote in hand from incumbent and / or marketed targets; coverage comparison run | Producer | Always if not Renew As-Is |
| **T-30** | Renewal strategy call with insured; Producer Live-Call Copilot loaded with this brief | Producer | Always |
| **T-21** | Decision locked; binder requests issued; loss-runs requested for current period if marketing | Producer + CSR | Always |
| **T-14** | Insured signature / payment / financing arrangement; AMS activity logged | CSR | Always |
| **T-0** | Effective; post-renewal recap; Cross-Sell Opportunity Analyzer queued for 30-day call | CSR + Producer | Always |

Show the actual dates, not just the T-minus labels. Owner column uses names from `config.yml.agency.signer_block` or producer-of-record on the account.

#### 6. Producer Talk-Track — Tuned to the Recommendation

Generate a short producer-and-insured talk-track calibrated to the recommended verdict and the rate change. Five tracks:

- **Renew As-Is at flat-to-modest increase** — appreciation, value reaffirmation, single-paragraph rate explanation
- **Re-Negotiate (rate increase 5–15%)** — empathetic acknowledgement of the increase, market-cycle context (no blame), specific lever options (deductible / sublimit / loss-control commitment), close with calendar hold
- **Restructure** — frame as "redesigning the program around what's changed in your business" rather than as a price-defense
- **Remarket** — explain what marketing means and does not mean, set realistic timeline, note that the incumbent will still get the last look
- **Decline-to-Renew (or carrier non-renewal)** — empathetic, factual, never apologetic-for-the-business-decision, explain the state-required notice timing, name 2–3 alternative paths the agency can help with, log to compliance file

Each talk-track is 4–8 sentences, drops directly into the Producer Live-Call Copilot's review mode, and never makes coverage promises beyond authority.

#### 7. Risk & Compliance Flags

Surface any of the following if present — explicitly named, not buried:

- **State non-renewal notice deadline** with the exact latest-fire date computed from the renewal effective date
- **Bad-faith / unfair-claims-practices exposure** if pending claims could trigger 215 ILCS 5/154.6, NY Reg 64, CA Fair Claims, TX 542, GA 60-day rules
- **Surplus-lines diligent-search documentation** if the marketing path is E&S
- **AI disclosure** required on the consumer-facing artifacts (TRAIGA, AB 489, IN HB 1271 if applicable)
- **E&O exposure** from coverage gaps the renewal proposes (e.g., dropping employment practices when an insured has crossed the EPLI-trigger employee count)
- **BOR letter risk** if a competitor has been quoting this account

### Output Requirements

Produce **three paired deliverables**:

1. **Renewal Review Brief** (long form) — Sections 1–7 above; 800–1,500 words depending on complexity; tables where listed; saved as `outputs/renewal-brief-<insured>-<eff-date>.md`
2. **One-Page Renewal Executive Summary** — for the producer to walk into a leadership meeting with: header, retention tier and rule, loss verdict, recommended strategy, estimated premium band, agency revenue impact, top 3 risks, T-90/T-60/T-30/T-0 dates, signer block; ≤ 350 words; saved as `outputs/renewal-onepager-<insured>-<eff-date>.md`
3. **AMS / CRM Activity-Log Handoff** — 3-line block ready to paste into Applied Epic / AMS360 / HawkSoft / Vertafore / Salesforce: `Account · Verdict · Next Action · Date · Owner`; one line; ≤ 200 characters total

Plus, when applicable:

- **Cross-Sell Trigger List** for the Cross-Sell Opportunity Analyzer (one bullet per trigger)
- **Marketing Submission Brief Stub** for the Submission Intake Summarizer v3.0 if Remarket is the verdict (target carriers, appetite-prioritized; producer notes)
- **Compliance Memo Stub** for the Compliance Checklist Generator if Decline-to-Renew or material restructure (state-notice deadline, AI-disclosure trigger, producer-licensing footer)

### Formatting Rules

- Tables for premium summaries, loss history, and the strategy decision matrix
- Verdicts in **CAPS** (`PLATINUM`, `RESTRICTED`, `RENEW AS-IS`) so the reader sees them at a glance
- Currency formatted with thousands separators and the carrier's reporting convention from config (defaults to USD whole-dollar)
- Inline citations to the rule that drove a band — `(per config.agency.retention_tiers.gold rule G-2)` so any reviewer can audit how the brief got from inputs to verdict
- Distribution-scope label in the header: `INTERNAL ONLY` (default) / `PRODUCER + INSURED` / `PRODUCER + CARRIER`. Insured-distributable copies strip retention-tier scoring, agency contingency-program impact, producer commission impact, and any DECLINE-TO-RENEW commentary

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]

## Versioning

- v3.0 (this version) — Retention-tier rubric (Platinum / Gold / Standard / At-Risk / Decline-to-Renew) overrideable from config; carrier-appetite-map and contingency-posture pulled from `config.yml`; five-verdict strategy decision matrix (Renew As-Is / Re-Negotiate / Restructure / Remarket / Decline-to-Renew); quantified premium / coverage / agency-revenue / risk-of-loss-of-account outcome per verdict; T-minus action calendar driven by renewal effective date and state non-renewal-notice timeline; five-track producer talk-track tuned to verdict; three paired deliverables (Brief / One-Pager / AMS handoff); cross-references to Loss Run Analyzer, UW Risk Profile, Submission Intake Summarizer v3.0, Producer Live-Call Copilot, COI Compliance Reviewer, Cross-Sell Opportunity Analyzer, Compliance Checklist Generator. Distribution-scope label.
- v2.0 — Five-section structure (Account Snapshot, Loss Experience, Market Conditions, Renewal Strategy, Action Items), retention rating, remarket / restructure framework. (Now superseded — preserved as fallback only.)
