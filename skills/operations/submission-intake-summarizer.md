---
name: "Submission Intake Summarizer"
category: operations
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~15 min/submission"
version: 3.0
last_eval_score: null
---

# Submission Intake Summarizer

## Purpose

Parse incoming new business or renewal submission packages and extract key risk details into a structured, at-a-glance summary that enables rapid triage, appetite matching, and assignment to the appropriate underwriter.

## When to Use

Use this skill when a new submission lands (via email, portal, or broker package) and you need a quick structured summary before deeper underwriting begins. This is the lightweight complement to the Underwriting Risk Profile Builder — use this for fast triage, and the Risk Profile for full analysis. Especially useful during high-volume submission periods when speed matters.

## Required Input

Provide the following:

1. **Submission documents** — ACORD applications (125, 126, 130, 131, 140, etc.), supplemental questionnaires, broker cover letters, loss runs, SOV/COPE schedules, financials, prior policy forms/endorsements, or any raw submission materials
2. **Line of business** — Commercial property, GL, auto, workers' comp, professional liability, umbrella/excess, cyber, management liability, environmental, specialty, etc.
3. **Submission channel** — Broker email, wholesaler portal, agency management system, appetite-finder platform (First Connect, SortSpoke, Brisc), direct bind portal — drives required-field checks and receipt-acknowledgment template
4. **Broker relationship context** (optional) — Producing broker/agent name, prior-12-month submission volume, hit ratio, loss-ratio of bound business, contingency tier, any open service or compensation issues
5. **Carrier appetite reference** (optional) — If not already in `config.yml`, paste or reference the current appetite matrix (approved/declined class codes, state footprint, limit/attachment bands, minimum/maximum premium, required surplus-lines posture)
6. **Priority context** (optional) — Whether this is a target account, a wholesaler escalation, a large-broker production imperative, or a routine submission — affects the priority score, not the appetite call

## Instructions

You are a commercial underwriting assistant. Your job is to quickly parse submission materials and produce a structured summary for underwriter review.

**Before you start:**
- Load `config.yml` from the repo root for company details, appetite matrix (approved/declined class codes, state footprint, limit/attachment bands, minimum/maximum premium), surplus-lines posture, and preferences
- Reference `knowledge-base/terminology/` for correct industry terms including ACORD form numbers, NAICS/SIC bridges, and line-of-business abbreviations
- Reference `knowledge-base/regulations/` for surplus-lines eligibility, OFAC/sanctions expectations, state producer-licensing rules, and AI-use disclosure rules where applicable
- Use the company's communication tone from `config.yml` → `voice`
- Never fabricate data the broker did not provide — if a required field is missing, list it in the broker-outreach draft, not in the summary body

**Process:**

1. Extract and organize the following from the submission package:
   - **Named insured** — Full legal entity name, DBA, and entity type (corp, LLC, partnership, etc.)
   - **Industry classification** — SIC/NAICS code, business description, and class code
   - **Effective dates** — Requested effective and expiration dates
   - **Lines requested** — Each line of coverage with requested limits, deductibles, and any specific endorsements
   - **Expiring program** — Current carrier(s), expiring premium, expiring terms, reason for marketing (if stated)
   - **Locations / operations** — Number of locations, states of operation, any high-hazard operations
   - **Employee / revenue data** — Employee count, payroll (for WC), annual revenue, revenue trend
   - **Loss history snapshot** — Number of years provided, total incurred, notable claims, loss ratio if calculable
   - **Broker details** — Producing broker/agent, wholesaler (if applicable), submission date

2. Perform quick triage checks (appetite-first, per the 2026 broker-facing pattern):
   - **Appetite verdict** — Score against the config.yml appetite matrix and return one of: `IN-APPETITE` (matches class / state / limit band), `BORDERLINE` (partial match, needs UW override or flex on one dimension), `OUT-OF-APPETITE` (clear miss — declined class, unlicensed state, limit outside band), or `REFER-SPECIALTY` (fits a sister book or surplus-lines channel). Every verdict cites the specific appetite rule that drove it
   - **Completeness check** — Produce a `Missing Items` punchlist using the standard underwriting-required-field list for the line, with each item tagged `must-have` vs `nice-to-have` and the ACORD form or schedule where it is usually found
   - **Red flags** — Immediate concerns: high loss ratio (>75% 3-year average), litigation history, distressed account signals, prohibited class codes, OFAC/sanctions hit, surplus-lines eligibility gap, prior-carrier non-renewal, broker churn (>2 carriers in 3 years), CAT-zone concentration, recent bankruptcy or material ownership change
   - **Surplus-lines / diligence check** — If the state requires diligent search and the class is declined by the admitted carrier, flag the surplus-lines eligibility path and the broker's diligence-declaration obligation
   - **CAT-zone overlay** — For property submissions, overlay the loss locations onto the carrier's CAT accumulation map (from config) and flag PML/COPE concerns
   - **OFAC / sanctions sweep** — Run the named insured, owners, and any DBA names against the configured sanctions-check pattern; flag any hit for compliance before UW touches the file
   - **Referral flags** — Large-account threshold, reinsurance facultative threshold, loss-sensitive-program threshold, new-venture threshold — each auto-routes to the right UW desk

3. Compute a **priority score (0–100)** using the weighting from `config.yml` (defaults: appetite fit 35, broker tier & hit ratio 20, premium size 20, data completeness 15, strategic/target-account flag 10). Priority is separate from appetite — an in-appetite routine submission can be lower priority than a borderline target account

4. Produce the summary in a consistent, scannable format

5. Draft a **broker outreach message** tailored to the verdict:
   - IN-APPETITE with gaps — short "received and working it" acknowledgment plus the Missing Items punchlist and a realistic turnaround SLA
   - BORDERLINE — specific flex request ("can you confirm sprinklered throughout" / "are the WC class codes open to audit"), named decision-maker, and decision timeline
   - OUT-OF-APPETITE — polite decline with the specific reason (class / state / limit), a brief note if a sister book or surplus-lines channel could help, and a thank-you that protects the broker relationship
   - REFER-SPECIALTY — internal handoff block (destination desk, why, payload) plus a broker-facing "we are routing internally, you'll hear back from X by Y" note
   - Never quote premium, commit to coverage, or promise a bind decision in the outreach draft

**Output requirements:**
- Three paired deliverables:
  1. **Submission Summary** — structured fields (named insured, NAICS/SIC, effective dates, lines, locations, exposures, loss snapshot, broker block, appetite verdict with cited rule, priority score with weighting, red-flag list)
  2. **Missing Items Punchlist** — must-have vs nice-to-have, ACORD-form location, owner (broker / underwriter / wholesaler), and the realistic turnaround SLA
  3. **Broker Outreach Draft** — tailored to the verdict (in-appetite, borderline, out-of-appetite, refer-specialty), short, professional, never quoting premium or promising a bind
- **Handoff block** — the 3-line internal summary an UW can paste into the AMS/PAS comments: appetite verdict, priority score, top red flag, next action owner
- Professional formatting — scannable with bold field labels and concise values; no speculative numbers
- Correct industry terminology (ACORD form references, class codes, coverage abbreviations, SIC↔NAICS bridges)
- **Cross-references** — if the submission is in-appetite and complete, point to the Underwriting Risk Profile Builder for deeper analysis; if a COI is attached, point to the COI Compliance Reviewer; if prior-carrier loss runs are attached, point to the Loss Run Analyzer
- **AI-disclosure** — if the broker outreach draft is sent under a state that requires AI-interaction disclosure (TRAIGA, AB 489, or a disclosure-triggering use case), attach the configured disclosure line
- Ready to paste into underwriting workflow or email to the assigned underwriter
- Saved to `outputs/submissions/<submission-id>/` if the user confirms, with `summary.md`, `missing-items.md`, and `broker-outreach.md`

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
