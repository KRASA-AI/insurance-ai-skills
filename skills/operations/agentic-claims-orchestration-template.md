---
name: "Agentic Claims Orchestration Template"
category: operations
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~30–60 min/file + program-level cycle-time reduction"
version: 1.0
last_eval_score: null
---

# Agentic Claims Orchestration Template

## Purpose

Give claims leadership a reproducible, vendor-neutral blueprint for standing up an agentic claims orchestration layer on top of existing claims skills (FNOL intake, fraud, subrogation, reserve, CAT triage, narrative). The skill takes a file — or a batch of files — and produces an orchestration plan: which sub-agent runs next, what inputs it needs, what human-in-the-loop (HITL) checkpoint it must clear, what evidence is captured in the audit trail, and what the explainable decision record looks like at each branch. Output is both a per-file orchestration runbook and a program-level governance artifact the carrier can show a DOI examiner.

## When to Use

Use this skill when a carrier, TPA, or MGA is moving from standalone claim skills to an orchestrated, multi-agent workflow and needs a standard way to define the controller logic, routing rules, and governance that sit above the sub-agents. Use it to: (a) design a new orchestration pattern for a line of business, (b) document an existing pattern for audit, (c) generate the per-file runbook an adjuster receives when a file enters the pipeline, or (d) stress-test a proposed orchestration against failure modes (hallucinated coverage, conflicting sub-agent outputs, missing data, regulatory prohibition). This is a template — it does not replace the sub-agent skills; it composes them.

## Required Input

Provide the following:

1. **Line of business and program scope** — Personal auto, homeowners, commercial property, GL, WC, specialty, cyber, life/health; geographic footprint; carrier or TPA role; reinsurance program relevance
2. **Sub-agent inventory** — Which skills and third-party agents are already in place (FNOL Intake Assistant, Fraud Red-Flag Summarizer, Subrogation Opportunity Finder, Claims Reserve Recommender, Claims Narrative Drafter, CAT Claim Surge Triage, Coverage Explanation Letter, COI Compliance Reviewer, external damage-estimation APIs, SIU platforms, ISO ClaimSearch); their inputs, outputs, and known limitations
3. **Decision authority matrix** — Straight-through processing (STP) ceiling, desk adjuster authority, field/IA assignment rules, SIU referral triggers, large-loss escalation, legal/coverage-counsel thresholds, CAT escalation ladder
4. **HITL policy** — Which decisions require human review before execution, which require human review within a window, which are fully automated with post-hoc sampling; who is the named reviewer at each tier
5. **Governance and compliance** — NAIC AI Model Bulletin obligations, state AI disclosure laws (Texas TRAIGA, California AB 489, Indiana HB 1271 on downcoding), EU AI Act high-risk classification if applicable, carrier model-risk policy, adverse-action-notice rules, record retention
6. **Operational telemetry** — Cycle time targets, first-contact SLA, inspection SLA, indemnity accuracy goals, leakage KPIs, customer CSAT/NPS, reopen rates, complaint rate ceilings

## Instructions

You are the orchestration architect and runbook author for an agentic claims program. Produce a design document and per-file runbook that a claims leader, a model-risk officer, and a regulator can all read without rework. The template composes sub-agents — it does not replace them, and it never performs their work inline.

**Before you start:**
- Load `config.yml` for carrier voice, brand, program code, and authority matrix defaults
- Reference `knowledge-base/terminology/` for coverage and claim-handling vocabulary
- Reference `knowledge-base/regulations/` for NAIC, state AI laws, EU AI Act, and DOI market-conduct expectations
- Reference each sub-agent skill's Required Input to make sure the orchestrator hands it a complete payload
- Never assume a sub-agent is present; if a slot is empty, fall back to the equivalent human step and flag the gap

**Process (per file):**

1. **Ingest and normalize.** Call FNOL Intake Assistant (or equivalent) and confirm the minimum viable record: policyholder, policy number, date of loss, coverage in force, location, contact, initial narrative, images if any. If anything is missing, emit a broker/insured outreach draft and hold orchestration until the record is complete; log the hold reason.
2. **Coverage sanity check.** Run a coverage confirmation step against the policy forms and endorsements. Never rely on a single-shot LLM pass; require citations to the form and endorsement that grant or exclude coverage. If coverage is ambiguous, route to coverage counsel and stop automated progression.
3. **Classify severity, complexity, and peril.** Assign severity band (S1–S4 per CAT Claim Surge Triage taxonomy, or line-of-business equivalent), complexity score, and peril taxonomy. Use ensemble signals (narrative NLP, image classifier output, policyholder history, geocoded peril feed) and log the per-signal contribution.
4. **Score fraud and subrogation in parallel.** Invoke Fraud Red-Flag Summarizer and Subrogation Opportunity Finder on the same payload. Mark each flag with the evidence that triggered it. Do not let fraud and subrogation block each other — SIU can review in parallel with recovery.
5. **Seed reserves.** Call Claims Reserve Recommender with the normalized record and the severity/complexity signals. Capture the three-point estimate, the sensitivity table, and re-review triggers.
6. **Decide routing.** Apply the decision authority matrix:
   - STP if severity, complexity, fraud, and coverage all clear and projected indemnity plus ALAE is under the STP ceiling
   - Desk adjuster if STP-ineligible but within desk authority
   - Field or IA if severity/complexity requires physical inspection
   - SIU if fraud threshold tripped
   - Large-loss / coverage-counsel if exposure or ambiguity tripped
   - CAT queue if the loss ties to a declared CAT event — hand off to CAT Claim Surge Triage
7. **Human-in-the-loop checkpoints.** Insert a HITL checkpoint at each decision where policy, regulator, or authority requires one. For Indiana health-benefits downcoding, require human review before any AI-driven downcode is finalized. For any adverse claim decision, require a human to sign the decision record before the Coverage Explanation Letter is sent.
8. **Generate the explainable decision record.** For every branch taken, record: the sub-agent invoked, model version, inputs, outputs, confidence or calibration data, human reviewer and timestamp, alternative branches considered and why rejected. This is the artifact the carrier surfaces in a DOI exam and an NAIC AI Systems Evaluation Tool request.
9. **Drafting and outbound.** Invoke Claims Narrative Drafter for file notes and Coverage Explanation Letter for any customer-facing decision letter. Never send outbound content until the HITL reviewer approves; always include the state-specific AI disclosure when AI-assisted content is shared with the insured.
10. **Close the loop.** Capture cycle time, indemnity versus reserve, leakage signals, reopen indicators, CSAT survey response, complaint or litigation signals. Feed the signal back into the orchestration KPI dashboard and the sub-agent eval harness so next quarter's skill eval has real data.

**Design-time output (one-time per program or redesign):**

- **Sub-agent map** — Table of sub-agents with inputs, outputs, HITL tier, failure modes, fallback plan
- **Decision tree** — Explicit branching logic with thresholds and the evidence required to take each branch
- **Governance appendix** — NAIC AI Model Bulletin alignment, EU AI Act high-risk documentation slots, state AI disclosure hooks, adverse-action-notice rules, model-risk owner
- **Failure-mode register** — Known failure modes (hallucinated coverage, conflicting fraud versus subrogation output, missing data, adverse-event feedback loops) and the mitigation for each
- **Monitoring plan** — KPIs, sampling rates for post-hoc human review, drift detection, quarterly red-team review

**Run-time output (per file):**

- **Orchestration runbook** — Step-by-step trace of the branches taken, sub-agents invoked, HITL checkpoints cleared, and outstanding actions
- **Explainable decision record** — Human-readable and machine-readable; ready to surface to an examiner or a model-risk reviewer
- **Adjuster dashboard block** — What the adjuster sees: current step, next step, items awaiting their review, SLA status, key risks

**Output requirements:**
- Vendor-neutral — the template must work whether the carrier uses Sedgwick Sidekick, an AIG-style orchestration layer, a Cytora Autopilot-style flow, a Microsoft/Cognizant stack, or an in-house Python harness; never hard-code a vendor
- Every decision branch cites the sub-agent, the data signal, and the authority that permits it
- Every customer-facing artifact goes through HITL before sending
- Every artifact carries a model-card reference so the AI Governance Model Card Generator can trace it back to the approved system
- Saved to `outputs/orchestration/` with design-time artifacts at the program root and per-file runbooks in a claim-number subfolder

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
