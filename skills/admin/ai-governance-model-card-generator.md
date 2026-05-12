---
name: "AI Governance Model Card Generator"
category: admin
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~3–5 hours/model card"
version: 2.0
last_eval_score: null
---

# AI Governance Model Card Generator

## Purpose

Produce an insurance-grade model card and governance artifact bundle for any AI system used in underwriting, claims, pricing, marketing, or customer service. The output is structured to satisfy, in a single document: the NAIC AI Model Bulletin (including readiness for the NAIC AI Systems Evaluation Tool that state regulators are using in market-conduct and financial exams), the EU AI Act high-risk obligations that apply to life and health insurance pricing and risk assessment from August 2026, state AI laws (Texas TRAIGA, California AB 489, Indiana HB 1271 on downcoding, Colorado SB 21-169, Alabama SB 63, NY DFS Reg 187), and the carrier's own model-risk policy. When the system being documented is an inventoried model, the skill pre-populates the system identification block from `config.yml.ai_governance.model_inventory` to eliminate re-entry and ensure alignment with the official model register. Output is the artifact a model-risk officer, a DOI examiner, a data-protection authority, and an executive sponsor should all be able to read without rework.

## When to Use

Use this skill when a carrier, MGA, TPA, or insurtech vendor needs to (a) document a new AI system before production launch, (b) refresh an existing model card on a scheduled cycle using the inventoried model's metadata, (c) respond to a DOI AI Systems Evaluation Tool request, (d) prepare an EU AI Act high-risk conformity file, (e) close a gap surfaced in internal audit, or (f) hand a model card to a reinsurer or carrier partner as part of due diligence. Works for classic ML scorers (fraud, subrogation propensity, renewal retention), generative AI systems (coverage-letter drafter, FAQ chatbot, producer copilot), computer vision systems (damage estimation), and orchestrated multi-agent systems (agentic claims orchestrator). For agentic systems, cross-reference Agentic Claims Orchestration Template v2.0, which includes the vendor-pattern preset library and the HITL checkpoint library that feeds directly into Section 6 (Human Oversight) of this model card.

## Required Input

Provide one of the following:

**Option A — Inventoried model (preferred):** Provide the model name or internal model ID. The skill will pre-populate Sections 1–4 and the change-control history from `config.yml.ai_governance.model_inventory`. Confirm the pre-populated fields, supply any delta since the last inventory update, and provide the performance and fairness evidence, human oversight design, and consumer-disclosure artifacts for the current cycle.

**Option B — New or uninventoried system:** Provide the following directly:

1. **System identification** — Model or system name, version, internal owner, business owner, vendor or in-house flag, deployment environment, date of first production use
2. **Business purpose and scope** — What the system is designed to do, which claim/policy/consumer decisions it informs or automates, which lines and jurisdictions it applies to, whether it is on the critical path for a consumer-facing decision
3. **Data provenance** — Training data sources, licensed or proprietary, PII/PHI scope, geographic and demographic coverage, known data-quality issues, refresh cadence, retention policy, lineage to the Subject System of Record
4. **Model architecture** — Algorithm class (linear, tree, neural, LLM, ensemble, hybrid), pretraining provenance for foundation models, fine-tuning data, retrieval sources, third-party APIs invoked, the orchestrator (if any) that composes it
5. **Performance and fairness evidence** — Holdout accuracy, calibration, drift history, bias-testing results across protected classes (disparate impact, equalized odds, error-rate parity), sensitivity analysis, adversarial or red-team findings, rollout-gate thresholds
6. **Human oversight design** — HITL checkpoints, reviewer role and training, override rate, sampling rate for post-hoc audit, escalation and kill-switch procedure, incident response runbook
7. **Regulatory classification** — EU AI Act risk tier (high-risk for life/health pricing and risk assessment), Annex III citation, NAIC applicability, state-level applicability (TRAIGA, AB 489, Indiana HB 1271, NY Reg 187, Colorado SB 21-169, Alabama SB 63), adverse-action-notice posture
8. **Consumer disclosures** — AI-interaction disclosure language actually used at each touchpoint, translation support, accessibility posture, any implied-professional-license language that must be avoided, channel-by-channel disclosure map
9. **Vendor and third-party posture** — Contracts, data-processing agreements, subprocessor list, indemnities, audit rights, SOC 2 / ISO 27001 evidence, AI-specific certifications
10. **Change-control history** — Version history, prior incidents, retirement plan

## Instructions

You are a model-risk documentation author working under the direction of the carrier's chief model-risk officer and legal counsel. Produce an auditable, plain-English, insurance-grade model card plus the supporting governance artifacts. Everything must be defensible in a market-conduct exam, an EU AI Act conformity review, and an internal audit — concurrently.

**Before you start:**

Load `config.yml` from the repo root and extract the following:

- **`config.yml.ai_governance.model_inventory`** — the carrier's official model inventory. If the requested system is listed, pre-populate Sections 1 (System Identification), 2 (Intended Use and Users), 3 (Data Documentation), 4 (Model Documentation), and the Change-Control history from the inventory entry. Flag any field in the inventory that is stale (last-updated date older than the configured refresh cadence) as `INVENTORY REFRESH REQUIRED` and prompt for the updated value.
- **`config.yml.ai_governance.regulatory_profile`** — the carrier's per-state and per-LoB regulatory applicability map (which state AI laws apply to which lines, whether the EU AI Act applies given the carrier's EU operations, NAIC Model Bulletin applicability, adverse-action-notice jurisdictions). Use this to auto-populate Section 7 (Regulatory Classification) and Section 8 (Regulatory Alignment Appendix) without requiring the user to re-specify the regulatory landscape for every card.
- **`config.yml.ai_governance.approval_authority`** — per-model-risk-tier approval authority table: who must sign off on each risk-tier model-risk rating (inherent-risk tier × residual-risk tier → approver role, e.g., high-inherent / high-residual → CRO + Board AI Committee; medium/medium → Chief Model Risk Officer; low/low → Model Risk Manager). Use this to auto-populate the "Approver" field in the Model-Risk Rating artifact and to flag any model exceeding a risk tier whose card lacks the required approver sign-off as `APPROVAL REQUIRED`.
- **`config.yml.ai_governance.risk_thresholds`** — the carrier's configured model-risk tier thresholds (e.g., inherent-risk score ≥ 7 = high; 4–6 = medium; < 4 = low; residual-risk score after controls applied). Use these thresholds to produce a consistent, config-keyed risk rating rather than a qualitative one.
- **`config.yml.agency.voice`** — carrier communication tone for the executive summary and consumer-facing disclosure artifacts.
- **`config.yml.agency.languages_supported`** — if the carrier supports languages beyond English, produce translated variants of the consumer-disclosure library for each listed language.

Also reference:

- `knowledge-base/regulations/` for NAIC AI Model Bulletin, NAIC AI Systems Evaluation Tool prompts, EU AI Act Articles 9–15 obligations, state AI laws (TRAIGA, AB 489, Indiana HB 1271, Colorado SB 21-169, Alabama SB 63, NY Reg 187), Solvency II governance expectations where applicable
- Never copy language from a regulator's document verbatim beyond the short mandated notices; rewrite in the carrier's own words and cite the source
- Never fabricate performance metrics; if a metric is not provided in input, mark it as "pending" with a named owner and a due date
- Flag any claim that cannot be supported by the input as a governance gap, not a drafting gap

**Process:**

1. **System identification block.** If inventoried, pre-populate from `config.yml.ai_governance.model_inventory` and flag any stale fields. If new, populate from input. Include: name, version, owner, date, deployment surface, system diagram reference, EU AI Act high-risk classification (yes/no, Annex III citation), NAIC applicability, state AI law applicability (from `config.yml.ai_governance.regulatory_profile`). Include a plain-English "what this system does" paragraph an executive can read in 30 seconds.

2. **Intended use and users.** Describe the approved decisions the system informs, the users it serves (adjuster, producer, underwriter, customer-service rep, policyholder), and — critically — the out-of-scope uses that are prohibited.

3. **Data documentation.** Inputs and outputs at the field level; source systems; representativeness assessment across jurisdictions, demographics, and policy types; PII/PHI handling; retention and deletion; data quality metrics; drift indicators. Pre-populate from `config.yml.ai_governance.model_inventory` if inventoried.

4. **Model documentation.** Algorithm class, pretraining and fine-tuning provenance (for foundation models), prompts and system prompts for generative systems, retrieval sources, temperature/parameter posture, guardrails, known failure modes, refresh and retraining cadence. For agentic or orchestrated systems, cross-reference Agentic Claims Orchestration Template v2.0 for the vendor-pattern preset library and the failure-mode register. Pre-populate from `config.yml.ai_governance.model_inventory` if inventoried.

5. **Performance and fairness.** Accuracy, calibration, precision/recall for threshold decisions, confusion matrix, drift history, bias-testing results across protected classes (using CO SB 21-169 / NY DFS Reg 187 / IN HB 1271 / AL SB 63 / NAIC AI Model Bulletin / EU AI Act Annex III as the applicable legal framework per `config.yml.ai_governance.regulatory_profile`), disparate-impact analysis, sensitivity to input variation, adversarial testing outcomes, rollout gates cleared. For claims decisions, include consistency evidence so decisions are transparent, predictable, and auditable.

6. **Human oversight.** The HITL tier for every decision the system makes; named reviewer roles; review SLA; sampling for post-hoc audit; override rate tracked monthly; kill-switch authority and procedure from `config.yml.ai_governance.approval_authority`; incident-response runbook. For agentic systems, align the HITL checkpoint library against Agentic Claims Orchestration Template v2.0.

7. **Consumer disclosures.** Channel-by-channel map of the disclosure language used when AI is in the loop, including: Texas TRAIGA chatbot disclosure, California AB 489 prohibition on implied professional licensure, Indiana HB 1271 disclosure when AI is used for adverse prior-authorization determinations or claim downcode, Alabama SB 63 adverse-action notice where AI informs the decision, Colorado SB 21-169 adverse-action notice, and EU AI Act consumer-notice obligations. If `config.yml.agency.languages_supported` lists languages beyond English, produce translated variants for each.

8. **Regulatory alignment appendix.** Side-by-side matrix: obligation ↔ artifact that satisfies it ↔ named owner ↔ last-reviewed date. Covers NAIC Model Bulletin, NAIC AI Systems Evaluation Tool prompts, EU AI Act Articles 9–15, state AI laws per `config.yml.ai_governance.regulatory_profile`, adverse-action-notice obligations. Auto-populate the scope column from `config.yml.ai_governance.regulatory_profile`.

9. **Vendor and third-party posture.** Contracts and DPAs; subprocessors; audit rights; certifications; incident notification terms. Flag any vendor risk not contractually mitigated.

10. **Change-control and retirement.** Version history (pre-populated from `config.yml.ai_governance.model_inventory` if inventoried), incident log, open findings, retirement criteria, sunset plan.

11. **Executive summary.** One page, plain English in `config.yml.agency.voice`, suitable for the CEO, board AI-committee, or regulator-facing cover letter. Lead with what the system does, who reviews it, and what the residual risks are.

**Governance artifact bundle (produced alongside the model card):**

- **Model-risk rating** — inherent-risk score and tier, residual-risk score and tier (both computed against `config.yml.ai_governance.risk_thresholds`), rationale, required approver (from `config.yml.ai_governance.approval_authority`), `APPROVAL REQUIRED` flag if sign-off is missing, review date
- **Bias-testing report** — methodology, protected classes evaluated (per `config.yml.ai_governance.regulatory_profile`), results, mitigation plan
- **HITL playbook** — reviewer roles, review SLA, override process, sampling plan, training requirements
- **Consumer-disclosure library** — approved language per channel and jurisdiction, translated variants per `config.yml.agency.languages_supported`, versioned
- **Change-log** — machine-readable record of model, prompt, and guardrail changes tied to approvals
- **Regulator response kit** — pre-written, factual answers to the NAIC AI Systems Evaluation Tool prompts, mapped to evidence files, pre-scoped to the applicable states per `config.yml.ai_governance.regulatory_profile`

**Output requirements:**

- Plain English at a 10th–12th grade reading level for the body; technical appendices where precision is required
- Every claim cites the evidence file that supports it; anything unsupported is flagged "pending evidence" with a named owner and a due date
- Every disclosure is quoted verbatim exactly as it appears at the consumer touchpoint, not paraphrased
- `INVENTORY REFRESH REQUIRED` flags on any stale inventoried field
- `APPROVAL REQUIRED` flag on the model-risk rating if the required approver sign-off per `config.yml.ai_governance.approval_authority` is absent
- Document is versioned and dated; diff-friendly markdown headings
- Saved to `outputs/governance/<system-name>/` with the model card as the root file and the bundle in subfolders (`bias/`, `hitl/`, `disclosures/`, `changelog/`, `regulator-kit/`)
- Never invent metrics; never imply regulator approval that has not been granted; never copy verbatim from a regulator's document beyond short mandated notices

## Versioning

v2.0 (2026-05-11): Added pre-population from `config.yml.ai_governance.model_inventory` (Option A for inventoried models — eliminates re-entry, flags stale fields as `INVENTORY REFRESH REQUIRED`, aligns with the official model register); per-tenant regulatory-profile scoping from `config.yml.ai_governance.regulatory_profile` (auto-populates regulatory classification and the regulatory alignment appendix scope for each state AI law and EU AI Act applicability without user re-specification); approval-authority workflow from `config.yml.ai_governance.approval_authority` (per-risk-tier approver mapping, `APPROVAL REQUIRED` flag if sign-off is missing); model-risk tier computation against `config.yml.ai_governance.risk_thresholds` (consistent quantitative rating instead of qualitative); multi-language consumer-disclosure library variants gated on `config.yml.agency.languages_supported`; Alabama SB 63 adverse-action disclosure added alongside TX TRAIGA, CA AB 489, IN HB 1271, CO SB 21-169, and NY DFS Reg 187; AI-bias and disparate-impact framework scoped to the carrier's active regulatory profile rather than a generic list; cross-reference to Agentic Claims Orchestration Template v2.0 for the vendor-pattern preset library and the HITL checkpoint library for agentic-system model cards. Every v1.0 capability is preserved — the 11-section model card structure, the full governance artifact bundle (model-risk rating, bias-testing report, HITL playbook, consumer-disclosure library, change-log, regulator response kit), and all regulatory frameworks (NAIC, EU AI Act, TRAIGA, AB 489, HB 1271, SB 21-169, Reg 187) are all retained and extended.

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
