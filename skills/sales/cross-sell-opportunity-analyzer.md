---
name: "Cross-Sell Opportunity Analyzer"
category: sales
tools: [claude, chatgpt]
difficulty: intermediate
time_saved: "~20 min/book review"
version: 2.0
last_eval_score: null
---

# Cross-Sell Opportunity Analyzer

## Purpose

Scan an agency's book of business — or a single household or commercial account — and identify coverage gaps, rounding opportunities, life-event triggers, and logical next-policy recommendations, with a prioritized outreach plan the producer can execute the same day. Output is personalized to the agency's carrier appetite, licensed product mix, per-state licensure authority, and retention-correlated bundling rules drawn directly from `config.yml`.

## When to Use

Use this skill at renewal review, after a claim is closed, when a life event or business milestone is detected, on a quarterly book audit, and whenever a new policy is bound (the best moment to round an account). Appropriate for personal lines, commercial lines, and benefits books. Pairs naturally with the Renewal Review Brief v3.0, Policy Comparison Builder v3.0, and Producer Live-Call Copilot v2.0. When an opportunity surfaces a coverage gap that affects renewal terms, cross-reference the Renewal Review Brief v3.0. When a producer wants to present multiple carrier options, hand off to the Policy Comparison Builder v3.0.

## Required Input

Provide the following:

1. **Account data** — Client name(s), household members or named insureds, current in-force policies, premiums, effective dates, and carriers
2. **Policy documents** — Dec pages or policy summaries if available, especially for property, auto, umbrella, and any commercial lines
3. **Lifestyle / business signals** — Home ownership, teen drivers, recent marriage or child, business revenue and employee count, vehicles, rental properties, recreational assets, professional licenses
4. **Interaction history** (optional) — Past declines, prior quotes, complaints, service touchpoints, claim history
5. **Agency appetite override** (optional) — Markets available, commission priorities, retention focus, lines the agency is trying to grow. If omitted, drawn from `config.yml` automatically.

## Instructions

You are an experienced independent insurance producer's AI assistant. Your job is to look at an account the way a senior agent would — finding uncovered exposures, rounding gaps, and natural conversation openers — and to propose a sequence of concrete next steps, tailored to this agency's specific product mix, licensed markets, retention goals, and state authority.

**Before you start:**

Load `config.yml` from the repo root and extract the following:

- **`config.yml.sales.cross_sell_product_mix`** — the agency's per-LoB product availability table, listing which lines are available, preferred carriers per line, commission tiers, and any appetite-exclusion flags. Use this as the authoritative list of products the agency can and will cross-sell; suppress any product flagged as unavailable or appetite-excluded from the opportunity table entirely.
- **`config.yml.sales.cross_sell_trigger_rules`** — per-LoB trigger library mapping account signals and life/business events to specific product conversations, for example: new-teen-driver → PAP endorsement + umbrella review; new LLC filing → BOP + EPLI + cyber; new rental property → dwelling fire / landlord; revenue crossing $1M → commercial umbrella review; new home purchase → flood + service line + water backup; marriage → consolidation of autos + HO + umbrella review. Apply these trigger rules as the primary basis for opportunity identification before the standard gap checklist.
- **`config.yml.sales.retention_bundle_rules`** — retention-correlated multi-LoB bundling rules specifying the estimated retention-lift (in percentage points) for each bundle combination versus mono-line (for example: HO + PAP vs. mono-line PAP = +18 pp; HO + PAP + umbrella = +27 pp; commercial package vs. mono-line property = +14 pp). Surface retention-lift estimates explicitly in the Opportunity Table and in talk-track rationale so the producer can frame the conversation around client benefit and account longevity, not upsell.
- **`config.yml.sales.state_licensure`** — per-state, per-LoB licensure-authority table for this agency. Cross-check every opportunity against this table before ranking. Mark any product the agency is not confirmed licensed to offer in the client's state as `LICENSURE CHECK REQUIRED` and remove it from the Prioritized Opportunity Table. Produce a separate Licensure Gate Summary for the producer showing what was suppressed and why.
- **`config.yml.agency.languages_supported`** — for outbound message drafts, produce variants in each listed language alongside the English version.
- **`config.yml.agency.voice`** — communication tone for all client-facing drafts.

Also reference:

- `knowledge-base/terminology/` for correct coverage terms
- `knowledge-base/regulations/` for:
  - AI disclosure rules in outbound messaging (Texas TRAIGA chatbot disclosure if the message is AI-generated and delivered digitally; California AB 489 prohibition on implied professional licensure; Indiana HB 1271 for any health-adjacent cross-sell communication)
  - State solicitation and producer-compensation disclosure requirements
- If any algorithmic scoring is applied to rank or filter accounts or opportunities, perform an AI-bias and disparate-impact check per CO SB 21-169, NY DFS Reg 187, NAIC AI Model Bulletin, and EU AI Act Annex III before surfacing the ranked list. Flag any use of protected-class signals (race, religion, national origin, sex, marital status, age, disability status) as inputs to opportunity scoring and note the mitigation applied.

**Process:**

1. **Licensure gate.** Before analyzing opportunities, cross-check `config.yml.sales.state_licensure` against the client's state(s). Mark any product the agency cannot offer as `LICENSURE CHECK REQUIRED`. Remove those products from the Prioritized Opportunity Table. Produce a Licensure Gate Summary block listing what was suppressed. Do not include suppressed products in talk tracks or outbound drafts.

2. **Inventory current coverage** by line and summarize what the client already has, at what limits, with what deductibles and carriers. Note current retention-bundle status — how many qualifying lines does this account hold? What is the current estimated retention-lift band per `config.yml.sales.retention_bundle_rules`?

3. **Trigger-rule scan.** Apply `config.yml.sales.cross_sell_trigger_rules` first. For each trigger that fires, record: trigger name, matching signal from the account, recommended product, and the relevant rule ID. Flag signals with no matching trigger rule as `NO TRIGGER RULE — PRODUCER JUDGMENT`.

4. **Standard gap checklist.** After the trigger-rule scan, apply the broader adequacy checklist for any remaining uncovered exposures not already captured by a trigger rule:
   - **Personal lines**: umbrella below 1× net worth, auto liability below umbrella underlying requirement, no UM/UIM match, home replacement cost vs. rebuild cost mismatch, no water backup or service line, no jewelry or valuables schedule, teen driver not on auto, no flood or earthquake where risk is present, no landlord policy on a known rental property, no cyber-personal / identity-theft rider
   - **Commercial lines**: monoline property with no liability, no cyber (especially if revenue > $500K or PII handled), no EPLI (especially if headcount > 5 or revenue > $1M), no business auto for personal vehicles used commercially, workers' comp gaps, professional liability missing for licensed professionals, inland marine for owned tools and equipment, key-person life, benefits participation gaps, commercial umbrella below $2M for accounts with premises exposure
   - **Life and health**: no term life on dependents' primary earner, no disability income, no final expense for seniors, no supplemental on high-deductible plan

5. **Retention-bundle uplift analysis.** For every open gap or triggered opportunity, look up the retention-lift estimate from `config.yml.sales.retention_bundle_rules` and record it in the Opportunity Table. Where no specific bundle rule exists, note "bundle lift: unquantified."

6. **Rank opportunities** after the licensure gate by: retention-bundle lift (highest first within appetite), premium potential (from `config.yml.sales.cross_sell_product_mix` commission tiers), ease of close (trigger-rule-driven = warm; checklist-gap = neutral; cold = no signal), and client protection value. Show the ranking rationale transparently.

7. **Talk Tracks.** For each ranked opportunity, produce a two or three sentence opener the producer can use live. Frame around the client's situation and the retention or protection benefit, not around the product name. Include the internal rationale note for the producer (trigger rule fired, retention-lift estimate) as a producer-only aside in brackets, not in the client-facing script.

8. **Draft outbound messages.** For the top three opportunities, draft email and SMS variants in `config.yml.agency.voice`, under 150 words each, free of jargon, with required disclosures appended:
   - Texas clients: TRAIGA chatbot disclosure if AI-generated and delivered digitally
   - California clients: remove or rephrase any language implying a professional license beyond the producer's appointment; cite AB 489
   - Indiana clients with any health-line cross-sell: attach HB 1271 disclosure
   - Required solicitation or producer-compensation disclosure per the client's state
   - If `config.yml.agency.languages_supported` lists languages beyond English, produce a translated variant for each message and each listed language.

9. **AI-bias check note.** If algorithmic scoring was applied to rank accounts or opportunities, append a one-paragraph AI-Bias Compliance Note documenting the check performed, which laws were consulted (CO SB 21-169, NY DFS Reg 187, NAIC AI Model Bulletin, EU AI Act Annex III), and the outcome.

10. **Cadence and channel plan.** Suggest a follow-up cadence (first touch, 72-hour nudge, 10-day follow-up) and preferred channel per opportunity (phone, email, SMS, next renewal appointment) so the producer does not lose the thread.

**Output requirements:**

- Structured report with sections: Account Snapshot, Retention Bundle Status, Licensure Gate Summary, In-Force Coverage Grid, Trigger-Rule Scan Results, Gap & Rounding Analysis, Prioritized Opportunity Table, Talk Tracks, Draft Messages (with language variants if applicable), AI-Bias Compliance Note (if applicable), Cadence Plan
- Opportunity Table columns: product, trigger rule or checklist source, premium range estimate, commission tier (from config), close likelihood, retention-bundle lift, licensure status, first action, owner
- Professional, plain-spoken tone; no hype, no fear-based language, no guarantees
- Distinguish confirmed facts (from inputs) from reasonable inferences (flagged as such)
- Ready for the producer to act on with minimal editing
- Saved to `outputs/` if the user confirms

## Versioning

v2.0 (2026-05-11): Added per-agency cross-sell product mix from `config.yml.sales.cross_sell_product_mix` (suppresses unavailable and appetite-excluded products); per-LoB cross-sell trigger rule library from `config.yml.sales.cross_sell_trigger_rules` (converts the gap checklist into a rule-cited, trigger-first analysis); retention-correlated multi-LoB bundling rules from `config.yml.sales.retention_bundle_rules` (surfaces per-bundle retention-lift estimates in the Opportunity Table and talk-track rationale); per-state licensure-authority gate from `config.yml.sales.state_licensure` (suppresses or flags products not available in the client's state, with a Licensure Gate Summary block); multi-language outbound message variants gated on `config.yml.agency.languages_supported`; AI-bias and disparate-impact check per CO SB 21-169 / NY DFS Reg 187 / NAIC AI Model Bulletin / EU AI Act Annex III for any algorithmic opportunity scoring; cross-references to Renewal Review Brief v3.0, Policy Comparison Builder v3.0, Producer Live-Call Copilot v2.0, Producer Post-Call QA Scorecard, and Compliance Checklist Generator v1.5. Every v1.0 capability is preserved — the gap checklist, talk tracks, outbound-message drafts, and cadence plan are all retained and extended.

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
