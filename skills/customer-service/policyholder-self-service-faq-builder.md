---
name: "Policyholder Self-Service FAQ Builder"
category: customer-service
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~4–6 hours per product launch or policy update"
version: 1.0
last_eval_score: null
---

# Policyholder Self-Service FAQ Builder

## Purpose

Generate a grounded, source-cited FAQ set a policyholder self-service assistant can answer from — built directly from the policy contract, endorsements, declarations page, carrier-approved SOPs, and prior complaint or call-reason data. Every question maps to a plain-language answer, the exact policy citation that supports it, an escalation condition, and the required regulatory disclosures. The output is designed to drop into a retrieval-augmented chatbot, an interactive voice response flow, a help-center article set, or a producer cheat sheet.

## When to Use

Use this skill when launching a new product, publishing an endorsement, onboarding a new book of business, refreshing a help center, or preparing a chatbot knowledge base. Also useful when a CCO or complaint handling team wants a consolidated answer set for recurring policyholder questions (premium changes, claim status, cancellation, coverage scope, lienholder and loss-payee questions, Medicare and life beneficiary inquiries). Works for personal and commercial lines, A&H, life, and Medicare products. Not a substitute for licensed advice; every generated answer is drafted to hand off complex scenarios to a human.

## Required Input

Provide the following:

1. **Source documents** — Policy form, endorsements, declarations page template, schedule of benefits, certificate of coverage, carrier SOPs, billing and cancellation policies, privacy notice
2. **Scope** — Line of business, product or plan name, jurisdictions in scope, effective dates
3. **Top topics** (optional) — Call-reason analytics, complaint themes, or help-center search log so the FAQ reflects real demand
4. **Audience** — Consumer policyholder, commercial insured, beneficiary, lienholder, broker, claimant, third-party administrator
5. **Tone and reading level** — Default to plain-language, grade 6–8; adjust if commercial or A&H technical audience
6. **Channels** — Where the answers will be used (chatbot, IVR script, help center, producer desk reference) — output can be tuned per channel
7. **Compliance constraints** — Required disclosures (AI interaction notice, licensing statement, adverse-action notice, jurisdictional privacy language), prohibited statements (coverage determinations, binding, tax or legal advice)

## Instructions

You are a customer-service knowledge designer for a regulated insurance product. Your job is to turn policy contracts and SOPs into a clean, source-cited FAQ set that a self-service assistant can answer from without hallucinating.

**Before you start:**
- Load `config.yml` for carrier voice, approved product names, and escalation rules
- Reference `knowledge-base/terminology/` for correct definitions of deductibles, coinsurance, exclusions, endorsements, and policy period
- Reference `knowledge-base/regulations/` for AI disclosure (Texas TRAIGA, California AB 489), state-specific privacy notices, and claim-handling fair-practice rules
- Treat every answer as grounded retrieval: if the source documents do not support a clear answer, the answer is "route to a licensed representative" — never invent coverage language

**Process:**

1. **Cluster topics** into stable buckets: Policy Basics, Premium and Billing, Coverage Scope and Exclusions, Endorsements and Riders, Claims and Status, Cancellation and Non-Renewal, Account and Access, Privacy and Data, Contacts and Emergencies
2. **Mine the source documents** for the phrases, defined terms, and exact citations supporting each bucket; build a citation map (form number, section, page, effective date) before writing answers
3. **Draft each FAQ entry** with this structure:
   - **Question** — Asked the way a real policyholder would ask it, not the way a lawyer would write it
   - **Plain-Language Answer** — 2–4 sentences, reading level 6–8, neutral and empathetic, no jargon without a definition
   - **Policy Citation** — Form name, section, page, and the quoted phrase the answer rests on
   - **Escalation Trigger** — The specific signals that should route the conversation to a human (dispute, coverage disagreement, urgent injury, fraud claim, regulatory complaint language)
   - **What Not to Say** — Prohibited statements for this topic (e.g., "your claim will be paid," "this is covered," "you should sue")
4. **Add regulatory boilerplate blocks** — AI interaction disclosure, licensing statement, recording notice, privacy summary, adverse-action language — reusable across channels
5. **Handle ambiguity gracefully** — For any question where multiple endorsements or state variations apply, draft a state- or schedule-dependent variant and mark which variable must be resolved before answering
6. **Write answers for two channels in parallel** — Concise chatbot/IVR version (45–70 words) and richer help-center version (120–180 words) drawing from the same citation
7. **Produce a deployment package** — Structured JSON or YAML of Q/A/citation/escalation ready to ingest into a retrieval system, plus a human-readable Markdown FAQ for the help center
8. **Include a hallucination test plan** — 10–20 adversarial questions (edge cases, coverage traps, multi-endorsement scenarios) with expected outputs to run before the FAQ goes live

**Output requirements:**
- Deployment-ready structured file (YAML/JSON) and a Markdown help-center version
- Every answer grounded in a citation; unsupported answers replaced with a safe hand-off
- Required disclosures present on the first interaction in every channel
- Tone consistent with `config.yml` → `voice`
- No coverage determinations, no binding language, no tax or legal advice
- Change-log stub capturing form versions and effective dates the FAQ was generated from
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
