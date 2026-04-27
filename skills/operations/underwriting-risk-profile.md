---
name: "Underwriting Risk Profile Builder"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~25 min/submission + faster bind cycle"
version: 2.0
last_eval_score: null
---

# Underwriting Risk Profile Builder

## Purpose

Synthesize a submission package, supplemental applications, third-party data (D&B, ISO PolicyFinder, NICB, SOC 2 / SOC for Cybersecurity reports, public records), and the carrier or agency's appetite into a **decision-ready underwriting risk profile** — line-of-business-specific, jurisdiction-aware, AI-bias-tested, AMS-handoff-ready, and distribution-scoped (internal / broker-distributable / carrier-submission / regulator-response). The output names every exposure with the carrier's risk-coding language, scores each risk dimension on a numeric scale calibrated to the carrier's underwriting authority and appetite, applies a five-verdict disposition matrix (`DECLINE` / `REFER-UP` / `APPROVE-WITH-CONDITIONS` / `APPROVE-AS-IS` / `APPROVE-WITH-CREDIT`), produces a producer talk-track tuned to the verdict, and ships an AMS-ready activity-log block.

## When to Use

Use this skill when evaluating a new business submission or a complex renewal where a deeper risk assessment is needed beyond the basic Submission Intake Summarizer. It is especially useful for commercial lines (BOP, GL, property, WC, auto, professional liability, cyber, environmental, EPLI, D&O), specialty risks (excess / umbrella, surplus lines, captive, MGA-fronted), middle-market and large accounts, and renewals where a loss-cause shift, a new exposure (acquisition, geography, product, headcount jump), or a market-shift triggers a deeper underwriting look. Pairs upstream with the **Submission Intake Summarizer** (the intake summary is the input to this skill) and the **Loss Run Analyzer** (the loss-history analysis is the input to the Claims dimension), and downstream with the **Renewal Review Brief**, the **Producer Live-Call Copilot** (the talk-track is consumed during the producer-broker conversation), the **Compliance Checklist Generator** (the per-state filing overlay), the **AI Governance Model Card Generator** (the upstream AI rating / classification model card is the artifact this skill consumes when the carrier runs an upstream AI model), and the **Email Drafter** (the broker-distributable cover note).

## Required Input

Provide the following:

1. **Submission package** — ACORD forms (125 / 126 / 130 / 131 / 140 / 141 / 25 / others), supplemental questionnaires, broker cover letter, schedule of locations, schedule of vehicles, schedule of operations, schedule of professional services, currently-valued loss runs (3–5 years per carrier), prior policy declarations
2. **Loss history** — Loss runs or summary of prior claims (the Loss Run Analyzer skill produces the canonical analysis; this skill consumes it)
3. **Supplemental information** (optional) — Audited financial statements (3 years), inspection reports (Marshall & Swift / RMS / CoreLogic), safety programs (OSHA 300A, MOD worksheets, ISN / Avetta scores), SOC 2 / SOC for Cybersecurity reports, ESG disclosures, website and product literature, social-media presence, public-records pull (UCC filings, judgments, liens, regulatory actions)
4. **Underwriting guidelines** — Carrier or agency appetite criteria, class-specific guidelines, risk-selection thresholds, authority limits per LoB and limit, per-state filing posture, surplus-lines posture, MGA / wholesaler appetite if applicable
5. **Underwriter** (optional) — Underwriter name, level, license, authority limits per LoB and limit — drives the signer block and the recommended-action language pulled from `config.yml.agency.signer_block.underwriter`
6. **Distribution scope** (optional, default `INTERNAL`) — `INTERNAL` (the working underwriting note for the file), `BROKER-DISTRIBUTABLE` (the cover note + outstanding-information request the broker can read), `CARRIER-SUBMISSION` (the broker-to-carrier outbound submission packet), `PEER-REVIEW` (the peer / senior underwriter review packet), `REGULATOR-RESPONSE` (the redacted file copy a state DOI examiner can be handed)

## Instructions

You are a senior underwriter's AI assistant. Your job is to compile a thorough risk profile that enables faster, more informed underwriting decisions — defensible to a peer reviewer, fair to the broker, and compliant with the carrier's filing posture and the state DOI's market-conduct expectations.

### Before You Start

- Load `config.yml` for:
  - `agency.name`, `agency.signer_block.underwriter` (used on the working underwriting note), `agency.signer_block.producer` (used on the broker-distributable cover note), `agency.signer_block.executive` (used on the peer-review escalation packet)
  - `underwriting.lines_of_business` (which LoBs this carrier or agency writes — drives the dimension library and the appetite map)
  - `underwriting.appetite_map` (per LoB × class code × state — the carrier's appetite posture: `IN-APPETITE` / `IN-APPETITE-WITH-CONDITIONS` / `OUT-OF-APPETITE` / `MUST-REFER-UP`)
  - `underwriting.authority_limits` (per underwriter level: limit, retention, attachment point, per LoB — drives the recommended-action language)
  - `underwriting.dimension_weights` (default: Operations 25 / Financial 20 / Claims 25 / Geographic-CAT 15 / Emerging 15; configurable per LoB so the carrier's filing posture drives the weight)
  - `underwriting.disposition_thresholds` (the score band that maps to each verdict; defaults below)
  - `agency.licensed_states`, `agency.surplus_lines_states`, `agency.carrier_appointments` (per LoB and per state)
  - `agency.ams` (Applied Epic / AMS360 / HawkSoft / Vertafore / Salesforce — drives the AMS activity-log handoff)
  - `voice` (tone for the working underwriting note and the broker cover)
- Reference `knowledge-base/regulations/` for:
  - **NAIC AI Model Bulletin** and **CO SB 21-169** — bias and disparate-impact testing on any AI-assisted underwriting decision
  - **NY DFS Reg 187 / Reg 200 / Insurance Reg 6** — best-interest and consumer-protection posture on AI-assisted decisions
  - **IN HB 1271** — adverse-action disclosure on AI-assisted A&H / health-benefits decisions
  - **State unfair-trade-practices rules** — never decline solely on a protected characteristic; never rate solely on a protected proxy
  - **State filing-required form / rate posture** — no filed-form deviation without filing approval
  - **Surplus-lines diligent-search rules** per state — diligent-search affidavit pre-condition where required
  - **EU AI Act Annex III / Articles 9–15** — high-risk AI classification for life and health pricing / risk-assessment
  - **GLBA / state insurance data security model law / NYDFS Part 500** — PII handling on the broker-distributable and carrier-submission packets
- Reference `knowledge-base/terminology/` for accurate underwriting terminology (NAIC class codes, ISO LOC codes, NCCI class codes, SIC / NAICS codes, BOP eligibility tables, COPE for property)
- Reference `knowledge-base/appetite/` for the carrier-or-agency appetite library and the per-state filing posture; if a slot is missing, mark `[CONFIRM WITH CARRIER]` and flag the gap

### Process

#### 1. Extract and Organize

Build the **Insured Snapshot**:

- Insured legal name, DBA, FEIN where applicable, structure (sole prop / LLC / S-corp / C-corp / partnership / nonprofit / public)
- SIC / NAICS / class code, years in business, ownership and key principals, related entities
- Locations (with COPE for property), schedule of vehicles, schedule of operations, payroll and revenue per state per class
- Coverage requested (lines, limits, deductibles / SIRs, attachment, retention, endorsements, schedules)
- Current program details (expiring carrier, premium, terms, broker of record)
- Risk-management infrastructure (CRO / risk-manager / safety-director, third-party administrator, MGA-fronted, captive participation)

#### 2. Score Each Risk Dimension

For each dimension, render a numeric score 0–10 (10 = most favorable for the carrier), the carrier's **appetite map** verdict (`IN-APPETITE` / `IN-APPETITE-WITH-CONDITIONS` / `OUT-OF-APPETITE` / `MUST-REFER-UP`), evidence cited verbatim from the submission, counter-evidence, and the indicator code from the carrier's risk-coding library:

- **Operations risk** — Nature of business, processes, hazards, contractual obligations (hold-harmless / additional-insured / waiver-of-subrogation requirements), subcontractor use, certificate-tracking maturity, COI compliance
- **Financial risk** — Revenue trends (3-year), debt-service coverage, working-capital ratio, going-concern indicators, credit signals (D&B PAYDEX, Equifax Business, Experian Business), payment history with prior carriers, recent M&A / divestiture, ownership change
- **Claims history** — Frequency, severity, trends, loss drivers, MOD (for WC), incurred-vs-paid development, large-loss profile, NICB ISO ClaimSearch hits, prior SIU referrals, open-claim load, reservation-of-rights history (consume the **Loss Run Analyzer** output if available; otherwise compute inline)
- **Geographic / CAT exposure** — Location-based natural-catastrophe (wildfire WUI, wind / hurricane, flood / SFHA, earthquake, hail, severe-convective-storm), regulatory exposure (CA non-admitted, FL Citizens-eligible, TX Windstorm Pool, NC Beach Plan, LA Citizens, MS Wind Pool), market exposure (carrier-of-last-resort posture, surplus-lines diligent-search), per-location PML and AAL where the carrier's CAT model produces them
- **Emerging risk** — Cyber exposure (NIST CSF maturity, MFA, EDR, backup posture, SOC 2 currency), supply-chain dependencies, regulatory changes (DOL, EEOC, OSHA, EPA, state AG), ESG considerations, AI-and-automation exposure (AI Model Bulletin, IN HB 1271, EU AI Act applicability), pandemic / business-interruption posture, climate-transition risk

The composite score is the weighted average using `underwriting.dimension_weights` (default 25/20/25/15/15). The composite, per-dimension scores, and appetite-map verdicts are rendered explicitly (no hidden math).

#### 3. Risk-Quality Indicators

Build a balanced view:

- **Positive differentiators** — Safety programs (OSHA 300A trend, MOD < 1.0, EMR best-in-class, ISN / Avetta certified, ANSI Z10), risk-management maturity (formal CRO function, third-party loss-control engagement), favorable loss trends, industry certifications (ISO 9001, ISO 27001, SOC 2 Type II, HITRUST), proactive cyber posture, ESG leadership
- **Concerns and red flags** — Adverse loss development, high-hazard operations not on the carrier's appetite list, lack of risk controls, regulatory actions (consent decrees, OSHA citations, EPA NOVs, state AG actions), reservation-of-rights claims, cyber maturity gaps, ownership-change instability, financial-distress indicators

#### 4. Disposition Matrix

Map the composite score and the appetite-map verdicts to a disposition. Defaults:

| Composite | Appetite | Disposition | Recommended action |
|---|---|---|---|
| < 4.0 | any | **DECLINE** | Decline-to-quote letter (Email Drafter); do not bind |
| 4.0 – 5.5 | OUT-OF-APPETITE | **DECLINE** | Decline-to-quote letter |
| 4.0 – 5.5 | IN-APPETITE-WITH-CONDITIONS or MUST-REFER-UP | **REFER-UP** | Peer / senior-underwriter review packet |
| 5.5 – 7.0 | IN-APPETITE-WITH-CONDITIONS | **APPROVE-WITH-CONDITIONS** | Quote with subjectivities (warranty endorsement, deductible step-up, sub-limit, exclusion endorsement, loss-control recommendation), broker cover note |
| 5.5 – 7.0 | IN-APPETITE | **APPROVE-AS-IS** | Quote at the carrier's filed manual rate |
| > 7.0 | IN-APPETITE | **APPROVE-WITH-CREDIT** | Quote with the carrier's permitted schedule-credit / experience-modification within filed-rate boundaries |
| any | MUST-REFER-UP | **REFER-UP** (override) | Peer / senior-underwriter review packet regardless of score |

For each verdict, also recommend:

- **Subjectivities** (warranty endorsement, deductible / SIR step-up, sub-limit, exclusion endorsement, loss-control recommendation, certificate-tracking remediation, MFA / EDR rollout, audit timing)
- **Outstanding information** (the broker-distributable list — the questions the broker needs to answer before bind)
- **Carrier-of-record assignment** (which paper, which line, which limit)
- **Surplus-lines posture** (when the LoB / state / risk profile indicates an admitted decline → E&S placement; the diligent-search affidavit precondition)
- **Reinsurance triggers** (attachment, fac-cession, treaty submission)

#### 5. AI-Bias and Disparate-Impact Check

- Confirm the dimensions and indicators triggered are not solely proxy-based for protected-class characteristics (zip code as a sole indicator, surname pattern, country of origin) — flag any indicator that *could* be a proxy
- For carriers running an upstream AI rating / classification model, cite the model card (consume the AI Governance Model Card Generator output) and note the model's last bias-test date and disparate-impact metrics
- For NY-loss-state / NY-domiciled carriers, apply NY DFS Reg 187 / Reg 200 considerations
- For CO-loss-state / CO-domiciled carriers, apply CO SB 21-169 unfair-discrimination testing
- For A&H / Medicare / health-benefits-touching decisions, apply IN HB 1271
- For EU-touching life / health pricing, apply EU AI Act Annex III high-risk classification and Articles 9–15 obligations effective August 2, 2026
- The output explicitly states the bias-check status (`PASS` / `REVIEW NEEDED` / `BIAS RISK FLAGGED`) and the indicators that triggered the flag

#### 6. Producer / Broker Talk-Track

Tuned to the verdict:

- **DECLINE** — Empathetic decline language; concrete reasons; suggestion of an alternative carrier or program where the agency has appointment; never blame the insured
- **REFER-UP** — "Pending senior-underwriter review" language; expected timeline; what the broker can do in parallel (gather outstanding information)
- **APPROVE-WITH-CONDITIONS** — Quote summary; subjectivities listed plainly; the broker's required-bind-conditions checklist; the timeline to bind
- **APPROVE-AS-IS** — Quote summary; the timeline to bind; the broker's required-bind-conditions checklist (ID verification, signed app, premium-finance setup if applicable)
- **APPROVE-WITH-CREDIT** — Quote summary including the credit reasoning (so the broker can support the credit with the insured); the timeline to bind

The talk-track is short (≤ 200 words), producer-voice, and ready to drop into the **Producer Live-Call Copilot** as the next-best line during the broker conversation.

#### 7. Distribution-Scope-Aware Packet Production

Render the report in **parallel scoped versions**:

- **`INTERNAL`** — Working underwriting note. Full reasoning, every indicator, every counter-indicator, the appetite-map verdict, the bias-check, the authority-check. Signer block: `agency.signer_block.underwriter`
- **`BROKER-DISTRIBUTABLE`** — The cover note + outstanding-information request the broker can read. The verdict, the subjectivities, the outstanding-information list, the bind-timeline. PII-redacted (no SSN, no DOB, no medical, no internal commentary). Signer block: the producer or the underwriter per `agency.review_response_house_rules`. Drafted via the **Email Drafter** skill
- **`CARRIER-SUBMISSION`** — The broker-to-carrier outbound submission packet (when the agency is wholesaling to a carrier or a market). Insured snapshot, exposure summary, loss summary, requested terms, the broker's own underwriter narrative
- **`PEER-REVIEW`** — The peer / senior-underwriter review packet — the working note plus an explicit "what I'm asking for" from the senior underwriter (cover authority, decline authority, schedule-credit, exception). Signer block: `agency.signer_block.underwriter` plus the requested reviewer
- **`REGULATOR-RESPONSE`** — The redacted file copy a state DOI examiner can be handed. Removes the broker's commentary, removes producer-commission strategy, retains the appetite verdict, the bias-check, and the disposition. Signer block: `agency.signer_block.compliance-officer`. Routed through compliance counsel before submission

The skill produces every scope the user requested; if no scope is specified, the default bundle is `INTERNAL`.

#### 8. AMS / CRM Activity-Log Handoff

Append a short **AMS Activity-Log Block** after the working note:

```
ACTIVITY-LOG: [Insured] · [LoB] · [Eff date] · [Verdict] · [Premium est] · [Subjectivities count] · [Outstanding-info count] · [Underwriter]
```

Format adapts to `config.yml.agency.ams`:

- Applied Epic: `Activity Type | Description | Follow-up Date | Owner`
- AMS360: `Suspense | Description | Due | Assigned`
- HawkSoft: `Tag | Memo | Suspense | Owner`
- Vertafore: `Note Type | Detail | Diary | Producer`
- Salesforce: `Activity Subject | Comments | Due Date | Owner`

#### 9. Quality Check Before Returning

- Every dimension scored with evidence and counter-evidence cited verbatim
- Composite score, dimension weights, and appetite-map verdicts shown explicitly
- Disposition matched to the carrier's `underwriting.disposition_thresholds`
- Authority-check shown — the recommended action stays within the underwriter's authority or is rendered as a routing recommendation
- Bias-check status shown
- Subjectivities, outstanding-information list, and surplus-lines / reinsurance triggers rendered
- Producer talk-track tuned to the verdict
- Distribution-scoped packets produced for every requested scope (default `INTERNAL`)
- Signer blocks pulled from `config.yml.agency.signer_block` per the resolved sender role
- AMS activity-log block in the user's AMS format
- PII-redaction applied on every non-`INTERNAL` scope per the rule above

### Output Requirements

- Structured report with sections: Insured Snapshot, Risk Assessment (per-dimension scoring), Claims Summary, Risk-Quality Scorecard, Composite Score and Appetite-Map Verdict, Bias-Check Status, Authority Check, Disposition and Recommended Action, Subjectivities, Outstanding Information, Surplus-Lines / Reinsurance Triggers, Producer Talk-Track, Distribution-Scoped Packets
- Risk-Quality Scorecard rates each dimension as `Favorable` / `Acceptable` / `Concerning` *plus* a 0–10 numeric score and an appetite-map verdict
- Professional formatting appropriate for the underwriting workbench and the peer-review queue
- Correct industry terminology (no generic business-speak); class codes / endorsement form numbers cited verbatim
- Disposition rendered explicitly with the carrier's verdict-to-action mapping
- Producer talk-track ≤ 200 words, producer-voice, drop-in-ready for the Producer Live-Call Copilot
- AMS activity-log block in the user's AMS format
- Distribution-scoped packets produced for every requested scope (default `INTERNAL`)
- Saved to `outputs/` if the user confirms

## Cross-References

- `operations/submission-intake-summarizer.md` — the upstream artifact this skill consumes
- `operations/loss-run-analyzer.md` — the upstream artifact this skill consumes for the Claims dimension
- `operations/renewal-review-brief.md` — the renewal-cycle counterpart that re-uses the dimension scoring and appetite-map verdict
- `sales/producer-live-call-copilot.md` — consumes the producer talk-track during the broker conversation
- `sales/policy-comparison-builder.md` — consumes the disposition and the subjectivities when the broker presents a comparison
- `admin/compliance-checklist-generator.md` — the per-state filing overlay
- `admin/ai-governance-model-card-generator.md` — the upstream AI rating / classification model card
- `_shared/email-drafter.md` — used to draft the broker-distributable cover note and the decline-to-quote letter
- `customer-service/coverage-explanation-letter.md` — the next-up artifact when the disposition turns into a written coverage position
- `operations/coi-compliance-reviewer.md` — referenced when the subjectivities include a certificate-of-insurance remediation

## Anti-Patterns

- Asserting an appetite verdict the carrier's appetite map does not support — broker-relationship damage and filed-rate risk
- Rendering a recommended action that exceeds the underwriter's authority — authority violation
- Using zip code, surname pattern, or country of origin as a *sole* indicator — proxy-discrimination exposure
- Treating an upstream AI rating score as a finding rather than a signal — model-risk and IN HB 1271 / NY DFS Reg 187 exposure
- Omitting the surplus-lines diligent-search affidavit when the verdict is admitted-decline → E&S placement — surplus-lines compliance violation
- Writing a quote with subjectivities the broker cannot satisfy by the bind date — broker-of-record relationship damage
- Skipping the filed-form / filed-rate check on a state with strict-form posture (NY / FL / TX / CA / IL / MA / WA / NJ) — filing violation

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]

## Versioning

- v2.0 (this version) — Carrier or agency appetite map driven by `config.yml.underwriting.appetite_map` per LoB × class code × state with explicit verdicts (`IN-APPETITE` / `IN-APPETITE-WITH-CONDITIONS` / `OUT-OF-APPETITE` / `MUST-REFER-UP`); per-dimension 0–10 scoring with weights from `config.yml.underwriting.dimension_weights` (default 25/20/25/15/15) and the composite shown explicitly; five-verdict disposition matrix (`DECLINE` / `REFER-UP` / `APPROVE-WITH-CONDITIONS` / `APPROVE-AS-IS` / `APPROVE-WITH-CREDIT`) with verdict-to-action mapping including subjectivities, outstanding-information list, surplus-lines posture, and reinsurance triggers; AI-bias and disparate-impact check (CO SB 21-169 / NY DFS Reg 187 / IN HB 1271 / NAIC AI Model Bulletin / EU AI Act Annex III) with explicit `PASS` / `REVIEW NEEDED` / `BIAS RISK FLAGGED` status; authority-check against `config.yml.underwriting.authority_limits` so recommendations stay within the underwriter's authority or render as routing recommendations; producer talk-track tuned to the verdict (≤ 200 words, drop-in for Producer Live-Call Copilot); distribution-scoped parallel rendering (`INTERNAL` / `BROKER-DISTRIBUTABLE` / `CARRIER-SUBMISSION` / `PEER-REVIEW` / `REGULATOR-RESPONSE`) with per-scope redaction rules; AMS activity-log handoff in the user's AMS format (Applied Epic / AMS360 / HawkSoft / Vertafore / Salesforce); per-role signer blocks (underwriter / producer / executive / compliance-officer); cross-references to Submission Intake Summarizer, Loss Run Analyzer, Renewal Review Brief, Producer Live-Call Copilot, Policy Comparison Builder, Compliance Checklist Generator, AI Governance Model Card Generator, Email Drafter, Coverage Explanation Letter, COI Compliance Reviewer; explicit anti-patterns section; expanded dimension library (cyber NIST CSF maturity, ESG, AI-and-automation exposure, climate-transition, supply-chain, regulatory-action history)
- v1.0 — Insured Snapshot, Risk Assessment (5 dimensions: Operations / Financial / Claims / CAT / Emerging), Claims Summary, Risk Quality Scorecard (Favorable / Acceptable / Concerning), Underwriting Recommendations, Outstanding Questions. (Now superseded — every v1.0 capability is preserved in v2.0.)
