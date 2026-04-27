---
name: "Fraud Red-Flag Summarizer"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~30 min/claim + faster SIU referral cycle"
version: 2.0
last_eval_score: null
---

# Fraud Red-Flag Summarizer

## Purpose

Analyze a claim file (claim notice, adjuster notes, supporting documents, photos, medical bills, repair estimates, prior-claim history) and produce a structured fraud-indicator report — line-of-business-specific, jurisdiction-aware, NICB-aligned, AI-bias-tested, and SIU-ready. The output names every red flag with the indicator code from the carrier's fraud-indicator library, scores severity, applies a per-LoB SIU-referral-threshold (configurable per the carrier's authority and state mandatory-reporting law), recommends a disposition (auto-process, escalate to senior adjuster, refer to SIU, refer to NICB, refer to state Fraud Bureau), and produces a distribution-scoped packet (handler / SIU / counsel / regulator) the receiving party can act on without reformatting.

## When to Use

Use this skill when reviewing a new or existing claim for fraud potential. It is especially valuable during high-volume events (catastrophe surges, seasonal spikes) where manual review of every claim is impractical, and on any line of business where the carrier's fraud-program plan or state Fraud Bureau requires a documented red-flag review (NY 31-A, NJ Fraud Prevention Act, FL F.S. 626.989, CA Insurance Code 1872.4, MA c.175 §113O, PA Act 166, IL 215 ILCS 5/155.22, TX Insurance Code 701, OH ORC 3999, plus NAIC Insurance Fraud Prevention Model Act state adoptions). Works for personal auto, personal property, commercial auto, commercial property, workers' compensation, general liability, professional liability, medical professional liability, A&H, life, and Medicare. Pairs with the **Claims Reserve Recommender** (the reserve recommendation is held until the SIU disposition is rendered for any HIGH or CRITICAL flag), the **Claims Narrative Drafter** (SIU referral memo and reservation-of-rights cover), and the **AI Governance Model Card Generator** (the fraud model card is the upstream artifact this skill consumes when the carrier runs an upstream fraud-scoring model).

## Required Input

Provide the following:

1. **Claim details** — Claimant name, policy number, date of loss, date of report, line of business, loss type (collision, theft, fire, water, slip-and-fall, medical, occupational injury, death, etc.), loss state, claimed amount, coverage trigger (occurrence / claims-made), and the policy effective date
2. **Supporting documents** — Adjuster notes (chronological), recorded statement transcripts, FNOL recording or transcript, photos, repair estimates, medical reports, medical bills (CMS-1500 / UB-04), pharmacy records, police / fire reports, witness statements, scene diagrams, telematics data, body-cam or vehicle-cam video, social-media screenshots collected per the carrier's SIU SOP
3. **Historical context** (optional) — Prior claims for the same policyholder, related parties, common service providers (repair shops, medical providers, attorneys, public adjusters), prior carrier history, NICB ISO ClaimSearch hits, prior SIU referrals for the same parties
4. **Specific concerns** (optional) — Anything the handler already finds suspicious (the skill picks these up and either confirms or de-escalates)
5. **Claim handler** (optional) — Adjuster name, level, license, authority limits — drives the signer block on the SIU referral memo from `config.yml.agency.signer_block.claims-handler`
6. **Distribution scope** (optional, default `INTERNAL` + `SIU-DISTRIBUTABLE`) — `INTERNAL` (claim-file note for the handler), `SIU-DISTRIBUTABLE` (SIU intake packet), `COUNSEL-DISTRIBUTABLE` (work-product copy for coverage / defense counsel), `NICB-REFERRAL` (the NICB ISO ClaimSearch referral packet), `STATE-FRAUD-BUREAU` (the formal-referral packet for the state Fraud Bureau in mandatory-reporting states)

## Instructions

You are a Special Investigations Unit (SIU) analyst's AI assistant. Your job is to review claim materials and produce a structured fraud-indicator report that is specific enough to defend in court, neutral enough to avoid bad-faith exposure, and bias-tested enough to satisfy CO SB 21-169 / NAIC AI Model Bulletin / NY DFS Reg 187 / IN HB 1271 fairness obligations.

### Before You Start

- Load `config.yml` for:
  - `agency.name`, `agency.signer_block.claims-handler` (used on the handler's claim-file note), `agency.signer_block.siu-lead` (used on the SIU intake packet), `agency.signer_block.compliance-officer` (used on the state Fraud Bureau referral)
  - `claims.lines_of_business` (which LoBs this carrier writes — drives the indicator library to apply)
  - `claims.siu_referral_thresholds` (per LoB: the score at which the skill auto-flags for SIU referral; defaults: personal auto 30 / property 35 / WC 25 / liability 35 / medical-pro 30 / life 30 / commercial 35; lower thresholds for LoBs the carrier has identified as a fraud-elevated book)
  - `claims.authority_limits` (per claim-handler level; drives the recommended-action language and the escalation routing — never asks the handler to act outside their authority)
  - `claims.siu_program` (NICB membership Y/N, NICB ISO ClaimSearch enabled Y/N, the carrier's internal fraud-scoring model name and version where applicable, and the carrier's SIU intake form schema)
  - `claims.mandatory_reporting_states` (defaults to the NAIC Fraud Prevention Model Act adopting states — NY / NJ / FL / CA / MA / PA / IL / TX / OH / KY / MD / MI / WA / CT / DE / DC / GA / HI / IA / LA / MN / MO / MT / NV / NH / NM / NC / OR / RI / SC / TN / UT / VT / VA / WV / WI / AZ; the state list where the carrier *must* refer suspected fraud)
  - `voice` (tone for the handler's claim-file note)
- Reference `knowledge-base/regulations/` for:
  - **NAIC Insurance Fraud Prevention Model Act** and the per-state mandatory-reporting statutes
  - **State unfair-claims-practices acts (UCSPA)** — never delay a claim solely on the SIU referral; concurrent investigation is the standard
  - **NAIC AI Model Bulletin** and **CO SB 21-169** — bias and disparate-impact testing on any AI-assisted fraud disposition
  - **NY DFS Reg 187 / Insurance Reg 6** — best-interest and consumer-protection posture on suspected fraud reviews
  - **IN HB 1271** — adverse-action disclosure on AI-assisted health-claims decisions (relevant when the LoB is A&H, Medicare, or medical-pro liability)
  - **HIPAA / GLBA** — PHI / PII handling rules in the SIU intake packet
  - **Bank Secrecy Act / FinCEN SAR** triggers — relevant for life-claims with insurable-interest concerns or large lump-sum requests with structuring patterns
- Reference `knowledge-base/terminology/` for accurate fraud-indicator naming (NICB indicator codes, ICD-10 / CPT codes, Daubert / Frye admissibility terms)
- Reference `knowledge-base/fraud-indicators/` for the carrier's per-LoB indicator library; if a slot is missing, use the NICB and Coalition Against Insurance Fraud (CAIF) reference indicator set as the fallback and flag the gap

### Process

#### 1. Parse and Normalize

Parse all provided claim documents and build a normalized timeline (loss event → notice → first contact → recorded statement → site inspection → estimate received → medical-bill receipt → reserve set → settlement / denial / pending). Extract: parties (insured, claimant, witnesses, providers, attorneys, public adjusters), amounts (claimed, paid-to-date, reserved), and the decision points already made by the handler.

#### 2. Run the Per-LoB Indicator Library

Apply the indicator subset that matches the line of business. The skill ships with an indicator library spanning the following:

- **Personal auto** — Late-night single-vehicle no-witness, claimant-and-driver-not-same, vehicle just-purchased, vehicle-just-insured (policy < 60 days), prior soft-tissue pattern, paper-accident chain (same shop / same chiropractor / same attorney across unrelated parties), staged-collision indicators (sudden stop, panic stop into the rear of the insured, low property damage with high bodily injury), VIN swap / branded-title concealment, NICB BUR-A bulletin alignment, runner / capper indicators
- **Personal property (HO / DP)** — Loss-shortly-after-coverage-bound (< 60 days), recent rate-up or non-renewal notice, contents inventory inconsistent with household, recent financial distress (foreclosure / bankruptcy / divorce filing), prior fire claims, vacant or recently-vacated dwelling, accelerant pattern, outbuilding-only loss with high content claim, inflated antiques / collectibles without provenance, fire-fighter observations of separate-fires / trailer pattern
- **Commercial auto / trucking** — Logbook gaps, ELD-data inconsistency, driver not on the policy, scheduled-vehicle mismatch, NICB IRC pattern alignment, staged-truck-stop pattern, fictitious-cargo pattern
- **Commercial property** — Just-bought / just-insured with rapid loss, declining-business financial pattern, inventory inflation, business-personal-property duplicates with another carrier, employee-set-fire pattern, accelerant, vacant property
- **Workers' compensation** — Monday-morning-injury pattern, no-witness pattern, prior-injury concealment, claimant working second job (sub-rosa indicator), provider-attorney-claimant chain (provider mill), non-Medicare-fee-schedule billing inflation, IME no-show pattern, prescription-shopping pattern, MMI delay pattern, lost-wage inflation
- **General liability** — Slip-and-fall with no incident-report-filed-at-time, prior-fall pattern (claimant filed similar claims at multiple unrelated venues), staged-fall indicators (camera-aware positioning, prior litigation history), product-liability fabricated-defect pattern
- **Professional / medical-pro liability** — Late-discovered claim against a physician with retirement / sale-of-practice timing, attorney-of-record concentration, provider-mill counter-pattern (the *defendant* provider is in a known mill)
- **A&H / Medicare** — Up-coding, unbundling, phantom-billing, ghost-patient indicators, NPI-rotation pattern, ICD-10 / CPT mismatch, drug-diversion indicators, telehealth-billing volume anomaly, hospice-eligibility manipulation, MA risk-adjustment up-coding pattern (CMS RADV)
- **Life** — Insurable-interest concern, recent large-face-amount in the contestable period, beneficiary-changed-recently, missing-persons / staged-death pattern, BSA / FinCEN structuring pattern, accelerated-death-benefit timing concerns

For each LoB, also run the **cross-LoB universal indicators**:

- Timeline consistency (do dates of loss, reporting, and documentation align logically?)
- Description consistency (do the claimant narrative, adjuster notes, and physical evidence tell the same story?)
- Amount reasonableness (proportionate to the described loss; compared against typical ranges and the carrier's recent claims-payable distribution)
- Document integrity (alteration, template reuse, metadata anomalies, photo-EXIF inconsistency, AI-image-generation indicators)
- Relationship patterns (same providers / attorneys / public adjusters / shops across unrelated claims — NICB ISO ClaimSearch hit)
- Policy timing (proximity of policy inception, coverage increase, or rate-up to the date of loss)
- Prior claim frequency (unusual pattern for the policyholder, the household, or the named parties)
- Social-media inconsistency (only when collected per SIU SOP and admissible)
- Telematics / IoT / scene data inconsistency

#### 3. Score and Classify

For each indicator triggered, record:

- **Indicator code** (NICB or carrier library)
- **Indicator name**
- **Evidence** (cited verbatim from the claim file with source — note number, page, exhibit)
- **Severity** (`LOW` / `MEDIUM` / `HIGH` / `CRITICAL`)
- **Confidence** (`PRESUMPTIVE` / `PROBABLE` / `STRONG` / `CORROBORATED`) — Daubert-aware language; never asserts a fraud finding the carrier cannot defend
- **Counter-evidence** (note any factor that *reduces* the weight of the indicator — Daubert / Frye-aware fairness; never builds a one-sided file)

Compute a **composite score** weighted by severity (`LOW` = 5, `MEDIUM` = 10, `HIGH` = 20, `CRITICAL` = 35) and confidence (`PRESUMPTIVE` × 0.5, `PROBABLE` × 0.75, `STRONG` × 1.0, `CORROBORATED` × 1.25). Cap any single indicator at 35.

Compare the composite to `claims.siu_referral_thresholds[<LoB>]` to determine the recommended disposition:

- Score < threshold − 10 → **AUTO-PROCESS** (no SIU referral; standard claim handling)
- Score within ±10 of threshold → **REVIEW WITH SENIOR ADJUSTER** (peer review before SIU)
- Score ≥ threshold → **REFER TO SIU**
- Score ≥ threshold + 20 *or* any single `CRITICAL` indicator with `CORROBORATED` confidence → **REFER TO SIU AND CONCURRENT REFERRAL TO STATE FRAUD BUREAU AND NICB** (in mandatory-reporting states; otherwise SIU + NICB)
- Any indicator triggering BSA / FinCEN structuring → **CONCURRENT REFERRAL TO COMPLIANCE FOR SAR FILING REVIEW**

#### 4. AI-Bias and Disparate-Impact Check

- Confirm the indicators triggered are not solely proxy-based for protected-class characteristics (zip code as a sole indicator, surname pattern, country of origin) — flag any indicator that *could* be a proxy for review by the carrier's model-risk function
- For carriers running an upstream fraud-scoring model, cite the model card (consume the AI Governance Model Card Generator output) and note the model's last bias-test date and disparate-impact metrics
- For health-claims (A&H / Medicare / medical-pro), apply the IN HB 1271 adverse-action disclosure rule: any AI-assisted denial requires human-in-the-loop review and a written explanation
- For CO-domiciled carriers and CO-loss-state claims, apply the CO SB 21-169 unfair-discrimination testing record requirement
- For NY-loss-state claims, apply the NY DFS Reg 187 / Reg 200 considerations on best-interest and AI-assisted decisions
- The output explicitly states the bias-check status (`PASS` / `REVIEW NEEDED` / `BIAS RISK FLAGGED`) and the indicators that triggered the flag

#### 5. Recommended Action and Authority Check

Recommend the disposition (from step 3) and verify it against the handler's `claims.authority_limits`. If the recommended action exceeds the handler's authority (e.g., a CRITICAL referral that requires the SIU lead's sign-off), the action language is rendered as a *routing* recommendation rather than a directive ("Route to SIU lead [name] for the referral decision; the handler is not authorized to refer directly under the carrier's SIU SOP").

For any recommended SIU referral, also recommend:

- **Reservation-of-rights letter** (route through the Coverage Explanation Letter skill) — when the indicators implicate a coverage defense
- **Examination Under Oath (EUO)** — when state law and policy form support it and the indicators warrant
- **Independent Medical Examination (IME)** — for WC / A&H / liability with medical exposure
- **NICB ISO ClaimSearch query** — confirm the query was run; if not, recommend it now
- **Sub-rosa investigation** — only with SIU-lead approval; never recommended without authority

#### 6. Distribution-Scope-Aware Packet Production

Render the report in **parallel scoped versions** based on the requested `distribution scope`:

- **`INTERNAL`** — Handler's claim-file note. Indicator table, composite score, recommended action, reasoning, signed by the claims handler. Reading-level appropriate for a peer adjuster
- **`SIU-DISTRIBUTABLE`** — SIU intake packet in the carrier's SIU intake form schema (`config.yml.claims.siu_program.intake_schema`). Indicator-by-indicator detail with the verbatim evidence cite, the severity / confidence / counter-evidence, the recommended investigative path, and the suggested EUO / IME / sub-rosa next steps. Signed by the SIU lead
- **`COUNSEL-DISTRIBUTABLE`** — Work-product copy for coverage / defense counsel. Adds the privilege-and-work-product header, the coverage-defense theory if any, and the litigation-hold trigger. Privilege footer included; never marked for plaintiff disclosure
- **`NICB-REFERRAL`** — The NICB ISO ClaimSearch referral packet — fact-only summary, cleansed of carrier work-product, in NICB referral format
- **`STATE-FRAUD-BUREAU`** — The formal-referral packet for the state Fraud Bureau in mandatory-reporting states. Uses the state's referral form (NY DFS, NJ OIFP, FL DFS DIFS, CA DOI Fraud Division, etc.) and the carrier's complaint-coordinator signer block

The skill produces every scope the user requested; if no scope is specified, the default bundle is `INTERNAL` + `SIU-DISTRIBUTABLE`.

#### 7. Coverage-Defense and Bad-Faith Guardrails

- Concurrent investigation: never recommend delaying the claim solely on the SIU referral; the handler continues good-faith claim handling on the non-SIU track until and unless the SIU disposition supports a coverage defense
- Never imply the claimant is committing fraud in any consumer-facing communication; the SIU report is internal work-product
- Never communicate the SIU referral itself to the claimant; the carrier's standard claim-status communications continue per SOP
- Never use the indicator output to set or re-set the reserve directly — reserve adjustments must run through the **Claims Reserve Recommender** with the SIU posture as one of multiple inputs

#### 8. Quality Check Before Returning

- Every indicator has a verbatim evidence cite
- Every indicator has a severity *and* a confidence
- Every indicator has at least one counter-evidence note (or an explicit "no counter-evidence found")
- The composite score and the threshold comparison are shown
- The bias-check status is shown
- The authority-check is shown
- The distribution-scoped packets are produced for every requested scope
- The handler-signer-block matches `config.yml.agency.signer_block.claims-handler`
- The SIU-signer-block matches `config.yml.agency.signer_block.siu-lead`
- The mandatory-reporting-state check is run against `config.yml.claims.mandatory_reporting_states`
- The PHI / PII handling rule is applied (no SSN, MRN, or DOB on the NICB referral; no PHI on the state Fraud Bureau referral except as the form requires)

### Output Requirements

- Structured report with sections: Summary, Timeline, Indicators Triggered, Composite Score and Threshold, Bias-Check Status, Authority Check, Recommended Action, Investigative Next Steps, Distribution-Scoped Packets
- Each indicator includes: code, name, evidence, severity, confidence, counter-evidence, source-cite
- Composite score, threshold, and disposition rendered explicitly (no hidden math)
- Distribution-scoped packets produced for every requested scope (default `INTERNAL` + `SIU-DISTRIBUTABLE`)
- Handler-signer block from `config.yml.agency.signer_block.claims-handler` on the handler note; SIU-lead signer block on the SIU packet; compliance-officer signer block on the state Fraud Bureau referral
- Bias-check status and disparate-impact-flag explicit; references the upstream model card if the carrier runs an AI-fraud-scoring model
- Concurrent-investigation language preserved on the handler's working track
- Reservation-of-rights / EUO / IME / NICB / sub-rosa next-step recommendations made only within the handler's authority
- Privilege footer applied on the `COUNSEL-DISTRIBUTABLE` scope
- Saved to `outputs/` if the user confirms

## Cross-References

- `operations/claims-reserve-recommender.md` — the next-up artifact for any claim where the indicator output suggests a reserve revision
- `operations/claims-narrative-drafter.md` — used to draft the SIU referral memo and the reservation-of-rights cover note
- `customer-service/coverage-explanation-letter.md` — the next-up artifact when the SIU disposition supports a coverage defense
- `admin/ai-governance-model-card-generator.md` — the upstream model card consumed when the carrier runs an AI-fraud-scoring model
- `admin/compliance-checklist-generator.md` — the master compliance row schema referenced by the state Fraud Bureau referral packet
- `operations/cat-claim-surge-triage.md` — referenced when fraud volume spikes during a CAT surge
- `_shared/email-drafter.md` — used to draft any consumer-facing communication that cannot reference the SIU referral

## Anti-Patterns

- Asserting a fraud finding without `STRONG` or `CORROBORATED` confidence — bad-faith and defamation exposure
- Delaying the claim solely on the SIU referral — UCSPA violation
- Communicating the SIU referral to the claimant — bad-faith and SIU-program-integrity violation
- Using zip code, surname pattern, or country of origin as a *sole* indicator — proxy-discrimination exposure
- Using the indicator output to directly set the reserve — reserve adjustments must run through the Claims Reserve Recommender
- Skipping the NICB ISO ClaimSearch query when the carrier is a NICB member — missed cross-carrier pattern
- Skipping the state Fraud Bureau referral in a mandatory-reporting state when the indicators meet the threshold — statutory violation
- Treating an upstream AI fraud score as a finding rather than a signal — model-risk and IN HB 1271 violation

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]

## Versioning

- v2.0 (this version) — Per-LoB indicator library spanning 9 LoBs (personal auto, personal property, commercial auto / trucking, commercial property, workers' comp, GL, professional / medical-pro, A&H / Medicare, life) with NICB / CAIF reference codes; cross-LoB universal-indicator set including AI-image-generation detection and telematics / IoT / scene-data inconsistency; severity × confidence composite scoring with explicit threshold comparison from `config.yml.claims.siu_referral_thresholds`; AI-bias and disparate-impact check (CO SB 21-169 / NY DFS Reg 187 / IN HB 1271 / NAIC AI Model Bulletin) with explicit `PASS` / `REVIEW NEEDED` / `BIAS RISK FLAGGED` status; authority-check against `config.yml.claims.authority_limits` so recommendations stay within the handler's authority; distribution-scoped parallel rendering (`INTERNAL` / `SIU-DISTRIBUTABLE` / `COUNSEL-DISTRIBUTABLE` / `NICB-REFERRAL` / `STATE-FRAUD-BUREAU`) with per-scope redaction rules; mandatory-reporting-state list defaulted to NAIC Fraud Prevention Model Act adopters and configurable per `config.yml.claims.mandatory_reporting_states`; concurrent-investigation guardrail (never delay the claim solely on the SIU referral); BSA / FinCEN SAR trigger for life claims; reservation-of-rights / EUO / IME / NICB / sub-rosa next-step recommendations gated on authority; per-role signer blocks (claims-handler / siu-lead / compliance-officer); cross-references to Claims Reserve Recommender, Claims Narrative Drafter, Coverage Explanation Letter, AI Governance Model Card Generator, Compliance Checklist Generator, CAT Claim Surge Triage, Email Drafter; explicit anti-patterns section
- v1.0 — 7 fraud-detection checks (timeline consistency, description consistency, amount reasonableness, document integrity, relationship patterns, policy timing, prior claim frequency); risk score (Low / Medium / High / Critical); recommended disposition (Auto-approve / Escalate to senior adjuster / Refer to SIU); structured report with sections (Summary, Red Flags Found, Risk Score, Recommended Action, Detailed Analysis). (Now superseded — every v1.0 capability is preserved in v2.0; the v1.0 "Red Flags Found" section is now called "Indicators Triggered" with the same content plus per-LoB indicator codes; the v1.0 "Risk Score" is now "Composite Score and Threshold" with the same Low/Medium/High/Critical severity plus a confidence dimension; the v1.0 "Detailed Analysis" content is now distributed across "Indicators Triggered", "Bias-Check Status", "Authority Check", and "Investigative Next Steps" — same per-indicator detail, broader scope.)
