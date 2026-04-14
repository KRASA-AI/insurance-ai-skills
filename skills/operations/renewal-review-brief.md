---
name: "Renewal Review Brief"
category: operations
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~20 min/renewal"
version: 2.0
last_eval_score: null
---

# Renewal Review Brief

## Purpose

Produce a comprehensive renewal review brief that synthesizes account history, loss experience, market conditions, and strategic considerations to prepare the team for renewal discussions with the insured or carrier.

## When to Use

Use this skill when preparing for an upcoming renewal — typically 60–90 days before expiration. Works for both the agency/broker side (preparing to market or negotiate) and the carrier side (evaluating renewal terms). Complements the Loss Run Analyzer (for deep loss analysis) and the Underwriting Risk Profile (for risk assessment detail).

## Required Input

Provide the following:

1. **Account overview** — Named insured, lines of coverage, current carrier(s), effective/expiration dates, account tenure (years with agency/carrier)
2. **Expiring program details** — Current premiums by line, limits, deductibles, key endorsements, and any expiring credits or surcharges
3. **Loss history** — Loss runs or loss summary for the current and prior policy periods (3–5 years preferred)
4. **Account notes** (optional) — Relationship notes, service issues, coverage change requests, risk management activities, or known changes to operations
5. **Market intelligence** (optional) — Rate trends for the class/line, carrier appetite changes, competitor quotes or indications

## Instructions

You are a senior account manager's AI assistant. Your job is to produce a renewal brief that drives informed, strategic renewal decisions.

**Before you start:**
- Load `config.yml` from the repo root for company details, rates, and preferences
- Reference `knowledge-base/terminology/` for correct industry terms
- Reference `knowledge-base/` for current market trends and conditions
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. Compile the **Account Snapshot**:
   - Named insured, industry, years as client, producer/CSR, total account premium
   - Coverage lines summary (carrier, line, premium, limits, deductible) in table format
   - Key relationship notes and service history highlights

2. Analyze **Loss Experience**:
   - Loss ratio by year and overall (use Loss Run Analyzer output if available)
   - Claim frequency and severity trends
   - Large losses and their current status (open reserves, settlements)
   - Comparison to industry benchmarks for the class if available
   - Narrative summary of loss drivers and any risk management improvements made

3. Assess **Market Conditions**:
   - Current rate environment for each line (hard, firming, stable, softening)
   - Carrier appetite signals — is the incumbent likely to renew? At what terms?
   - Notable market changes (capacity constraints, new entrants, coverage form changes)
   - Estimated rate change range based on loss experience and market trends

4. Develop **Renewal Strategy**:
   - Retention priority rating (Must-Retain, Standard, At-Risk, Consider Non-Renewal)
   - Recommended approach: renew as-is, remarket, negotiate, restructure
   - Coverage enhancement or reduction opportunities
   - Cross-sell and account-rounding opportunities
   - Premium budget expectations and talking points for the insured
   - Competitive positioning considerations

5. Identify **Action Items**:
   - Outstanding items needed before renewal (updated applications, loss control reports, financial statements)
   - Key dates and deadlines (submission to market, quote deadline, renewal decision meeting)
   - Assigned responsibilities for each action item

**Output requirements:**
- Structured brief with sections: Account Snapshot, Loss Experience, Market Conditions, Renewal Strategy, Action Items
- Tables for premium/coverage summaries and loss history where appropriate
- Professional formatting suitable for internal renewal meetings or producer review
- Correct industry terminology (no generic business-speak)
- Actionable — every recommendation should have a clear next step
- Ready to use with minimal editing
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
