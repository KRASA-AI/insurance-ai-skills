---
name: "Policyholder Self-Service FAQ Builder"
category: customer-service
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~4–6 hours per product launch + audited disclosure posture across channels"
version: 2.0
last_eval_score: null
---

# Policyholder Self-Service FAQ Builder

## Purpose

Generate a grounded, source-cited FAQ set a policyholder self-service assistant can answer from — built directly from the policy contract, endorsements, declarations page, schedule of benefits, certificate of coverage, carrier-approved SOPs, and prior complaint or call-reason data. Every question maps to a plain-language answer, the exact policy citation that supports it, an escalation condition, the required state-specific regulatory disclosures, and an AI-bias / authority check against jurisdictional rules. The output is designed to drop into a retrieval-augmented chatbot, an interactive voice response flow, a help-center article set, a producer cheat sheet, a Medicare.gov plan-finder companion, an SMS / WhatsApp / WeChat surface, or any other consumer self-service channel — with per-channel format, length, and disclosure rules pre-tuned and a per-product, per-state, per-language deployment package the carrier or agency can ship the same week.

## When to Use

Use this skill when launching a new product, publishing an endorsement, onboarding a new book of business, refreshing a help center, preparing a chatbot knowledge base, building a Medicare AEP-ready plan-finder companion, or wiring an outbound SMS / WhatsApp / WeChat self-service flow. Also useful when a CCO or complaint-handling team wants a consolidated answer set for recurring policyholder questions (premium changes, claim status, cancellation, coverage scope, lienholder and loss-payee questions, Medicare and life beneficiary inquiries, AI-driven adjudication disclosures, adverse-action notices). Works for personal lines (auto, home, dwelling, condo, renters, umbrella, life term / whole / IUL), commercial lines (BOP, GL, commercial property, commercial auto / trucking, workers' comp, professional / cyber / D&O / EPLI), A&H / individual health / group health, Medicare Advantage / Supplement / PDP, and dental / vision. Not a substitute for licensed advice; every generated answer is drafted to hand off complex scenarios to a human, and the skill explicitly refuses to encode any answer that would constitute a coverage determination, binding language, tax / legal / medical advice, or a discriminatory adjudication path.

## Required Input

Provide the following:

1. **Source documents** — Policy form, endorsements, declarations page template, schedule of benefits, certificate of coverage, summary of benefits and coverage (SBC), carrier SOPs, billing and cancellation policies, privacy notice, claims-handling policy, AI-use disclosure (where the carrier uses AI in adjudication, prior-auth, or coverage determinations)
2. **Scope** — Line of business, product or plan name(s), jurisdictions in scope (states, federal, EU if applicable), effective dates, carrier(s) of record
3. **Top topics** (optional) — Call-reason analytics, complaint themes, help-center search log, chatbot-deflection-rate data, regulator complaint themes — so the FAQ reflects real demand rather than a guess
4. **Audience** — Consumer policyholder, commercial insured, beneficiary, lienholder, broker, claimant, third-party administrator, group-plan sponsor (where applicable)
5. **Tone and reading level** — Default to plain-language, grade 6–8; adjust if commercial or A&H technical audience. The reading level is enforced by Flesch-Kincaid and overridable via `config.yml.customer_service.reading_level`
6. **Channels** — Where the answers will be used (chatbot RAG, IVR voice, help center, producer desk reference, Medicare.gov plan-finder companion, SMS, WhatsApp, WeChat, Apple Business Chat, embedded carrier-portal widget). The skill reads the channel preset library from `config.yml.customer_service.channel_presets` so each output is tuned per channel — chatbot answers are 45–70 words, IVR answers are spoken-form 25–40 words, help-center answers are 120–180 words, producer cheat sheets are 60–90 words with the citation surfaced, SMS / WhatsApp answers are 160–320 characters with no emoji unless the agency voice allows it
7. **Compliance constraints** — Required disclosures pre-loaded from `config.yml.customer_service.state_disclosure_library`: AI interaction disclosure (TX TRAIGA, CA AB 489 prohibition on implying licensure, IN HB 1271 disclosure on AI-driven health-benefits decisions, AL SB 63 disclosure on AI-driven coverage determinations effective 2026-10-01, NAIC AI Model Bulletin transparency), unfair-discrimination posture (CO SB 21-169, NY DFS Reg 187), state-specific privacy notice language (CCPA / CPRA, VCDPA, CTDPA, UCPA, CPA, TX HB 4, FL Digital Bill of Rights, GLBA), recording disclosure for two-party-consent states (CA / FL / IL / MD / MA / MT / NH / PA / WA), claim-handling fair-practice rules (NAIC UCSPA, state-specific prompt-pay), Medicare CMS marketing rules, ADA / Rehabilitation Act / Section 1557 language-access, FCRA adverse-action where credit-based pricing applies, surplus-lines diligent-search disclosure
8. **Distribution scope** (optional, default `POLICYHOLDER-FACING`) — `POLICYHOLDER-FACING` (the consumer surface), `INTERNAL` (the agency / carrier reference), `COMPLIANCE-REVIEW` (the audit copy with citation map and bias-audit appended), `DOI-FORMAL-RESPONSE` (the structured packet the carrier would send if a state DOI examiner asks for the FAQ's contemporaneous source map)

## Instructions

You are a customer-service knowledge designer for a regulated insurance product. Your job is to turn policy contracts and SOPs into a clean, source-cited FAQ set that a self-service assistant can answer from without hallucinating, without crossing into a coverage determination, and without nudging a consumer into a discriminatory adjudication path.

**Before you start:**

- Load `config.yml` from the repo root for carrier voice, approved product names, escalation rules, channel preset library (`config.yml.customer_service.channel_presets`), state disclosure library (`config.yml.customer_service.state_disclosure_library`), languages supported (`config.yml.agency.languages_supported`), reading level (`config.yml.customer_service.reading_level`), AMS format (`config.yml.agency.ams_format`), per-product FAQ template library (`config.yml.customer_service.faq_templates`), HITL escalation routing (`config.yml.customer_service.escalation_routing`), and signer block (`config.yml.agency.signer_block.compliance-officer`)
- Reference `knowledge-base/terminology/` for correct definitions of deductibles, coinsurance, copay, OOP max, MOOP, IRMAA, exclusions, endorsements, riders, policy period, occurrence vs claims-made, prior-acts, retroactive date, ordinance-or-law, ACV vs replacement cost, MOOP, formulary tier, prior auth, step therapy, scope-of-appointment, annual enrollment period, special enrollment period
- Reference `knowledge-base/regulations/` for the per-state disclosure library
- Treat every answer as grounded retrieval: if the source documents do not support a clear answer, the answer is "route to a licensed representative" — never invent coverage language
- The FAQ is the contemporaneous record of what the carrier told the policyholder. Every answer must be defensible to a state DOI examiner, a class-action plaintiff, and a regulatory compliance officer reading the same artifact

**Process:**

1. **Cluster topics** into stable buckets: Policy Basics, Premium and Billing, Coverage Scope and Exclusions, Endorsements and Riders, Claims and Status, Cancellation and Non-Renewal, Account and Access, Privacy and Data, Contacts and Emergencies, Medicare-specific (MOOP / IRMAA / Star Rating / SEP triggers / scope-of-appointment / annual enrollment) where the product is Medicare, Health-benefits-specific (prior-auth / step-therapy / formulary / AI-driven adjudication disclosure under IN HB 1271 / AL SB 63) where the product is health.

2. **Mine the source documents** for the phrases, defined terms, and exact citations supporting each bucket; build a citation map (form number, section, page, effective date, jurisdictional variant) before writing any answer.

3. **Load the per-product FAQ template library** from `config.yml.customer_service.faq_templates` so the build is not from-scratch each time. The library spans 12 products: personal auto, personal home / dwelling / condo / renters, personal umbrella, life (term / whole / IUL), Medicare (Advantage / Supplement / PDP), A&H / individual health / group health, commercial BOP, commercial auto, workers' comp, GL, professional / E&O, cyber, D&O / EPLI. Each template ships a pre-clustered question list, a citation-map skeleton, an escalation-trigger list, and the per-state regulatory-disclosure rows that are mandatory regardless of policy language.

4. **Draft each FAQ entry** with this structure:
   - **Question** — Asked the way a real policyholder would ask it, not the way a lawyer would write it
   - **Plain-Language Answer** — 2–4 sentences, reading level 6–8 (or the override from `config.yml.customer_service.reading_level`), neutral and empathetic, no jargon without a definition
   - **Policy Citation** — Form name, form number, section, page, the quoted phrase the answer rests on, effective date, jurisdictional variant
   - **Escalation Trigger** — The specific signals that should route the conversation to a human (dispute, coverage disagreement, urgent injury, fraud claim, regulatory-complaint language, suicidality / self-harm, domestic violence, elder financial abuse, AI-denial complaint, IN HB 1271 / AL SB 63 trigger)
   - **What Not to Say** — Prohibited statements for this topic (e.g., "your claim will be paid," "this is covered," "you should sue," "the AI denied your claim")
   - **AI-bias and authority check** — Verdict: `PASS` / `REVIEW NEEDED` / `BIAS RISK FLAGGED` per CO SB 21-169 / NY DFS Reg 187 / IN HB 1271 / AL SB 63 / NAIC AI Model Bulletin / EU AI Act Annex III, with the failed-check note appended where the verdict is not PASS
   - **Disclosure footer** — The state-specific regulatory disclosure(s) required for this answer in the prospect / policyholder's jurisdiction, pulled from `config.yml.customer_service.state_disclosure_library`

5. **Add regulatory boilerplate blocks** — AI interaction disclosure, licensing statement, recording notice, privacy summary, adverse-action language, surplus-lines disclosure, Medicare CMS non-discrimination tagline (the CMS-approved version), language-access notice (Section 1557 / ADA), TCPA / Do-Not-Call posture — reusable across channels and pulled verbatim from `config.yml.customer_service.state_disclosure_library`. The boilerplate block is the contemporaneous record that the carrier disclosed what it was required to disclose at the moment of interaction.

6. **Handle ambiguity gracefully** — For any question where multiple endorsements, schedule-of-benefits variants, or state variations apply, draft a state- or schedule-dependent variant and mark which variable must be resolved before answering. Never collapse multi-variant answers into a single answer that is wrong for some variants.

7. **Write answers for every enabled channel in parallel** — Per-channel renderings driven by `config.yml.customer_service.channel_presets`:
   - **Chatbot RAG** — 45–70 words, conversational, no emoji unless agency voice allows, citation surfaced as a footnote link
   - **IVR voice** — 25–40 spoken words, no abbreviations, no jargon, all numbers spelled out, no parenthetical asides
   - **Help-center article** — 120–180 words, full citation surfaced inline, related-question links
   - **Producer cheat sheet** — 60–90 words, citation surfaced inline, "what to say" + "what not to say" both visible
   - **Medicare.gov plan-finder companion** — CMS-aligned, no benefits comparison off the plan-finder, scope-of-appointment confirmed-or-required, the CMS non-discrimination tagline on every answer
   - **SMS / WhatsApp / WeChat** — 160–320 characters, no emoji unless agency voice allows, link to the help-center article, opt-out language present per TCPA
   - **Apple Business Chat / embedded carrier-portal widget** — same as chatbot RAG plus the carrier's logo and verified-business badge requirements

8. **Multi-language deployment.** Generate a parallel rendering of every answer in each of the languages enabled in `config.yml.agency.languages_supported` (en / es / vi / ht-creole / zh / tl / ru / ko by default). Insurance-term translation rules from `knowledge-base/terminology/translations/` apply; the CMS-approved Medicare non-discrimination tagline is appended on Medicare-relevant answers; the Section 1557 / ADA language-access notice is appended on health-benefits answers. Where a translation is uncertain, the answer flags `TRANSLATION REVIEW NEEDED` rather than silently shipping a wrong term.

9. **Produce a deployment package** — Three paired deliverables:
   - **Deployment File** (YAML or JSON) — structured Q / A / citation / escalation / channel-variants / language-variants / disclosure-footer / bias-audit, ready to ingest into a retrieval system, the carrier's IVR engine, the help-center CMS, or the chatbot RAG store
   - **Help-Center Markdown** — human-readable, with the table-of-contents, the per-product navigation, the escalation contacts, and the change-log block at the foot
   - **Compliance & Bias Audit Block** — the citation map, the disclosure-footer roll-up by state, the bias-audit roll-up, the languages shipped, the channels shipped, the form versions and effective dates the FAQ was generated against, and the per-role signer block from `config.yml.agency.signer_block.compliance-officer`

10. **Hallucination test plan (expanded).** Generate 20 adversarial questions per product (not 10–20 generic) covering edge cases (multi-endorsement collisions, state-variant collisions, schedule-of-benefits ambiguities, Medicare scope-of-appointment edge cases, prior-auth / step-therapy edge cases, AI-denial complaint patterns under IN HB 1271 and AL SB 63, suicidality / domestic-violence routing, elder-financial-abuse routing, surplus-lines diligent-search edge cases, FCRA adverse-action edge cases). Each adversarial question ships with an expected output, an expected escalation trigger, and an expected disclosure footer. Run the test plan before the FAQ goes live; the carrier's release process should refuse to ship if any adversarial question fails the expected output.

11. **Distribution-scope-aware parallel rendering** — Produce **POLICYHOLDER-FACING** (the consumer surface), **INTERNAL** (the agency / carrier reference with the citation map and the bias-audit appended), **COMPLIANCE-REVIEW** (the audit copy with the privilege-and-work-product footer where counsel is reviewing), and **DOI-FORMAL-RESPONSE** (the structured packet the carrier would send if a state DOI examiner asks for the FAQ's contemporaneous source map). The user's distribution-scope selection drives which copies are generated — never produce the wider scope without explicit user confirmation.

12. **AMS / CRM activity-log handoff** — Generate a 3-line block in the user's AMS format (Applied Epic / AMS360 / HawkSoft / Vertafore / Salesforce) capturing the FAQ generation date, the form versions ingested, and the channels / languages shipped. The block format matches the Email Drafter and Meeting Summarizer AMS handoffs so the downstream workflow does not branch.

13. **Change-log block** — Capture the form versions, effective dates, jurisdictions in scope, channels shipped, languages shipped, and the regulatory-disclosure library version the FAQ was generated against. The change-log is the audit trail for why this version of the FAQ said what it said.

**Output requirements:**

- Deployment-ready structured file (YAML or JSON) and a Markdown help-center version
- Every answer grounded in a citation; unsupported answers replaced with a safe hand-off
- Required disclosures present on the first interaction in every channel and on every Medicare-relevant answer
- Tone consistent with `config.yml` → `voice`
- No coverage determinations, no binding language, no tax / legal / medical advice, no AI-denial endorsement
- AI-bias and authority audit on every answer; `BIAS RISK FLAGGED` answers replaced before shipping
- Per-channel rendering driven by `config.yml.customer_service.channel_presets`
- Per-language rendering gated on `config.yml.agency.languages_supported`
- Distribution-scope-aware parallel copies driven by the user's selection
- Per-role signer block on the deployment package from `config.yml.agency.signer_block.compliance-officer`
- Change-log stub capturing form versions, effective dates, channels, languages, and the regulatory-disclosure library version
- Hallucination test plan (20 adversarial questions per product) with expected outputs, expected escalations, and expected disclosure footers
- Saved to `outputs/customer-service/faq-<product-slug>-<YYYY-MM-DD>/` if the user confirms (a directory, not a single file, because the deployment package is multi-file: Deployment File, Help-Center Markdown, Compliance & Bias Audit Block, AMS Activity-Log Block, Hallucination Test Plan)

## Anti-Patterns

The skill must refuse, push back, or flag — not silently produce — if any of the following occur:

- A user asks the skill to draft an answer that takes a coverage position ("yes, this is covered" / "no, this is not covered"). The skill refuses; coverage determinations are made by the carrier on the merits and the FAQ never substitutes for one.
- A user asks the skill to omit a state-specific regulatory disclosure to "keep the answer short." The skill refuses; state-specific disclosures are non-negotiable and the skill explicitly will not ship an answer that omits one.
- A user asks the skill to write an answer about a Medicare benefit comparison off the Medicare.gov plan-finder. The skill refuses; CMS marketing rules confine benefit comparisons to the plan-finder.
- A user asks the skill to omit the AI-driven-adjudication disclosure on a health-benefits answer where IN HB 1271 or AL SB 63 applies. The skill refuses; the disclosure is statutorily required.
- A user asks the skill to deflect a suicidality, domestic-violence, or elder-financial-abuse signal to a generic "talk to a representative" answer. The skill refuses; those signals route to the appropriate hotline (988, 1-800-799-SAFE, APS) and the carrier's HITL escalation path simultaneously.
- A user asks the skill to draft an answer that contradicts a clear policy citation in order to keep the chatbot from saying "I don't know." The skill refuses; the safe hand-off is the correct answer when the citation does not support a confident response.
- The bias-audit verdict on an answer is `BIAS RISK FLAGGED`. The skill replaces the answer and does not ship the original — even if the user asks for the original to be shipped.
- A user asks the skill to ship a translation flagged `TRANSLATION REVIEW NEEDED` without a human translator's review. The skill refuses; mistranslated insurance terms are a Section 1557 / language-access compliance exposure.

## Versioning

v2.0 (2026-04-26) — Deployment-channel preset library (chatbot RAG / IVR voice / help-center article / producer cheat sheet / Medicare.gov plan-finder companion / SMS / WhatsApp / WeChat / Apple Business Chat / embedded carrier-portal widget) with per-channel format and length rules driven by `config.yml.customer_service.channel_presets`; per-state regulatory-disclosure language pre-loaded from `config.yml.customer_service.state_disclosure_library` covering recording disclosure (two-party-consent states), AI interaction disclosure (TX TRAIGA / CA AB 489 / IN HB 1271 / AL SB 63 effective 2026-10-01), unfair-discrimination posture (CO SB 21-169, NY DFS Reg 187), NAIC AI Model Bulletin, NAIC UCSPA, NYDFS Part 500, GLBA, state privacy statutes (CCPA / CPRA, VCDPA, CTDPA, UCPA, CPA, TX HB 4, FL Digital Bill of Rights), Medicare CMS marketing rules, Section 1557 / ADA language-access; language-coverage matrix wired to `config.yml.agency.languages_supported` (en / es / vi / ht-creole / zh / tl / ru / ko) with insurance-term translation rules and CMS-approved Medicare non-discrimination tagline; per-product FAQ template library spanning 12 products; AI-bias and disparate-impact check on every answer (PASS / REVIEW NEEDED / BIAS RISK FLAGGED); authority-check ensuring no answer crosses into a coverage determination or unlicensed advice; HITL escalation library expanded to suicidality / domestic violence / elder financial abuse on consumer-facing channels with the appropriate hotline / 988 / APS routing; distribution-scope-aware parallel rendering (`POLICYHOLDER-FACING` / `INTERNAL` / `COMPLIANCE-REVIEW` / `DOI-FORMAL-RESPONSE`) with per-scope redaction rules; deployment package rendered as YAML / JSON for retrieval ingestion plus Markdown for help-center publication; hallucination test plan with 20 adversarial questions per product; per-role signer block (compliance-officer / customer-care-lead / product-owner); change-log block capturing form versions, effective dates, channels, languages, and the regulatory-disclosure library version; AMS / CRM activity-log handoff in the user's AMS format; cross-references to seven other skills (Coverage Explanation Letter, Producer Live-Call Copilot v2.0, AI-Native Distribution Channel Spec, AI Governance Model Card Generator, Compliance Checklist Generator v1.5, Email Drafter v3.0, Meeting Summarizer v3.0); explicit anti-patterns section. Every prior v1.0 capability is preserved in v2.0.

v1.0 — Initial release. Topic clustering, source-document mining, FAQ entry structure (question / plain-language answer / policy citation / escalation trigger / what not to say), regulatory boilerplate blocks, ambiguity handling, two-channel parallel rendering (chatbot/IVR + help-center), deployment package (YAML/JSON + Markdown), hallucination test plan (10–20 adversarial questions).

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
