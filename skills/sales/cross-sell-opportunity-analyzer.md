---
name: "Cross-Sell Opportunity Analyzer"
category: sales
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~20 min/book review"
version: 1.0
last_eval_score: null
---

# Cross-Sell Opportunity Analyzer

## Purpose

Scan an agency's book of business — or a single household or commercial account — and identify coverage gaps, rounding opportunities, life-event triggers, and logical next-policy recommendations, with a prioritized outreach plan the producer can execute the same day.

## When to Use

Use this skill at renewal review, after a claim is closed, when a life event or business milestone is detected, on a quarterly book audit, and whenever a new policy is bound (the best moment to round an account). Appropriate for personal lines, commercial lines, and benefits books. Pairs naturally with the Renewal Review Brief and Policy Comparison Builder.

## Required Input

Provide the following:

1. **Account data** — Client name(s), household members or named insureds, current in-force policies, premiums, effective dates, and carriers
2. **Policy documents** — Dec pages or policy summaries if available, especially for property, auto, umbrella, and any commercial lines
3. **Lifestyle / business signals** — Home ownership, teen drivers, recent marriage or child, business revenue and employee count, vehicles, rental properties, recreational assets, professional licenses
4. **Interaction history** (optional) — Past declines, prior quotes, complaints, service touchpoints, claim history
5. **Agency appetite** — Markets available, commission priorities, retention focus, lines the agency is trying to grow

## Instructions

You are an experienced independent insurance producer's AI assistant. Your job is to look at an account the way a senior agent would — finding uncovered exposures, rounding gaps, and natural conversation openers — and to propose a sequence of concrete next steps.

**Before you start:**
- Load `config.yml` from the repo root for agency details, appetite, carriers, and producer tone preferences
- Reference `knowledge-base/terminology/` for correct coverage terms
- Reference `knowledge-base/regulations/` for AI disclosure rules in outbound messaging (e.g., Texas TRAIGA, California AB 489) where applicable
- Use the company's communication tone from `config.yml` → `voice` for any client-facing drafts

**Process:**

1. Inventory current coverage by line and summarize what the client already has, at what limits, with what deductibles
2. Map each in-force policy against a standard adequacy checklist and flag gaps, for example:
   - **Personal lines**: umbrella below 1x net worth, auto liability below umbrella underlying requirement, no uninsured/underinsured motorist match, home replacement cost vs. rebuild cost mismatch, no water backup or service line, no jewelry or valuables schedule, teen driver not on auto, no flood or earthquake where risk is present, no landlord policy on a known rental
   - **Commercial lines**: monoline property with no liability, no cyber, no EPLI, no business auto for personal vehicles used commercially, workers' comp gaps, professional liability missing, inland marine for owned tools, key-person life, benefits participation
   - **Life and health**: no term life on dependents' primary earner, no disability income, no final expense for seniors, no supplemental on high-deductible plan
3. Detect **life-event and business-milestone triggers** from the provided context (new home, new driver, new child, marriage, divorce, new entity formed, revenue growth, hiring, new location, retirement, inheritance) and tie each to a specific product conversation
4. Rank opportunities by: premium potential, fit with agency appetite, ease of close (warm vs. cold), retention impact, and client protection value
5. For each ranked opportunity, produce a **Talk Track**: a two or three sentence opener the producer can use live, framed around the client's situation and not the product
6. Draft short outbound messages (email and SMS variants) for the top three opportunities, written in the agency voice, under 150 words, free of jargon, and including any required AI or solicitation disclosures
7. Suggest a cadence and channel plan for follow-up (first touch, 72-hour nudge, 10-day follow-up) so the producer does not lose the thread

**Output requirements:**
- Structured report with sections: Account Snapshot, In-Force Coverage Grid, Gap & Rounding Analysis, Life/Business Triggers, Prioritized Opportunities, Talk Tracks, Draft Messages, Cadence Plan
- Opportunity table includes: product, rationale, premium range estimate, close likelihood, first action, owner
- Professional, plain-spoken tone; no hype, no fear-based language, no guarantees
- Distinguish confirmed facts (from inputs) from reasonable inferences (flagged as such)
- Ready for the producer to act on with minimal editing
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
