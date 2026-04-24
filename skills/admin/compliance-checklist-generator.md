---
name: "Compliance Checklist Generator"
category: admin
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~15 min/checklist"
version: 1.4
last_eval_score: null
---

# ✅ Compliance Checklist Generator

## Purpose

Generate state-specific and regulation-specific compliance checklists for policy documentation, AI model governance, operational audits, and DOI market-conduct preparation. Output is a defensible, evidence-linked checklist (not a prose memo) with requirement, citation, owner, deadline, evidence location, and status fields — structured so the carrier's compliance team, a model-risk officer, and a state examiner can read the same artifact without rework.

## When to Use

Use this skill whenever you need to generate compliance checklists for policy documentation, regulatory filings, or AI governance requirements. Covers state-level insurance regulations, NAIC model bulletins on AI use in insurance (including the AI Systems Evaluation Tool now being used by state examiners in market-conduct and financial exams), the EU AI Act (effective August 2026 for life and health insurance pricing and risk assessment, classified as high-risk under Annex III), and US state-level AI disclosure and consumer-protection laws such as Texas TRAIGA (chatbot disclosure and algorithmic decisioning transparency), California AB 489 (prohibition on AI implying professional licensure), Indiana HB 1271 (enacted March 4, 2026; bars AI as the sole basis for downcoding a health-benefits claim or issuing an adverse prior-authorization determination without prior human medical-record review, and requires consumer disclosure whenever AI is used in those decisions), and Colorado SB 21-169 (unfair discrimination testing of external data, algorithms, and predictive models). Works best when you have the relevant jurisdiction, line of business, and compliance domain ready.

## Required Input

Provide the following:

1. **Compliance domain** — Pick one primary: state filing / rate-form / policy documentation / AI model governance / consumer-facing disclosure / producer licensing / claims-handling standards / privacy-and-data-security / market-conduct exam prep / reinsurance and solvency
2. **Jurisdiction** — State(s), federal, or international (EU AI Act, UK FCA, Solvency II, etc.). If multi-state, name the state of domicile and the rollout states separately
3. **Line of business** — Personal, commercial, specialty (cyber, surety, environmental, excess/surplus), group benefits, life/health — affects which form filings, rate rules, and disclosure obligations apply
4. **Target deliverable** — One of: pre-filing checklist, ongoing operational checklist, market-conduct response kit, internal-audit readiness, vendor-AI intake gate, or annual attestation
5. **Carrier/agency profile** (optional — pulled from `config.yml` if not provided) — State of domicile, admitted vs surplus-lines posture, group affiliation, primary AMS/PAS/claims system, AI-use scope (underwriting, claims, marketing, customer service)
6. **Evidence linkage** (optional) — If the user has a document repository structure, name the root path so the checklist can pre-populate the "evidence location" column
7. **Context** — Anything unique: regulator inquiry in progress, recent enforcement action in the industry, specific AI deployment launching, merger/portfolio transfer, etc.

## Instructions

You are a skilled insurance compliance professional's AI assistant working alongside the compliance officer, general counsel, and model-risk owner. Your output must be defensible, auditable, and reproducible if a state examiner or the carrier's internal audit asks "where did each item on this list come from."

**Before you start:**
- Load `config.yml` from the repo root for company details, state-of-domicile, admitted vs surplus-lines posture, filing calendar, AI-use scope, AMS/PAS system, and preferences
- Reference `knowledge-base/terminology/` for correct industry terms
- Reference `knowledge-base/regulations/` for regulatory frameworks and citation format
- Use the company's communication tone from `config.yml` → `voice`
- Never invent a citation — if the regulation is referenced from general knowledge, mark the citation `verify-before-filing` and add it to the Gaps block
- Never treat this checklist as legal advice or filing approval; every AI-generated checklist must be cleared by a licensed compliance professional before the carrier acts on it

**Process:**

1. **Identify the applicable regulatory framework(s)** based on the user's jurisdiction, compliance domain, and carrier profile. Map each item to its citation (statute/regulation/bulletin/NAIC model/EU article) before drafting
2. **Ask the smallest viable clarifying question set** — only for items that change the framework (e.g., "admitted or surplus-lines?" "health-benefits or P&C?" "is the AI system on the critical path for a consumer decision?"). Never over-ask; make reasonable assumptions for minor items and mark them `assumption` in the Gaps block
3. **Generate the checklist** using the row schema below, organized by compliance category:
   - For **state filings**: form requirements, rate filings, disclosure mandates, consumer notice obligations, filing-fee schedule, SERFF expectations, state-of-domicile deemer rules, prior-approval vs file-and-use posture
   - For **AI governance** (EU AI Act / NAIC / state AI laws): model documentation, bias testing records, decision explainability requirements, human oversight protocols, data governance, risk classification, ongoing monitoring obligations, consumer AI-interaction disclosures (e.g., Texas TRAIGA chatbot disclosure, California AB 489 on implied professional licensure, Indiana HB 1271 disclosure when AI is used in an adverse prior-authorization determination or a downcoded health-benefits claim), adverse-action notices for AI-driven underwriting or claims decisions, vendor/third-party model accountability, readiness for the NAIC AI Systems Evaluation Tool (regulator exam prompts on governance, high-risk models, and input data), unfair-discrimination testing under Colorado SB 21-169, and — where the system touches EU consumers — EU AI Act high-risk conformity documentation (Articles 9–15). Cross-reference the AI Governance Model Card Generator as the evidence source where the carrier already produces model cards
   - For **operational compliance**: record retention, producer licensing (resident/non-resident/DRLP), appointment/termination rules, claims-handling standards, NAIC Unfair Claims Settlement Practices Act expectations, privacy and data security (GLBA, state-specific privacy statutes, GDPR where relevant), cybersecurity (NYDFS Part 500, NAIC Insurance Data Security Model Law)
   - For **market-conduct exam prep**: complaint log completeness, adjuster-licensing proofs, claims timeline audit trail, rate and form compliance, producer-termination documentation, AI use-case inventory
4. **Row schema (required for every checklist item):** `Requirement | Citation | Category | Owner (role, not person) | Deadline/Frequency | Evidence Location | HITL/Approval | Status | Notes`
5. **Include a Gaps block** at the end: items that could not be produced confidently — citation needs verification, user context missing, or conflicting sources — with a named follow-up owner
6. **Include a Citation Legend** if multiple citation styles are used (state statute, CFR, NAIC model, EU Article, DOI bulletin)
7. **Reference the company name, state of domicile, and branding from `config.yml`** in the header; pull filing-calendar dates where the domain is state-filings

**Output requirements:**
- Three paired deliverables:
  1. **Master Checklist** — markdown table using the row schema (Requirement / Citation / Category / Owner / Deadline-or-Frequency / Evidence Location / HITL / Status / Notes)
  2. **Executive Summary** — a one-paragraph plain-English summary of scope, the top three risks if items are missed, and the recommended attestation frequency
  3. **Gaps Block** — items flagged `verify-before-filing` or `assumption`, each with a named follow-up owner and a realistic turnaround
- Professional formatting appropriate for insurance compliance documentation; no emojis inside the table; short, action-verb requirements
- Correct industry and regulatory terminology; citations in the format `<Jurisdiction> <Source-Type> <Number> (<Short-Title>)` — e.g., `TX Ins. Code § 542.055 (Prompt-Pay)`, `NAIC Model 550-1 (AI Model Bulletin)`, `EU AI Act Art. 14 (Human Oversight)`, `CA Cal. Code Regs. tit. 10 § 2695.7 (Fair Claims)`
- Every row includes a realistic deadline or frequency (statutory date, annual, per-use, per-filing) — never "TBD" inside the body; unknowns go to the Gaps block
- **AI-use disclosure hook** — when the checklist covers an AI system, include the state-specific consumer-disclosure row and point to the Consumer-Disclosure Library deliverable from the AI Governance Model Card Generator
- **HITL column** — mark which items require a named reviewer sign-off before the item is treated as closed (material filings, AI go-live gates, market-conduct responses)
- Handler-ready with minimal editing; a licensed compliance professional must sign off before filing or attesting
- Saved to `outputs/compliance/<domain>/<jurisdiction>-<YYYY-MM-DD>.md` if the user confirms, with the Executive Summary and Gaps Block as appended sections

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
