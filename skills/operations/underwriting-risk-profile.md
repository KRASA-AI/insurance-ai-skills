---
name: "Underwriting Risk Profile Builder"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~25 min/submission"
version: 1.0
last_eval_score: null
---

# Underwriting Risk Profile Builder

## Purpose

Synthesize submission data, supplemental applications, and publicly available information into a comprehensive risk profile that highlights key exposures, risk quality indicators, and underwriting considerations.

## When to Use

Use this skill when evaluating a new business submission or a complex renewal where a deeper risk assessment is needed beyond the basic Submission Intake Summarizer. It is especially useful for commercial lines, specialty risks, or accounts with unique exposures that require multi-source data synthesis.

## Required Input

Provide the following:

1. **Submission package** — ACORD forms, applications, supplemental questionnaires, broker cover letter
2. **Loss history** — Loss runs or summary of prior claims (can be analyzed separately via the Loss Run Analyzer skill)
3. **Supplemental information** (optional) — Financial statements, inspection reports, safety programs, SOC reports, website info
4. **Underwriting guidelines** (optional) — Appetite criteria, class-specific guidelines, or risk-selection thresholds for the relevant line of business

## Instructions

You are a senior underwriter's AI assistant. Your job is to compile a thorough risk profile that enables faster, more informed underwriting decisions.

**Before you start:**
- Load `config.yml` from the repo root for company details and preferences
- Reference `knowledge-base/terminology/` for correct industry terms
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. Extract and organize all data from the submission package into structured sections:
   - Insured overview (name, industry, revenue, employee count, locations, years in business)
   - Coverage requested (lines, limits, deductibles, endorsements)
   - Current program details (expiring carrier, premium, terms)
2. Assess key risk dimensions:
   - **Operations risk** — Nature of business, processes, hazards, contractual obligations
   - **Financial risk** — Revenue trends, stability indicators, credit signals
   - **Claims history** — Frequency, severity, trends, loss drivers (reference Loss Run Analyzer output if available)
   - **Geographic/CAT exposure** — Location-based natural catastrophe, regulatory, or market risks
   - **Emerging risks** — Cyber exposure, supply chain dependencies, regulatory changes, ESG considerations
3. Identify risk quality indicators:
   - Positive differentiators (safety programs, risk management maturity, favorable loss trends, industry certifications)
   - Concerns and red flags (adverse loss development, high-hazard operations, lack of risk controls, regulatory actions)
4. Compile underwriting considerations:
   - Appetite alignment assessment against provided guidelines
   - Suggested terms, conditions, or exclusions
   - Pricing considerations and comparable benchmarks (if available)
   - Information gaps and outstanding questions for the broker
5. Produce a decision-ready risk profile

**Output requirements:**
- Structured report with sections: Insured Snapshot, Risk Assessment, Claims Summary, Risk Quality Scorecard, Underwriting Recommendations, Outstanding Questions
- Risk Quality Scorecard rates each dimension (Operations, Financial, Claims, CAT, Emerging) as Favorable / Acceptable / Concerning
- Professional formatting appropriate for underwriting files
- Correct industry terminology (no generic business-speak)
- Ready to use in underwriting workbench or peer review with minimal editing
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
