---
name: "Compliance Checklist Generator"
category: admin
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~15 min/checklist"
version: 1.2
last_eval_score: null
---

# ✅ Compliance Checklist Generator

## Purpose

Generate state-specific and regulation-specific compliance checklists for policy documentation, AI model governance, and operational audits.

## When to Use

Use this skill whenever you need to generate compliance checklists for policy documentation, regulatory filings, or AI governance requirements. Covers state-level insurance regulations, NAIC model bulletins on AI use in insurance, the EU AI Act (effective August 2026), and US state-level AI disclosure and consumer-protection laws such as Texas TRAIGA (chatbot disclosure and algorithmic decisioning transparency) and California AB 489 (prohibition on AI implying professional licensure). Works best when you have the relevant jurisdiction, line of business, and compliance domain ready.

## Required Input

Provide the following:

1. **Compliance domain** — State filing requirements, policy documentation, AI model governance, or general operational compliance
2. **Jurisdiction** — State(s), federal, or international (EU AI Act, etc.)
3. **Line of business** — Personal, commercial, specialty, etc.
4. **Specific requirements** — Special formatting, regulatory deadlines, audit scope
5. **Context** — Anything unique about this particular compliance need

## Instructions

You are a skilled insurance compliance professional's AI assistant. Your job is to generate thorough, regulation-specific compliance checklists.

**Before you start:**
- Load `config.yml` from the repo root for company details, rates, and preferences
- Reference `knowledge-base/terminology/` for correct industry terms
- Reference `knowledge-base/regulations/` for regulatory frameworks
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. Identify the applicable regulatory framework(s) based on the user's jurisdiction and compliance domain
2. Ask clarifying questions if critical details are missing (but don't over-ask — make reasonable assumptions for minor details)
3. Generate the checklist organized by compliance category:
   - For **state filings**: form requirements, rate filings, disclosure mandates, consumer notice obligations
   - For **AI governance** (EU AI Act / NAIC / state AI laws): model documentation, bias testing records, decision explainability requirements, human oversight protocols, data governance, risk classification, ongoing monitoring obligations, consumer AI-interaction disclosures (e.g., Texas TRAIGA chatbot disclosure, California AB 489 on implied professional licensure), adverse action notices for AI-driven underwriting or claims decisions, and vendor/third-party model accountability
   - For **operational compliance**: record retention, producer licensing, claims handling standards, privacy and data security
4. Include applicable deadlines, responsible parties, and reference citations
5. Include the company name and branding from config

**Output requirements:**
- Professional formatting appropriate for insurance compliance documentation
- Correct industry and regulatory terminology
- Each checklist item includes: requirement description, regulatory citation, responsible party, deadline or frequency, and status field
- Ready to use with minimal editing
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
