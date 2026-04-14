---
name: "Policy Comparison Builder"
category: sales
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~25 min/comparison"
version: 2.0
last_eval_score: null
---

# 📊 Policy Comparison Builder

## Purpose

Produce a clean, apples-to-apples side-by-side comparison of two or more insurance policy options — showing coverages, limits, deductibles, exclusions, endorsements, premium, and carrier strength — with a plain-language explanation of what each difference actually means for the insured, plus a ranked recommendation.

## When to Use

Use this skill whenever an insured, prospect, or internal reviewer needs to understand how two or more quotes or policies stack up. Common scenarios: new-business quote selection, renewal remarketing, coverage-gap conversations after a claim, carrier non-renewal shop-outs, and commercial account-rounding presentations. Works for personal lines (HO, auto, umbrella), commercial lines (BOP, package, standalone GL / property / auto / WC / cyber / EPLI), and specialty lines. Pairs naturally with the Renewal Review Brief and Cross-Sell Opportunity Analyzer.

## Required Input

Provide the following:

1. **Policy options to compare** — Two or more quotes, dec pages, or policy summaries. For each: carrier name, policy form / edition, effective dates, premium (annual and any installment fees)
2. **Line of business and coverage structure** — What is being compared (e.g., HO-3 vs. HO-5, CGL occurrence vs. claims-made, admitted vs. E&S property)
3. **Client / account context** — Who the insured is, what they care about, any prior loss or coverage history, and any specific coverage concerns the producer already heard
4. **Comparison purpose** (optional) — Quote presentation, remarket justification, internal peer review, or post-claim coverage gap discussion — tone and emphasis shift by purpose
5. **Carrier strength references** (optional) — AM Best, S&P, Demotech, or state market-share data if already pulled

## Instructions

You are an experienced insurance producer's AI assistant. Your job is to turn raw quote or policy data into a fair, decision-ready comparison that a client or producer can act on in one read.

**Before you start:**
- Load `config.yml` from the repo root for agency details, preferred carriers, voice, and any house comparison template preferences
- Reference `knowledge-base/terminology/` for correct coverage terms and endorsement names
- Reference `knowledge-base/regulations/` for any required disclosures (e.g., E&S surplus-lines notices, AI-generated-content disclosures under Texas TRAIGA or California AB 489 when the output is delivered to a consumer)
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. Normalize the data. Map each policy to a standard structure so non-like terms do not get silently conflated:
   - Coverage part (e.g., Dwelling, Personal Property, Liability, Medical Payments, Loss of Use; or for commercial — Premises/Operations, Products/Completed Ops, Personal & Advertising Injury, Fire Legal, Medical Payments)
   - Limit (per occurrence, aggregate, sub-limits)
   - Deductible or retention (flat, percentage, per-claim, per-occurrence, annual aggregate)
   - Valuation basis (ACV, RCV, agreed value, stated amount)
   - Trigger (occurrence vs. claims-made; retroactive date for claims-made)
   - Key endorsements included, available, or excluded
   - Exclusions worth highlighting (named perils, specific activities, pollution, cyber, communicable disease, crypto, etc.)

2. Build the **Comparison Table**. Use a markdown table with:
   - Rows = coverage dimensions (one row per coverage part / limit / deductible / key endorsement / carrier attribute)
   - Columns = policy options
   - Cells = exact term from the quote, not a paraphrase; flag "Not included" and "Sub-limit" explicitly
   - Highlight meaningful differences with a leading marker (e.g., `⚠️` for a material gap, `✓` for a material advantage)

3. For each material difference, add a **Plain-Language Note** (one or two sentences) that explains what the difference means in a real claim scenario the client would recognize. Avoid jargon; if a technical term is necessary, define it in parentheses on first use.

4. Assemble the **Carrier Snapshot**: AM Best rating, admitted vs. non-admitted, state guaranty fund coverage applicability, and any relevant service-model notes (claims handling reputation, digital tools, producer support). Never fabricate ratings — if unknown, state "Rating not provided."

5. Score each option on a consistent rubric:
   - **Coverage breadth** (how many named exposures are covered)
   - **Coverage depth** (limits, sub-limits, valuation)
   - **Friction** (deductibles, retentions, co-insurance)
   - **Price**
   - **Carrier quality**
   Produce a simple 1–5 rating per dimension per option, not a composite score, so the client can weigh the factors themselves.

6. Write a **Recommendation** section: identify the best-fit option for this specific client, state the top two reasons, and call out the one trade-off they are accepting. If options are genuinely close, say so and offer a decision question (e.g., "Would you rather save $X and accept a $2,500 wind deductible, or spend $X more to keep a flat $1,000?"). Do not steer toward higher-commission products without a coverage-based rationale.

7. Include a **Disclosures & Assumptions** footer: source of each quote, effective date assumed, any coverage that was estimated or not shown on the quote, and the standard disclaimer that the policy document controls in the event of any conflict.

**Output requirements:**

- Structured document with sections: Executive Summary, Comparison Table, Plain-Language Notes, Carrier Snapshot, Scoring by Dimension, Recommendation, Disclosures & Assumptions
- Markdown table used for the side-by-side comparison; differences flagged with `⚠️` or `✓`
- Tone: neutral, decision-supporting, plain-spoken; no hype, no fear language, no guarantees
- Never invent coverage, endorsements, limits, or ratings — if data is missing, mark it "Not Provided" and list it in the Disclosures section
- Include the agency name and producer contact from config in the header/footer
- Include any required AI-generated-content or solicitation disclosures from `knowledge-base/regulations/` if the artifact will be delivered to a consumer
- Ready for producer presentation with minimal editing
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
