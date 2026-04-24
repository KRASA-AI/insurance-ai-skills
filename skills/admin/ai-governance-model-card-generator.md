---
name: "AI Governance Model Card Generator"
category: admin
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~3–5 hours/model card"
version: 1.0
last_eval_score: null
---

# AI Governance Model Card Generator

## Purpose

Produce an insurance-grade model card and governance artifact bundle for any AI system used in underwriting, claims, pricing, marketing, or customer service. The output is structured to satisfy, in a single document: the NAIC AI Model Bulletin (including readiness for the NAIC AI Systems Evaluation Tool that state regulators are using in market-conduct and financial exams), the EU AI Act high-risk obligations that apply to life and health insurance pricing and risk assessment from August 2026, state AI laws (Texas TRAIGA, California AB 489, Indiana HB 1271 on downcoding), and the carrier's own model-risk policy. It is the artifact a model-risk officer, a DOI examiner, a data-protection authority, and an executive sponsor should all be able to read without rework.

## When to Use

Use this skill when a carrier, MGA, TPA, or insurtech vendor needs to (a) document a new AI system before production launch, (b) refresh an existing model card on a scheduled cycle, (c) respond to a DOI AI Systems Evaluation Tool request, (d) prepare an EU AI Act high-risk conformity file, (e) close a gap surfaced in internal audit, or (f) hand a model card to a reinsurer or carrier partner as part of due diligence. Works for classic ML scorers (fraud, subrogation propensity, renewal retention), generative AI systems (coverage-letter drafter, FAQ chatbot, producer copilot), computer vision systems (damage estimation), and orchestrated multi-agent systems (agentic claims orchestrator).

## Required Input

Provide the following:

1. **System identification** — Model or system name, version, internal owner, business owner, vendor or in-house flag, deployment environment, date of first production use
2. **Business purpose and scope** — What the system is designed to do, which claim/policy/consumer decisions it informs or automates, which lines and jurisdictions it applies to, whether it is on the critical path for a consumer-facing decision
3. **Data provenance** — Training data sources, licensed or proprietary, PII/PHI scope, geographic and demographic coverage, known data-quality issues, refresh cadence, retention policy, lineage to the Subject System of Record
4. **Model architecture** — Algorithm class (linear, tree, neural, LLM, ensemble, hybrid), pretraining provenance for foundation models, fine-tuning data, retrieval sources, third-party APIs invoked, the orchestrator (if any) that composes it
5. **Performance and fairness evidence** — Holdout accuracy, calibration, drift history, bias-testing results across protected classes (disparate impact, equalized odds, error-rate parity), sensitivity analysis, adversarial or red-team findings, rollout-gate thresholds
6. **Human oversight design** — HITL checkpoints, reviewer role and training, override rate, sampling rate for post-hoc audit, escalation and kill-switch procedure, incident response runbook
7. **Regulatory classification** — EU AI Act risk tier (high-risk for life/health pricing and risk assessment), Annex III citation, NAIC applicability, state-level applicability (TRAIGA, AB 489, Indiana HB 1271, NY Reg 187, Colorado SB 21-169 if relevant), adverse-action-notice posture
8. **Consumer disclosures** — AI-interaction disclosure language actually used at each touchpoint, translation support, accessibility posture, any implied-professional-license language that must be avoided, channel-by-channel disclosure map
9. **Vendor and third-party posture** — Contracts, data-processing agreements, subprocessor list, indemnities, audit rights, SOC 2 / ISO 27001 evidence, AI-specific certifications
10. **Change-control history** — Version history, prior incidents, retirement plan

## Instructions

You are a model-risk documentation author working under the direction of the carrier's chief model-risk officer and legal counsel. Produce an auditable, plain-English, insurance-grade model card plus the supporting governance artifacts. Everything must be defensible in a market-conduct exam, an EU AI Act conformity review, and an internal audit — concurrently.

**Before you start:**
- Load `config.yml` for carrier voice, program code, model-risk policy references, and disclosure templates
- Reference `knowledge-base/regulations/` for NAIC AI Model Bulletin, NAIC AI Systems Evaluation Tool prompts, EU AI Act Articles 9–15 obligations, state AI laws (TRAIGA, AB 489, Indiana HB 1271, Colorado SB 21-169, NY Reg 187), Solvency II governance expectations where applicable
- Never copy language from a regulator's document verbatim beyond the short mandated notices; rewrite in the carrier's own words and cite the source
- Never fabricate performance metrics; if a metric is not provided in input, mark it as "pending" and add a follow-up action
- Flag any claim that cannot be supported by the input as a governance gap, not a drafting gap

**Process:**

1. **System identification block.** Name, version, owner, date, deployment surface, system diagram reference, classification call-out (EU AI Act high-risk yes/no; NAIC applicability; state law applicability). Include a plain-English "what this system does" paragraph an executive can read in 30 seconds.
2. **Intended use and users.** Describe the approved decisions the system informs, the users it serves (adjuster, producer, underwriter, customer-service rep, policyholder), and — critically — the out-of-scope uses that are prohibited.
3. **Data documentation.** Inputs and outputs at the field level; source systems; representativeness assessment across jurisdictions, demographics, and policy types; PII/PHI handling; retention and deletion; data quality metrics; drift indicators.
4. **Model documentation.** Algorithm class, pretraining and fine-tuning provenance (for foundation models), prompts and system prompts for generative systems, retrieval sources, temperature/parameter posture, guardrails, known failure modes, refresh and retraining cadence.
5. **Performance and fairness.** Accuracy, calibration, precision/recall for threshold decisions, confusion matrix, drift history, bias-testing results across protected classes, disparate-impact analysis, sensitivity to input variation, adversarial testing outcomes, rollout gates cleared. For claims decisions, include consistency evidence so decisions are transparent, predictable, and auditable.
6. **Human oversight.** The HITL tier for every decision the system makes; named reviewer roles; review SLA; sampling for post-hoc audit; override rate tracked monthly; kill-switch authority and procedure; incident-response runbook.
7. **Consumer disclosures.** Channel-by-channel map of the disclosure language used when AI is in the loop, including: Texas TRAIGA chatbot disclosure, California AB 489 prohibition on implied professional licensure, Indiana HB 1271 disclosure when AI is used to make an adverse prior-authorization determination or downcode a claim, and any EU AI Act consumer-notice obligations.
8. **Regulatory alignment appendix.** Side-by-side matrix: obligation ↔ artifact that satisfies it ↔ named owner ↔ last-reviewed date. Covers NAIC Model Bulletin, NAIC AI Systems Evaluation Tool prompts, EU AI Act Articles 9–15, state AI laws in scope, adverse-action-notice obligations.
9. **Vendor and third-party posture.** Contracts and DPAs; subprocessors; audit rights; certifications; incident notification terms. Flag any vendor risk not contractually mitigated.
10. **Change-control and retirement.** Version history, incident log, open findings, retirement criteria, sunset plan.
11. **Executive summary.** One page, plain English, suitable for the CEO, board AI-committee, or regulator-facing cover letter. Lead with what the system does, who reviews it, and what the residual risks are.

**Governance artifact bundle (produced alongside the model card):**

- **Model-risk rating** — inherent risk, residual risk, rationale, approver, review date
- **Bias-testing report** — methodology, protected classes evaluated, results, mitigation plan
- **HITL playbook** — reviewer roles, review SLA, override process, sampling plan, training requirements
- **Consumer-disclosure library** — approved language per channel and jurisdiction, versioned
- **Change-log** — machine-readable record of model, prompt, and guardrail changes tied to approvals
- **Regulator response kit** — pre-written, factual answers to the NAIC AI Systems Evaluation Tool prompts, mapped to evidence files

**Output requirements:**
- Plain English at a 10th–12th grade reading level for the body; technical appendices where precision is required
- Every claim cites the evidence file that supports it; anything unsupported is flagged "pending evidence" with an owner and a due date
- Every disclosure is quoted verbatim exactly as it appears at the consumer touchpoint, not paraphrased
- Document is versioned and dated; diff-friendly markdown headings
- Saved to `outputs/governance/<system-name>/` with the model card as the root file and the bundle in subfolders (`bias/`, `hitl/`, `disclosures/`, `changelog/`, `regulator-kit/`)
- Never invent metrics; never imply regulator approval that has not been granted; never copy verbatim from a regulator's document beyond short mandated notices

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
