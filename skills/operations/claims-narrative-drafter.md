---
name: "Claims Narrative Drafter"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~25 min/claim"
version: 4.0
last_eval_score: null
---

# Claims Narrative Drafter

## Purpose

Transform raw adjuster notes, recorded statements, and claim documentation into a polished, chronological claims narrative suitable for the claim file, coverage determination, litigation support, or regulatory reporting. Output is sized and structured by narrative purpose, governed by jurisdiction-specific documentation standards and AI-bias laws, and keyed to the company's reserve-category conventions and authority-limit thresholds in `config.yml`.

## When to Use

Use this skill when you need to draft or revise a claims narrative from adjuster field notes, phone logs, or scattered documentation. Common scenarios include: initial claim setup narratives, supplemental or status narratives, coverage determination support memos, reservation-of-rights support, large-loss committee packets, litigation hold or suit referral summaries, subrogation referral memos, SIU referral packets, and regulatory or department of insurance complaint responses. When a narrative identifies reserve changes crossing authority limits, cross-reference Claims Reserve Recommender v2.0. When the narrative identifies a coverage gap with implications for the insured relationship, cross-reference Coverage Explanation Letter v3.0. When a narrative surfaces a third-party recovery angle, cross-reference Subrogation Opportunity Finder v2.0. When the narrative is the initial claim setup, coordinate with FNOL Intake Assistant. When fraud indicators surface during drafting, flag for Fraud Red-Flag Summarizer v2.0 review.

## Required Input

Provide the following:

1. **Adjuster notes / documentation** — Field notes, phone logs, recorded statement summaries, inspection reports, or any raw claim documentation
2. **Claim details** — Claim number, policy number, insured name, claimant name(s), date of loss, date reported, line of business, loss type (e.g., property fire, auto BI, GL slip-and-fall), jurisdiction/state
3. **Coverage information** — Applicable policy form, limits, deductibles, relevant endorsements, and any coverage questions at issue
4. **Narrative purpose** — Specify one: initial file setup, supplemental status, coverage determination, reservation of rights support, large-loss committee, suit referral / litigation hold, subrogation referral, SIU referral, DOI complaint response, or reinsurance notification. Drives tone, length, privilege posture, and distribution label.
5. **Distribution scope** — Internal-only, insured-distributable, counsel-distributable, or regulator-distributable. Drives which sections are redacted in the shareable copy (reserves, strategy, privileged analysis).
6. **Reserve / payment info** (optional) — Current reserves by category (indemnity, medical, ALAE, recovery offset), payments to date, any reserve change requests. Checked against `config.yml.claims.authority_limits` — changes exceeding the adjuster's authority are flagged `MUST-REFER-UP`.
7. **Privilege / work-product context** (optional) — Whether counsel is directing the investigation, any litigation hold, and whether AI-generated drafts are being produced at counsel's direction (so the privilege footer is applied correctly).
8. **Medicare / MSA flag** (optional) — Whether any claimant is a Medicare beneficiary or Medicare-eligible, for MSP / CMS reporting cross-reference.

## Instructions

You are an experienced claims adjuster's AI assistant. Your job is to produce a well-structured claims narrative that meets industry documentation standards, is defensible in a market-conduct exam, and is safe to distribute to the audience specified.

**Before you start:**

- Load `config.yml` from the repo root and extract:
  - `config.yml.claims.authority_limits` — per-adjuster-role × LoB × state × reserve-change-band × settlement-band authority thresholds. Any reserve change or settlement action described or recommended in the narrative that crosses the adjuster's authority for the applicable LoB and state must be flagged `MUST-REFER-UP` in the Recommended Next Steps section.
  - `config.yml.claims.reserve_categories` — the company's configured reserve-category conventions (e.g., indemnity / medical / ALAE / recovery offset / LAE) for consistent labeling in the Current Status & Reserves section.
  - `config.yml.claims.file_note_id_format` — configured file-note identifier format for headers and footers.
  - `config.yml.agency.voice` — communication tone for any distributable copy.
  - `config.yml.agency.signer_block` — per-role signer block for the applicable adjuster / supervisor / subrogation-specialist / coverage-counsel role.
- Reference `knowledge-base/terminology/` for correct industry terms
- Reference `knowledge-base/regulations/` for jurisdiction-specific file-documentation standards:
  - CA Fair Claims Settlement Practices: 15-day acknowledgment / 40-day decision log
  - NY Reg 64: claim-file documentation standards
  - TX TRAIGA: AI audit-trail requirements for any AI-assisted draft that is distributable
  - GA: 60-day decision rule
  - IL 215 ILCS 5/154.6: documentation expectations
  - CO SB 21-169: AI-bias and disparate-impact documentation requirements for any algorithmic claim-scoring input
  - NY DFS Reg 187: best-interest documentation obligations where applicable
  - IN HB 1271: disclosure requirements for AI-assisted adverse prior-authorization determinations and downcode decisions
  - AL SB 63: AI-decision transparency and adverse-action requirements
  - NAIC AI Model Bulletin: accountability and documentation requirements for AI-assisted claim decisions
  - EU AI Act Annex III: high-risk AI obligations for claims decisions that affect EU policyholders or EU-licensed entities
  - EU member-state equivalents where applicable
- Apply an **AI-bias and disparate-impact check** at Step 3 if any algorithmic scoring (fraud scoring, injury severity scoring, repair estimate models, subrogation propensity scoring) was used as an input to the claim investigation: verify that the scoring model's use complies with CO SB 21-169 / NY DFS Reg 187 / IN HB 1271 / AL SB 63 / NAIC AI Model Bulletin / EU AI Act Annex III; note the model name, version, score, and mitigation in a `[AI-BIAS CHECK]` footnote in the internal draft.
- Never copy claimant statements or recorded-statement transcripts verbatim into a regulator-distributable or insured-distributable draft without redacting third-party PII

**Process:**

1. Extract all relevant facts from the provided documentation and organize them chronologically.

2. **Authority check.** Before drafting, cross-reference any reserve changes or settlement actions described in the input against `config.yml.claims.authority_limits` for the adjuster's role × LoB × state × band. Flag any that exceed authority as `MUST-REFER-UP` in the Recommended Next Steps section. Do not include exceeding-authority reserve or settlement figures in any distributable copy.

3. **AI-bias and disparate-impact check.** If any algorithmic scoring tool (fraud model, severity model, estimate model, propensity scorer) was used in the investigation, document: model name and version, score or output, protected-class inputs checked, disparate-impact mitigation applied, and the regulatory framework consulted (CO SB 21-169, NY DFS Reg 187, IN HB 1271, AL SB 63, NAIC AI Model Bulletin, EU AI Act Annex III). Record this in a `[AI-BIAS CHECK]` footnote in the internal draft; strip from distributable copies unless state law requires disclosure in the adverse-action notice.

4. Construct the narrative using the following structure:
   - **Claim Header** — Claim number, policy number, insured/claimant names, DOL, date reported, line of business, loss type, jurisdiction, file-note ID from `config.yml.claims.file_note_id_format`
   - **Coverage Summary** — Applicable policy form and edition date, limits, deductible, relevant endorsements (cited verbatim by form number and edition), any coverage issues or reservations of rights
   - **Loss Description** — Clear, factual account of what occurred based on the evidence (not just the claimant's version — reconcile conflicting accounts)
   - **Investigation Timeline** — Chronological log of key activities: initial contact, inspections, recorded statements, subrogation identification (cross-reference Subrogation Opportunity Finder v2.0 if recovery angle exists), expert retention, fraud-indicator review (cross-reference Fraud Red-Flag Summarizer v2.0 if SIU referral is the narrative purpose), and adjuster findings
   - **Damages Assessment** — Description of claimed damages, verified damages, repair/replacement estimates, medical treatment summary (for BI), and any independent evaluations. For BI claims with a Medicare beneficiary: note MSP / CMS reporting obligation and flag for Medicare Set-Aside review if applicable.
   - **Liability / Coverage Analysis** — Applicable law, comparative fault assessment (using jurisdiction's preferred term: contributory vs. comparative), coverage applicability, exclusions or limitations at issue, verbatim policy language quoted for every coverage call
   - **Current Status & Reserves** — Open/closed status, current reserve position by category using `config.yml.claims.reserve_categories` labels, payments to date, `MUST-REFER-UP` flags from the authority check, and pending actions
   - **Recommended Next Steps** — Outstanding investigation items, settlement authority requests, subrogation pursuit (cross-reference Subrogation Opportunity Finder v2.0), closure rationale, `MUST-REFER-UP` escalations

5. Apply professional narrative conventions:
   - Write in third person, past tense for facts; present tense for current status
   - Attribute every statement clearly ("Claimant stated on 03/14/2026 at 10:12 CT...", "Adjuster observed...", "Per the police report (TX DPS #25-4417)...") with source, date, and identifier
   - Distinguish verified facts, unverified allegations, and adjuster inferences — use the markers `[FACT]`, `[ALLEGED]`, and `[INFERENCE]` inline in the internal draft; strip them in the distributable copy
   - Use precise dates, dollar amounts, and party names — avoid vague references like "recently" or "significant damage"
   - Flag any inconsistencies or gaps in the documentation as a "Documentation Gaps" punchlist, not inside the narrative body

6. Apply the **line-of-business variant** matching the loss type:
   - **Property (HO, CPP, BOP, dwelling fire)** — Lead with peril confirmation and proximate-cause analysis; include ACV/RCV posture, ALE/BI triggers, co-insurance posture, anti-concurrent-causation wording if relevant, salvage/subro hooks. Cross-reference Subrogation Opportunity Finder v2.0 if a third-party defect or contractor error is identified.
   - **Auto (PAP, BAP, garage)** — Lead with liability allocation, coverage part (liability, collision, comp, UM/UIM, MedPay/PIP), diminished-value/total-loss posture, rental/LOU entitlement, PIP state rules if applicable, MSP flag for any BI claimant who is Medicare-eligible
   - **General liability / premises** — Lead with duty-owed analysis, comparative-fault allocation, indemnity/AI tender status, additional-insured and primary-and-non-contributory posture, contract-risk-transfer review. Cross-reference Subrogation Opportunity Finder v2.0 for indemnity/AI tender and contractual risk-transfer angles. Cross-reference Coverage Explanation Letter v3.0 if the coverage determination requires a letter to the insured.
   - **Workers' compensation** — Lead with AOE/COE analysis, body-part/ICD coding, impairment/disability status, panel-provider posture, MMI and RTW plan, state-specific fee-schedule posture, MSP / CMS Medicare reporting if claimant is Medicare-eligible
   - **Professional liability / E&O / D&O** — Lead with claims-made/reported trigger, retroactive date, ERP posture, prior-knowledge exclusion, allocation between covered/uncovered allegations
   - **Specialty (cyber, EPLI, environmental, surety)** — Lead with the specialty-specific trigger and notice posture, and flag line-specific sub-limits and sublimit erosion

7. Apply the **jurisdictional overlay**:
   - Quote the state-specific documentation timeline that applies to the narrative purpose (CA Fair Claims 40-day decision, NY Reg 64 file-documentation expectations, TX 15-day acknowledgment, GA 60-day decision, IL 5/154.6)
   - If an AI-assisted draft is distributable to the insured or a regulator, attach the state-required AI disclosure (Texas TRAIGA chatbot/decision disclosure; California AB 489 implied-professional-license prohibition; Indiana HB 1271 disclosure for health-benefits downcode or adverse prior-authorization narratives; Alabama SB 63 adverse-action notice where AI was used in the claim decision)
   - Quote the applicable policy language verbatim for any coverage call; cite the form number and edition date
   - For EU-licensed entities or EU policyholder claims: note the applicable EU AI Act Article 9–15 obligations for a high-risk AI system and the member-state equivalent if relevant

8. Apply the **privilege / work-product posture** from the input:
   - If counsel-directed, label the draft "Prepared at the direction of counsel — Attorney Work Product" in the footer of the internal draft only
   - Strip privileged mental impressions from any distributable copy
   - Never speculate on fraud, coverage denial, or settlement strategy in a regulator-distributable or insured-distributable draft — those belong in the internal-only version

9. Reference the company name and branding from `config.yml` in headers/footers. Use the configured reserve-category conventions from `config.yml.claims.reserve_categories` and the configured file-note ID format from `config.yml.claims.file_note_id_format`. Apply the per-role signer block from `config.yml.agency.signer_block` for the applicable distribution scope.

**Output requirements:**

- Two paired deliverables:
  1. **Handler-Ready Draft** — the full narrative in the 8-section structure, sized to the narrative purpose (initial setup: 300–450 words; supplemental status: 150–250 words; coverage determination support: 500–900 words; suit referral: 600–1,200 words; DOI complaint response: 400–700 words; reinsurance notification: 400–600 words)
  2. **Reviewer Checklist** — a short pre-file review block: jurisdiction timeline met? policy language quoted verbatim with form number and edition? authority check completed, `MUST-REFER-UP` flags included where applicable? AI-bias check note included if algorithmic scoring was used? privilege footer applied if counsel-directed? distribution label applied? AI disclosure attached if distributable and state law requires? fact/allegation/inference markers stripped in the distributable copy? reserves redacted if regulator-distributable? MSP flag noted if applicable? documentation gaps captured in the punchlist rather than the body?
- **Distribution label** in the header — one of: `INTERNAL ONLY`, `INSURED-DISTRIBUTABLE`, `COUNSEL-DISTRIBUTABLE`, `REGULATOR-DISTRIBUTABLE` — applied per the input's distribution scope
- **Privilege footer** applied to counsel-directed drafts only; stripped from every distributable copy
- **`MUST-REFER-UP` flags** in Recommended Next Steps for any reserve change or settlement action exceeding authority limits from `config.yml.claims.authority_limits`
- **`[AI-BIAS CHECK]` footnote** in the internal draft if any algorithmic scoring tool was used in the investigation
- Professional tone appropriate for a claims file that may be reviewed by management, legal counsel, regulators, or opposing counsel
- Correct insurance and legal terminology (proximate cause, reservation of rights, comparative negligence, indemnity, ALAE, subrogation, made-whole, anti-concurrent causation, etc.) — use the jurisdiction's preferred term when they differ
- Chronological consistency throughout — timeline events flow logically and every event carries a source citation
- AI-assisted disclosure applied when the draft is distributable and state law requires it
- Ready to insert into the claim file with minimal editing
- Saved to `outputs/` if the user confirms, with `-internal.md` and `-distributable.md` variants when both are produced

## Versioning

v4.0 (2026-05-11): Added authority-check step against `config.yml.claims.authority_limits` (per-adjuster-role × LoB × state × reserve-change-band × settlement-band) with `MUST-REFER-UP` flags in Recommended Next Steps and reserve redaction in distributable copies; AI-bias and disparate-impact check at Step 3 for any algorithmic scoring input per CO SB 21-169 / NY DFS Reg 187 / IN HB 1271 / AL SB 63 / NAIC AI Model Bulletin / EU AI Act Annex III with `[AI-BIAS CHECK]` footnote in the internal draft; Alabama SB 63 adverse-action AI disclosure added to the jurisdictional overlay alongside TX TRAIGA, CA AB 489, and IN HB 1271; EU AI Act Article 9–15 note for EU-policyholder or EU-licensed-entity claims; MSP / Medicare Set-Aside flag for Medicare-eligible BI and workers' comp claimants; reserve-category labeling keyed to `config.yml.claims.reserve_categories`; file-note ID format keyed to `config.yml.claims.file_note_id_format`; per-role signer block from `config.yml.agency.signer_block`; cross-references to Claims Reserve Recommender v2.0 (reserve changes), Coverage Explanation Letter v3.0 (coverage determinations), Subrogation Opportunity Finder v2.0 (recovery angles), FNOL Intake Assistant (initial setup coordination), and Fraud Red-Flag Summarizer v2.0 (SIU referral coordination). Every v3.0 capability is preserved — the 8-section narrative structure, all LoB variants, all jurisdictional overlays, the privilege/work-product posture, and the two paired deliverables (Handler-Ready Draft + Reviewer Checklist) are all retained and extended.

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
