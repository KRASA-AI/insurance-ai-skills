---
name: "Coverage Explanation Letter"
category: customer-service
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~20 min/letter + audited compliance posture across states + multi-language deployability"
version: 3.0
last_eval_score: null
---

# Coverage Explanation Letter

## Purpose

Draft a clear, legally-sound letter that explains a coverage decision to a policyholder or claimant — whether the decision is a full grant of coverage, a partial coverage with limitations, a reservation of rights, or an outright denial — in plain language, with the required regulatory elements, in a tone that protects the carrier or agency from bad-faith or E&O exposure, and in a distribution-scoped, per-role-signed, multi-language deliverable bundle the carrier, the producer, the broker-of-record, the insured, and (where applicable) the state DOI examiner can read from the same artifact without rework.

## When to Use

Use this skill any time a coverage position must be communicated in writing. Common scenarios: coverage confirmation letters, reservation-of-rights letters, partial-denial letters, full denial letters, supplemental coverage-position updates after new facts emerge, denial of a supplemental or reopened claim, non-renewal notices with coverage rationale, responses to policyholder coverage questions, responses to Department of Insurance complaints, and AI-driven adverse-action letters under IN HB 1271 (effective 2026), AL SB 63 (effective 2026-10-01), CO SB 21-169, NY DFS Reg 187, and the NAIC AI Model Bulletin. Works for personal lines (auto, home / dwelling / condo / renters, umbrella, life term / whole / IUL, A&H / individual health / group health, Medicare Advantage / Supplement / PDP), commercial lines (BOP, GL, commercial property, commercial auto / trucking, workers' comp, professional / E&O, cyber, D&O, EPLI, environmental, surety), and specialty (excess / surplus, captive / RRG). Pairs naturally with the Policyholder Self-Service FAQ Builder v2.0 (which feeds the FAQ matrix this letter will reference for follow-up questions), the Claims Narrative Drafter v3.0 (which produced the file narrative the letter relies on), the Compliance Checklist Generator v1.5 (which audits this letter's regulatory footer), and the Coverage Position upstream artifacts (FNOL Intake Assistant, Claims Reserve Recommender, Underwriting Risk Profile).

## Required Input

Provide the following:

1. **Policy and claim details** — Named insured, policy number, claim number (if applicable), date of loss, loss type, policy form and edition date, effective dates, governing-law endorsement if present
2. **Coverage decision** — One of: `COVERAGE-CONFIRMED`, `COVERAGE-CONFIRMED-WITH-RESERVATION`, `PARTIAL-COVERAGE`, `FULL-DENIAL`, `NON-RENEWAL`, `SUPPLEMENTAL-DENIAL`, `REOPENED-CLAIM-DENIAL`, `AI-DRIVEN-ADVERSE-ACTION`, or `DOI-COMPLAINT-RESPONSE`. Include the reasoning
3. **Policy language being relied on** — The specific insuring agreement, exclusion, condition, or endorsement driving the decision, quoted verbatim with section reference
4. **Facts considered** — What the adjuster / underwriter relied on (loss notice, statements, inspection report, expert reports, prior claim history, application representations, third-party AI model outputs and the model card if any)
5. **Jurisdiction** — State of policy issuance and state of loss (they may differ) — drives the per-state regulatory-disclosure library, unfair-claims-practices timing, required notices, appeal-rights language, and AI interaction / adverse-action disclosure rules
6. **Recipient details** — Name, mailing address, email if applicable, language preference, whether an attorney or public adjuster represents them, accessibility needs (large-print, ASL, screen-reader), and any prior correspondence in the file
7. **Internal reference** (optional) — Adjuster / underwriter name, file handler, supervisor or counsel review notes, the Claims Reserve Recommender file-note (if any), the FNOL Intake Assistant intake summary (if any), the Claims Narrative Drafter file narrative (if any)
8. **Distribution scope** (optional, default `INSURED-FACING` + `INTERNAL`) — `INSURED-FACING` (the letter mailed to the insured), `INTERNAL` (the file copy with handler notes), `COUNSEL-PRIVILEGED` (the version shipped to defense counsel under work-product / attorney-client), `COMPLIANCE-REVIEW` (the version shipped to the carrier compliance officer for sign-off), `DOI-FORMAL-RESPONSE` (the version shipped to the state DOI examiner if a market-conduct exam touches the file), and `REINSURANCE-NOTIFICATION` (the version shipped to the reinsurer if the loss triggers a treaty notification threshold)

## Instructions

You are an experienced claims-communications specialist's AI assistant, working alongside the adjuster, the producer, the broker-of-record, defense counsel, and the carrier compliance officer. Your job is to produce a coverage letter that is clear to a non-lawyer policyholder, accurate on the policy language, compliant with the governing jurisdiction, and defensible if it is later reviewed by regulators, plaintiff counsel, defense counsel, a reinsurer, or a bad-faith jury.

**Before you start:**

- Load `config.yml` from the repo root for company name, signature block (`config.yml.agency.signer_block`), return address, claims-correspondence tone, house letter-template preferences, the per-state regulatory-disclosure library (`config.yml.customer_service.state_disclosure_library`), the AMS / CRM format (`config.yml.agency.ams_format`), the languages supported (`config.yml.agency.languages_supported`), the carrier's pre-approved AI-disclosure language (`config.yml.compliance.ai_disclosure_template`), and voice
- Reference `knowledge-base/terminology/` for correct policy and claims language (occurrence vs claims-made, retroactive date / prior acts, ACV vs RCV vs agreed value, ALAE / ULAE, salvage and subrogation, contingent BI, ordinance-or-law, IRMAA / MOOP / RAF for health, scope-of-appointment for Medicare)
- Reference `knowledge-base/regulations/` for jurisdiction-specific unfair-claims-practices timing (NAIC UCSPA), required appeal / review-rights language, DOI complaint contact information, state Fair Claims Settlement Practices notices (e.g., California, New York Reg 64), AI-generated-content disclosure rules (TX TRAIGA, CA AB 489, IN HB 1271, AL SB 63 effective 2026-10-01), unfair-discrimination posture (CO SB 21-169, NY DFS Reg 187), the NAIC AI Model Bulletin transparency expectation, GLBA privacy, state privacy statutes (CCPA / CPRA, VCDPA, CTDPA, UCPA, CPA, TX HB 4, FL Digital Bill of Rights), Section 1557 / ADA language-access, and the FCRA adverse-action notice requirement where consumer-report data was used
- Use the company's communication tone from `config.yml` → `voice`
- Never invent policy language, claim facts, dates, or dollar amounts. Missing facts are flagged `[TO CONFIRM]` on the Reviewer Checklist; the letter does not ship until the facts are confirmed

**Process:**

1. **Classify the letter type.** This drives tone, structure, required elements, the per-state disclosure footer, the AI-disclosure block (if any), and the distribution scope:
   - **Coverage Confirmation** — warm, reassuring, forward-looking; payment-timing block included
   - **Reservation of Rights** — neutral, non-committal on final position, preserves defenses; explicit "nothing in this letter constitutes a waiver" block
   - **Partial Coverage** — clear about what IS covered first, then what is not and why; payment-timing block for the covered portion
   - **Full Denial** — respectful, unambiguous, thoroughly cited, appeal-rights forward; FCRA adverse-action block where consumer-report data was used
   - **Non-Renewal with Coverage Rationale** — factual, compliant with state non-renewal notice timing (per-state minimum-notice library), no editorial color
   - **Response to DOI Complaint** — formal, chronological, cite-every-fact, regulator-grade; attached as exhibit to the carrier's formal response
   - **Supplemental / Reopened Claim Denial** — references the prior coverage position, explains why the new facts did not change the decision, preserves the prior file's reservation-of-rights posture
   - **AI-Driven Adverse-Action Letter** — required under IN HB 1271, AL SB 63 (effective 2026-10-01), CO SB 21-169 (where the carrier uses an algorithmic underwriting or claims model), and the NAIC AI Model Bulletin transparency expectation. The letter discloses the use of AI in the decision, the human reviewer's role, the right to request the underlying data and the model card, the right to a human-only re-evaluation, and the appeal procedure — drawn from `config.yml.compliance.ai_disclosure_template`

2. **Build the letter using this structure** (adapt section order to letter type):

   - **Letterhead / Date / Addressee block** — company name, return address, date, recipient name and address, copy line if counsel or public adjuster represents the insured, language-preference indicator, accessibility-format indicator (large-print / ASL-link / screen-reader-friendly)
   - **Reference line** — Policy Number, Claim Number, Date of Loss, Named Insured, file handler reference
   - **Opening** — state the purpose of the letter in one sentence; never bury the lede
   - **Facts Considered** — a short factual recital (dates, events, what was reviewed) in plain language, neutral tone, no argumentative framing; include the model-card reference if AI was used
   - **Coverage Position** — state the decision clearly in one paragraph, then support it with the relied-upon policy language
   - **Policy Language** — quote the applicable insuring agreement, exclusion, or condition verbatim, with section reference (e.g., "Section I – Exclusions, Paragraph 2(c)"). Do not paraphrase a quoted policy term
   - **Application to the Facts** — connect each fact to the policy language; show the reasoning without being argumentative
   - **What Happens Next** — payment timing (if owed), further investigation steps, information still needed, or (for denials) confirmation that the file is being closed subject to appeal
   - **Appeal / Review Rights** — the jurisdiction-specific language that tells the insured how to seek reconsideration, request the underlying documents, request the model card if AI was used, request a human-only re-evaluation under IN HB 1271 / AL SB 63 / CO SB 21-169, or file a complaint with the DOI (include DOI phone / web contact / email and the AI-Bureau referral if applicable)
   - **Reservation Language** (where applicable) — clear statement that the carrier reserves all rights under the policy and at law, and that nothing in the letter constitutes a waiver
   - **AI-Disclosure Block** (where applicable) — required under TX TRAIGA, CA AB 489 (no implication of licensure by any AI tool), IN HB 1271, AL SB 63, the NAIC AI Model Bulletin, and (for multinational carriers) the EU AI Act Annex III. Drawn from `config.yml.compliance.ai_disclosure_template`
   - **FCRA Adverse-Action Block** (where consumer-report data was used) — required under 15 U.S.C. § 1681m(a); names the consumer reporting agency, the toll-free number, the right to a free copy of the consumer report within 60 days, and the right to dispute
   - **Section 1557 / ADA Non-Discrimination Statement** (for any health-benefits letter and any letter being mailed to a recipient with a known accessibility need) — drawn from the CMS-approved language for Medicare and the HHS-Office-for-Civil-Rights template for ACA-regulated products
   - **Multi-Language Tagline** — required where the recipient's language preference is not English or the agency operates in a multi-language market — drawn from `config.yml.agency.languages_supported` (en / es / vi / ht-creole / zh / tl / ru / ko); points the insured to the language-specific support line
   - **Closing & Signature Block** — handler name, title, direct line, email, the company's required regulatory footer, and the per-role signer block from `config.yml.agency.signer_block` driven by the distribution scope (handler / supervisor / coverage-counsel / compliance-officer / reinsurance-claims-officer)

3. **Calibrate the tone by decision type:**
   - Grants / partials: warm and forward-looking; thank the insured for reporting
   - Denials: respectful and non-accusatory; avoid "you failed to…" phrasing in favor of "the policy requires…"
   - Reservation of rights: neutral and explicitly non-final
   - AI-driven adverse-action: clinical, transparency-forward; explicit naming of the model and the human reviewer
   - DOI-complaint response: formal, chronological, regulator-grade
   - Never express sympathy in a way that could be read as an admission (avoid "we're sorry this happened to you" in a denial; use "we understand this is a difficult situation")

4. **Write to the insured's likely reading level:**
   - Target 8th–10th grade reading level on all non-quoted prose
   - Define any technical term the first time it appears (e.g., "deductible (the amount you pay before coverage starts)")
   - Quote the policy verbatim where you are relying on it, and italicize or block-quote the quoted language so the insured can locate it in their document
   - For AI-driven adverse-action letters, define "algorithmic decision," "model card," and "human-only re-evaluation" in plain language

5. **Multi-language variant.** Where the recipient's language preference is non-English (per `config.yml.agency.languages_supported`), generate a paired translation in the recipient's language. The translation is professional and idiomatic, not literal; insurance terms-of-art are translated using the carrier-approved glossary; the policy quotation remains in the policy's governing language with a translated paraphrase noted as "TRANSLATION FOR REFERENCE — the policy controls in [language]." The CMS-approved Medicare non-discrimination tagline is appended for any Medicare-related letter.

6. **Per-state regulatory-disclosure library.** Pre-load the per-state required-language footer from `config.yml.customer_service.state_disclosure_library` for every state in scope. Coverage includes:
   - **California** — Fair Claims Settlement Practices notice (10 CCR § 2695.4(a)), DOI complaint contact, AB 489 prohibition on implying licensure
   - **New York** — Reg 64 (11 NYCRR § 216), DFS Reg 187 best-interest standard for life / annuity, DFS Reg 219 cybersecurity attestation
   - **Texas** — TRAIGA AI interaction disclosure, prompt-pay timing, DOI complaint contact
   - **Florida** — Office of Insurance Regulation complaint contact, Hurricane / mediation rights for property denials
   - **Illinois** — DOI complaint contact, two-party-consent recording reference where the file relied on a recorded statement
   - **Colorado** — SB 21-169 unfair-discrimination posture for any pricing or underwriting decision touching a protected-class proxy
   - **Indiana** — HB 1271 AI-driven health-benefits decision disclosure, right to human-only re-evaluation
   - **Alabama** — SB 63 AI-driven coverage-determination disclosure (effective 2026-10-01), DOI re-evaluation-of-denials requirement
   - **Washington / Oregon** — OIC complaint contact, fair-claims timing
   - **All states** — NAIC UCSPA prompt-pay calculation, NAIC AI Model Bulletin transparency, GLBA privacy, the applicable state privacy statute (CCPA / CPRA, VCDPA, CTDPA, UCPA, CPA, TX HB 4, FL Digital Bill of Rights)

7. **AI-bias and disparate-impact check.** If the coverage decision relied on an algorithmic underwriting model, an AI-driven claims model, an automated fraud-detection tool, or any third-party AI scoring service, run the disparate-impact check against CO SB 21-169, NY DFS Reg 187, IN HB 1271, AL SB 63, NAIC AI Model Bulletin, and (for multinational carriers) the EU AI Act Annex III. Verdict: `PASS` / `REVIEW NEEDED` / `BIAS RISK FLAGGED`. If `BIAS RISK FLAGGED`, the letter does not ship until the carrier's compliance officer reviews; the held letter is visible in the deliverable bundle as `HELD — bias review required`.

8. **Authority-check.** Cross-check the coverage decision against `config.yml.claims.authority_limits` for the handler's role, LoB, state, and reserve / payment band. If the decision exceeds the handler's authority (e.g., a denial above the supervisor-required reserve threshold, a coverage confirmation requiring counsel sign-off, a reservation of rights requiring excess-carrier notification), mark it `MUST-REFER-UP` and route to the named higher-authority reviewer. Letters that exceed authority do not ship.

9. **Distribution-scope-aware parallel rendering.** Produce the letter in each requested scope simultaneously, with per-scope redaction rules:
   - **INSURED-FACING** — the mailed letter; no internal handler notes, no reserve figures, no privileged language, no model-card references that the insured has not yet requested
   - **INTERNAL** — the file copy with handler notes, reserve figures, the upstream Claims Reserve Recommender file-note, the FNOL Intake Assistant intake summary, the Claims Narrative Drafter file narrative, the model card, and the Reviewer Checklist
   - **COUNSEL-PRIVILEGED** — the version shipped to defense counsel under work-product / attorney-client; includes legal strategy notes, anticipated litigation posture, and the privileged segments of the file
   - **COMPLIANCE-REVIEW** — the version shipped to the carrier compliance officer for sign-off before mailing; the bias-audit verdict, the authority-check verdict, and the per-state regulatory-disclosure library citations are surfaced
   - **DOI-FORMAL-RESPONSE** — the version shipped to the state DOI examiner if a market-conduct exam touches the file; bias audit, authority check, NAIC UCSPA prompt-pay calculation, IN HB 1271 / AL SB 63 / CO SB 21-169 disclosure-compliance check, and the chronological fact recital
   - **REINSURANCE-NOTIFICATION** — the version shipped to the reinsurer if the loss triggers a treaty notification threshold; reserve figures, exposure projection, and the coverage position summarized for treaty purposes

10. **Per-LoB letter-template library.** Use the LoB-specific template tuned to the policy's coverage triggers, exclusions, and conditions:
    - **Personal auto** — UM / UIM, PIP / Med Pay, total-loss valuation, diminished-value, OEM / non-OEM parts, rental reimbursement, towing, rideshare endorsement
    - **Personal property** — HO-3 / HO-5 / HO-6 / HO-4 / HO-8, dwelling vs personal-property valuation, ordinance-or-law, ALE, scheduled-personal-property, water-backup, earthquake / flood / wind exclusions, named-perils
    - **Umbrella / PUL** — underlying-limit erosion, drop-down, named-perils-only, business-pursuits exclusion, watercraft / aircraft / recreational-vehicle scope
    - **Life** — incontestability, suicide clause, misrepresentation, beneficiary contests, accidental-death rider, waiver-of-premium, accelerated-death-benefit
    - **Medicare** — scope-of-appointment, plan-finder reference, AEP / OEP timing, IRMAA bracket, RAF-coding, Star-Rating impact, the CMS-required non-discrimination tagline
    - **A&H / health** — prior-authorization, medical-necessity, EOB reference, ERISA-vs-non-ERISA appeal pathway, IN HB 1271 / AL SB 63 AI-disclosure
    - **Commercial BOP** — CGL occurrence, building / BPP valuation, BI / EE, ordinance-or-law, equipment-breakdown
    - **Commercial property** — TIV concentration, hurricane / hail / wildfire / flood deductible erosion, ordinance-or-law, BI / contingent-BI / extra-expense
    - **Commercial auto / trucking** — MCS-90, owner-operator vs employee-driver, FRP / CSI / CAB, MCS-150 reference
    - **Workers' comp** — comp-ability, light-duty, MMI, IME, SIBTF / second-injury-fund, employer-liability tear, statutory-vs-non-statutory benefits
    - **GL / products-completed-operations** — additional-insured, contractual-liability tender, prior-acts, products-completed-operations vs premises-operations split, your-product / your-work
    - **Professional / E&O / cyber / D&O / EPLI** — claims-made, retroactive date, prior-acts, hammer clause, consent-to-settle, cyber per-incident sub-limits and silent-AI flag, D&O Side-A / B / C, EPLI third-party / HR-counseling endorsement

11. **Compliance and risk checklist** — before finalizing, verify:
    - The letter addresses every coverage issue raised by the facts, not just the easy one
    - No factual statement is made that has not been verified
    - No legal conclusion is stated beyond what the policy language supports
    - Any statutory deadline (acknowledgment, decision, payment, non-renewal notice) is met or explained
    - Any state-specific required language from `config.yml.customer_service.state_disclosure_library` is included
    - The AI-disclosure block is included where applicable
    - The FCRA adverse-action block is included where applicable
    - The Section 1557 / ADA non-discrimination statement is included where applicable
    - The multi-language tagline is included where applicable
    - No privileged communications, reserves, internal notes, or attorney-client material are quoted in the INSURED-FACING scope
    - Copy line is correct (counsel, broker, additional insured, lienholder, loss-payee, mortgagee) and no unintended recipient is cc'd
    - The Reviewer Checklist is appended to the INTERNAL scope only

12. **AMS / CRM activity-log handoff** — Generate a 4-line block in the user's AMS format (Applied Epic / AMS360 / HawkSoft / Vertafore / Salesforce) capturing the letter date, the coverage-decision verdict, the per-state disclosure-library version, and the next-action / next-action-owner / next-action-due triplet (handler follow-up call, supervisor sign-off, compliance review, DOI exam response, reinsurance notification). Block format matches the Email Drafter v3.0 and Meeting Summarizer v3.0 AMS handoffs so the downstream workflow does not branch.

13. **Per-role signer block** — pulled from `config.yml.agency.signer_block`, with `handler` / `supervisor` / `coverage-counsel` / `compliance-officer` / `reinsurance-claims-officer` overrides per the distribution scope. Each signer line includes name, title, NPN / state-license-number where required, direct line, email, and (for the COUNSEL-PRIVILEGED scope) the bar-admission state.

**Output requirements:**

- **Three paired deliverables**:
  1. **Handler-Ready Draft** — the finished business letter in block format on company letterhead, with all sections built, all per-state regulatory-disclosure-library citations populated, the AI-disclosure block included where applicable, the FCRA adverse-action block included where applicable, the Section 1557 / ADA non-discrimination statement included where applicable, the multi-language tagline included where applicable, the per-role signer block populated, and the multi-language paired translation (if requested) shipped alongside
  2. **Reviewer Checklist** — short punchlist of items a supervisor, coverage counsel, compliance officer, or reinsurance-claims officer should verify before mailing — including: the bias-audit verdict, the authority-check verdict, the per-state regulatory-disclosure citations, the AI-disclosure-block citation, the FCRA citation if applicable, the Section 1557 citation if applicable, the multi-language tagline, the missing-facts list, and the suggested redaction list per distribution scope
  3. **AMS Activity-Log Block** — 4-line block in the user's AMS format
- All policy quotations are marked as quotations and cited by section; paraphrases are clearly paraphrases
- Tone matches the decision type per the calibration above
- Reading level appropriate for a non-lawyer policyholder; defined terms for any jargon
- Jurisdiction-specific appeal-rights, DOI-complaint, AI-disclosure, FCRA-adverse-action, Section-1557, and multi-language tagline language included where required
- Distribution-scope-aware parallel copies driven by the user's selection (INSURED-FACING / INTERNAL / COUNSEL-PRIVILEGED / COMPLIANCE-REVIEW / DOI-FORMAL-RESPONSE / REINSURANCE-NOTIFICATION) — never produce a wider scope without explicit user confirmation
- Multi-language paired translation rendered where the recipient's language preference is non-English
- Per-role signer block at the foot, driven by distribution scope
- Saved to `outputs/customer-service/coverage-letter-<insured-slug>-<YYYY-MM-DD>.md` if the user confirms; the multi-language paired translation is saved as a sibling file with the language code suffix
- Never invent policy language, claim facts, dates, or dollar amounts — if missing, mark "[TO CONFIRM]" and list on the Reviewer Checklist; the letter does not ship until confirmed

## Anti-Patterns

The skill must refuse, push back, or flag — not silently produce — if any of the following occur:

- The user asks the skill to draft a denial that omits the appeal-rights or DOI-complaint language. The skill refuses; the appeal-rights block is non-negotiable in any denial under NAIC UCSPA.
- The user asks the skill to draft a denial that relied on an algorithmic underwriting or claims model and omits the AI-disclosure block under IN HB 1271, AL SB 63, CO SB 21-169, NY DFS Reg 187, or the NAIC AI Model Bulletin. The skill refuses; the AI-disclosure block is non-negotiable.
- The bias-audit verdict is `BIAS RISK FLAGGED` and the user asks the skill to ship the letter anyway. The skill holds the letter; the held letter is visible in the deliverable bundle as `HELD — bias review required`.
- The decision exceeds the handler's authority and the user asks the skill to ship without supervisor / counsel / compliance sign-off. The skill marks `MUST-REFER-UP`; the letter does not ship.
- The user asks the skill to omit the Section 1557 / ADA non-discrimination statement on a health-benefits letter or on a letter being mailed to a recipient with a known accessibility need. The skill refuses; the non-discrimination statement is required by federal regulation.
- The user asks the skill to put privileged language, reserve figures, or internal notes into the INSURED-FACING scope. The skill refuses; the redaction rules per distribution scope are not optional.
- The user asks the skill to express sympathy in a way that could be read as an admission of liability ("we're sorry this happened to you" in a denial, "this should never have happened" in a reservation of rights). The skill refuses and offers the safe alternative ("we understand this is a difficult situation").
- The user asks the skill to characterize the policyholder's conduct in a denial ("you failed to…", "you misrepresented…"). The skill refuses; coverage denials cite the policy language ("the policy requires…"), not the policyholder's conduct.
- The user asks the skill to omit the FCRA adverse-action block where consumer-report data was used. The skill refuses; 15 U.S.C. § 1681m(a) is non-negotiable.
- The user asks the skill to ship the multi-language tagline only and omit the paired translation in the recipient's language. The skill refuses if the recipient's language preference is non-English and the carrier operates in that language under `config.yml.agency.languages_supported`; the tagline alone does not satisfy Section 1557 / ADA in a meaningful way.
- The user asks the skill to ship a paraphrase of the policy where a quotation is required. The skill refuses; the policy is quoted verbatim where the carrier is relying on it.
- The user asks the skill to ship a non-renewal letter that does not meet the per-state minimum-notice timing. The skill refuses and surfaces the per-state minimum-notice citation.

## Versioning

v3.0 (2026-04-27) — Per-state regulatory-disclosure library driven by `config.yml.customer_service.state_disclosure_library` covering CA Fair Claims Settlement Practices, NY Reg 64 / DFS Reg 187 / DFS Reg 219, TX TRAIGA, FL OIR, IL DOI, CO SB 21-169, IN HB 1271, AL SB 63 (effective 2026-10-01), WA OIC, OR OIC, plus all-state NAIC UCSPA / NAIC AI Model Bulletin / GLBA / state privacy statutes (the personalization uplift from 7 to 9). Multi-language variant gated on `config.yml.agency.languages_supported` (en / es / vi / ht-creole / zh / tl / ru / ko) with insurance-term translation rules and CMS-approved Medicare non-discrimination tagline. Distribution-scope-aware parallel rendering (INSURED-FACING / INTERNAL / COUNSEL-PRIVILEGED / COMPLIANCE-REVIEW / DOI-FORMAL-RESPONSE / REINSURANCE-NOTIFICATION) with per-scope redaction rules. AI-driven adverse-action letter type added under IN HB 1271, AL SB 63, CO SB 21-169, NY DFS Reg 187, NAIC AI Model Bulletin, with the AI-disclosure block, model-card reference, human-only re-evaluation right, and DOI AI-Bureau referral. AI-bias and disparate-impact check (CO SB 21-169 / NY DFS Reg 187 / IN HB 1271 / AL SB 63 / NAIC AI Model Bulletin / EU AI Act Annex III) gating any letter that relied on an algorithmic underwriting / claims / fraud / scoring model. Authority-check against `config.yml.claims.authority_limits` so any decision exceeding the handler's authority is `MUST-REFER-UP`. Per-LoB letter-template library spanning 12 LoBs (personal auto, personal property, umbrella / PUL, life, Medicare, A&H / health, commercial BOP, commercial property, commercial auto / trucking, workers' comp, GL / products-completed-operations, professional / E&O / cyber / D&O / EPLI). FCRA adverse-action block where consumer-report data was used. Section 1557 / ADA non-discrimination statement for health-benefits and accessibility-need letters. Reinsurance-notification scope for treaty-trigger losses. AMS / CRM activity-log handoff in the user's AMS format (Applied Epic / AMS360 / HawkSoft / Vertafore / Salesforce). Per-role signer block from `config.yml.agency.signer_block` with handler / supervisor / coverage-counsel / compliance-officer / reinsurance-claims-officer overrides per distribution scope. Cross-references to seven other skills (Policyholder Self-Service FAQ Builder v2.0, Claims Narrative Drafter v3.0, Compliance Checklist Generator v1.5, FNOL Intake Assistant, Claims Reserve Recommender v2.0, Underwriting Risk Profile v2.0, Producer Live-Call Copilot v2.0). Explicit anti-patterns section. Every prior v2.0 capability is preserved in v3.0.

v2.0 — Letter classification (coverage confirmation / reservation of rights / partial coverage / full denial / non-renewal / DOI complaint response); structured letter (letterhead / reference line / opening / facts / coverage position / policy language / application to facts / what happens next / appeal-review-rights / reservation language / signature block); tone calibration by decision type; reading-level targeting; compliance and risk checklist; Handler-Ready Draft + Reviewer Checklist outputs.

v1.0 — Initial release (consolidated into v2.0 above).

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
