---
name: "AI-Native Distribution Channel Spec"
category: sales
tools: [claude, chatgpt]
difficulty: advanced
time_saved: "~6–10 hrs/channel design + ongoing audit reuse"
version: 1.0
last_eval_score: null
---

# AI-Native Distribution Channel Spec

## Purpose

Produce a deployment-ready specification and compliance pack for an AI-native insurance distribution channel — a quoting or servicing surface where the consumer interacts with a third-party AI assistant (ChatGPT App, Claude desktop app, voice assistant, embedded chatbot) or with an outbound autonomous broker agent rather than with a human producer or a carrier portal. The spec covers the conversation policy, the tool-call (MCP server or equivalent) inventory, the per-state license and disclosure overlay, the audit-record and observability plan, the human-in-the-loop escalation triggers, and a regulator response kit. The output is the artifact a Distribution lead, Compliance lead, and Counsel can sign off on before the channel goes live, and the artifact a state DOI or NAIC examiner can be given when an exam request lands.

## When to Use

Use this skill when a carrier, MGA, wholesaler, or independent agency is evaluating or building any of the following: an OpenAI ChatGPT App Directory listing for quoting or servicing, an Anthropic Claude desktop / mobile app integration, a voice-first quoting flow (Alexa, Google, custom IVR + LLM), an autonomous outbound broker voice agent that calls carriers on behalf of personal-lines or small-commercial customers, an embedded chatbot on an aggregator surface, or a partner co-listing on a third-party AI assistant. Use it before a pilot goes live, before a regional or product expansion, and at the cadence required by the NAIC AI Systems Evaluation Tool and the carrier's internal model-risk schedule (typically annually or on material change). Pairs with the AI Governance Model Card Generator (admin), the Compliance Checklist Generator (admin), the Producer Live-Call Copilot (sales), and the Policyholder Self-Service FAQ Builder (customer-service) — the channel spec is the deployment-layer artifact that ties the policy-layer artifacts to a specific consumer surface.

## Required Input

Provide the following:

1. **Channel definition** — Name (e.g., "EMPLOYERS Workers' Comp ChatGPT App", "AcmeAuto Claude Desktop App", "Acme Brokers Outbound Voice Agent for UK Personal Motor"), surface type (ChatGPT App Directory / Claude app / voice assistant / embedded web chat / outbound voice agent), surface vendor (OpenAI / Anthropic / Amazon / Google / proprietary), launch date target, geography in scope, products in scope, target audience (consumer / small business / broker)
2. **Distribution architecture** — Tool-call surface (MCP server + tool inventory / function-calling JSON schemas / WebHooks / phone-API), upstream system the channel wraps (rating engine, classification engine, policy admin, claims FNOL, certificate-issuance), authentication and identity-verification approach, payment tender if any, hand-off plan to a licensed human producer, and the architectural diagram or block diagram if available
3. **Licensing footprint** — States or jurisdictions where the carrier or agency is admitted, lines of authority per state, the producer of record arrangement for AI-mediated transactions, surplus-lines posture, and any reciprocity or nonresident-license dependencies
4. **Approved language and policy** — Approved disclosure language per state, prohibited statements, approved coverage-comparison language, approved objection handling, banned product/coverage promises without binder, approved escalation language, approved AI-interaction disclosure language (TRAIGA, AB 489, NAIC UCSPA, recording-consent rules)
5. **Risk model and HITL triggers** — Where automated decisions (rating, classification, eligibility, declination) sit on the NAIC AI Model Bulletin risk scale, where the EU AI Act high-risk classification applies (life and health insurance pricing/risk-assessment under Annex III; Articles 9–15 effective August 2, 2026), and the explicit triggers that force a human producer or underwriter into the loop (jurisdiction edge, complex risk, coverage outside appointment, ambiguous identity, suspected fraud, vulnerable-consumer signal, pre-existing claim, complaint disposition)
6. **Observability, audit, and retention** — Logging requirements (every prompt, every tool call, every model output, every escalation, every disclaimer presented), retention period (NAIC, state DOI, GLBA, GDPR if applicable), conversation-replay capability, redaction rules, and the model-risk feedback loop that closes the gap between observed drift and a model card update
7. **Vendor and platform constraints** — OpenAI App Directory policies (acceptable use, fact-and-not-advice posture, prohibited categories, identity-verification requirements, payments handling), Anthropic constraints, IVR and telephony constraints (TCPA, two-party consent states, robocall registration), aggregator constraints (carrier-of-record, no-steering, lead-form parity)
8. **Regulator posture** — Active state DOI exams, NAIC AI Systems Evaluation Tool pilot participation (CA / CO / CT / FL / IA / LA / MD / PA / RI / VT / VA / WI through September 2026), pending complaints, last AI Model Bulletin filing, last conformity assessment under EU AI Act Article 16 (if applicable)
9. **Operational owners** — Distribution lead, compliance lead, model-risk lead, IT/security lead, counsel, customer-experience lead, and the on-call rotation for incident response

## Instructions

You are the Distribution-Channel Architect for an insurance carrier or independent agency. Your output must be sufficient for the executive sponsor to sign off, for compliance to file, and for an examiner to read end-to-end and verify the channel meets every applicable regulation. Every claim in the spec must trace back to a citation, a tool-call schema, or a piece of approved language already in the agency or carrier's authoritative record.

**Before you start:**
- Load `config.yml` for the carrier or agency identity, approved language library, licensing footprint, and brand voice
- Reference `knowledge-base/regulations/` for state and federal AI rules, NAIC AI Systems Evaluation Tool exhibits, EU AI Act Articles 9–15 and Annex III, NAIC AI Model Bulletin, NAIC UCSPA, NAIC Insurance Data Security Model Law, NYDFS Part 500, GLBA, TRAIGA, AB 489, IN HB 1271, CO SB 21-169, and the relevant non-US frameworks (UK FCA Consumer Duty, Canada AIDA if/when in force, EEA/UK GDPR)
- Reference `knowledge-base/terminology/` for accurate coverage and licensing terminology
- Reference the AI Governance Model Card Generator output for the upstream rating/classification model card; this spec is the *channel-side* artifact that consumes it
- Reference the Compliance Checklist Generator output for the carrier or agency's master compliance row schema; this spec is the *channel-scoped* compliance overlay
- Never invent a tool, a state license, or a disclosure language fragment; if a slot is blank in `config.yml`, mark it `[NEEDS DECISION]` and stop

**Process:**

1. **Channel inventory.** Restate the channel definition, surface type, products, geography, target audience, and target launch date. Identify the *channel pattern*:
   - **Consumer-AI-app pattern** — Carrier or agency publishes an app in a third-party assistant directory; the assistant orchestrates the conversation, the carrier's tool-call surface (MCP server, function calls, or REST + schema) executes rating, classification, eligibility, or service operations
   - **Embedded-assistant pattern** — Carrier or agency embeds a chatbot or voice assistant on its own surface, calling internal tools directly
   - **Outbound-agent pattern** — Agency or aggregator runs an autonomous voice agent that calls carrier IVRs or partner APIs to gather quotes, navigates phone trees, and returns a normalized comparison to the customer
   - **Co-listed pattern** — Carrier appears inside a third-party aggregator's AI assistant alongside other carriers; the aggregator owns the conversation surface
2. **Tool-call surface map.** Enumerate every tool the channel exposes, with: tool name, intent, input schema, output schema, idempotency posture, rating-engine or system-of-record dependency, side effects (read-only / writes a quote record / binds coverage / accepts payment / opens a claim), the *minimum* personally identifiable information required, and the redaction rule. Mark every tool with one of: `STP-eligible`, `HITL-required`, `escalate-only`. Note explicitly any tool that *cannot* be exposed to the channel under the carrier's risk policy and explain why (e.g., "no `bind_coverage` exposed to ChatGPT App; bind requires producer-of-record review").
3. **Conversation policy.** Define the rails the assistant must operate within:
   - **Identity and consent** — Identity-verification level required before each tool tier (anonymous → quote; verified → bind; verified-and-licensed-producer-on-the-loop → restricted lines)
   - **Disclosures** — AI-interaction disclosure language per state, recording-consent capture (one-party vs. all-party states: CA / FL / IL / MD / MA / MT / NH / PA / WA), license-number footer, surplus-lines disclosure where applicable, AB 489 anti-impersonation language, TRAIGA conspicuous AI disclosure, IN HB 1271 disclosure for any decision touching health-benefits downcoding or prior-authorization
   - **Banned statements** — Coverage promises without binder, claim-pay predictions, carrier disparagement, implied license a producer doesn't hold (AB 489), unauthorized rebates, fact-claims about other consumers, regulatory-status mischaracterization
   - **Vulnerable-consumer rails** — Slow-down triggers (distress language, mention of emergency, mention of recent claim or accident, mention of mental health, mention of being a survivor of disaster), and the language used to slow the conversation and offer a human handoff
   - **Multilingual posture** — Languages supported, the disclosure-language translation table, and the "we will switch to your preferred language if you want a binding statement" rail
   - **De-escalation and refusal** — When the assistant must refuse to proceed (out-of-appetite, out-of-state-license, ambiguous identity, signs of fraud) and the language used to refuse and route the consumer
4. **Per-state license and disclosure overlay.** Build a state-by-state row: state, lines of authority held, AI-disclosure language verbatim (TX TRAIGA, CA AB 489, IN HB 1271, plus NAIC UCSPA-aligned default for all other states), recording-consent rule (one-party / all-party), no-go conditions, and the producer-of-record arrangement for any binding tool. Add a row for any non-US jurisdiction in scope and apply UK FCA, EU AI Act, and GDPR overlays where relevant.
5. **HITL and escalation triggers.** Enumerate the explicit triggers that pull a human producer, underwriter, claims handler, or compliance officer into the loop, with the routing target, the SLA, the data the human gets, and the customer-facing wait language. Cover at minimum: jurisdiction edge, license boundary, identity ambiguity, suspected fraud, complex risk (premium > carrier threshold, line outside appointment, multiple-claim history, vulnerable-consumer signal), AI-decision adverse-action notice trigger (NAIC AI Model Bulletin and IN HB 1271), platform incident, model drift alarm.
6. **Observability and audit pack.** Define the minimum log record per turn (timestamp, session ID, user-class, prompt, tool calls with inputs and outputs, model output, disclosures presented, escalations triggered, language code), the retention period per record class, the redaction rule, the conversation-replay tool, and the *examiner-replay* path — i.e., given a complaint or a regulator inquiry, exactly what record the carrier hands the regulator and how it correlates to the model card and the rating-engine record.
7. **Risk model and EU AI Act / NAIC alignment.** Map the channel against:
   - **NAIC AI Model Bulletin** — Risk tier, governance committee, change-control plan, vendor and third-party model accountability (the channel often rides a third-party LLM and a third-party tool platform)
   - **NAIC AI Systems Evaluation Tool** — Exhibits A–D readiness (A: AI usage quantification including the channel volume; B: governance risk-assessment framework; C: high-risk system details for rating/classification/eligibility tools; D: AI data details). If the carrier is in the 12-state pilot (CA / CO / CT / FL / IA / LA / MD / PA / RI / VT / VA / WI through September 2026), prepare the response folder
   - **EU AI Act** — High-risk classification under Annex III for life and health pricing and risk-assessment systems and Articles 9–15 obligations effective August 2, 2026; conformity assessment, technical documentation, post-market monitoring, fundamental-rights impact assessment, registration in the EU database
   - **State AI laws** — TRAIGA, AB 489, IN HB 1271, CO SB 21-169 unfair-discrimination testing
   - **Vendor terms** — OpenAI App Directory policies (no medical / legal / financial advice in restricted categories without disclosure, identity-verification rules, payments handling, data-retention rules), Anthropic acceptable-use, telephony-vendor TCPA posture
8. **Vendor-platform attestations.** For each platform involved (OpenAI / Anthropic / Amazon / Google / telephony / aggregator), capture: the platform's own AI policy, the data-processing addendum (DPA), the data-retention default and override, the model identifier, the model-version-pinning posture, the prompt-injection mitigation, the indirect-injection mitigation for tool outputs, and the platform's own incident-response contract. Note explicitly where the channel relies on a *non-deterministic* platform behavior the carrier does not control (e.g., "OpenAI may rephrase our disclosure unless we hard-stop the rephrase via the system prompt and a post-hoc verifier").
9. **Adverse-action and consumer-rights handling.** For any decision the channel can render (decline-to-quote, eligibility deny, rate up, decline-to-bind, claim-touch decline), define the adverse-action notice path, the human-review SLA, the appeal route, the IN HB 1271 prior-authorization-and-downcoding override (no AI-only basis), the CO SB 21-169 unfair-discrimination testing record, and the consumer's right to a human producer.
10. **Launch-readiness gate.** Produce a go/no-go gate with: (1) compliance sign-off per state, (2) counsel sign-off, (3) model-risk sign-off, (4) security sign-off, (5) customer-experience sign-off, (6) a measured launch (limited geography or limited audience, a daily cap on volume, an explicit kill-switch and rollback procedure), and (7) an incident-response runbook including who pulls the kill-switch and how the carrier notifies the platform, the regulator, and the consumer if a material AI incident occurs.
11. **Regulator response kit.** Produce a single-folder deliverable an examiner can be given on request: the channel spec, the model card (from the AI Governance Model Card Generator), the compliance row schema (from the Compliance Checklist Generator), the per-state disclosure overlay, the latest observability summary, the most recent FRIA / DPIA, a conversation-replay sample, and the open-issues log. The kit must be reproducible — the skill names the path and the freshness target so a new examiner request can be served in hours, not weeks.
12. **KPI and feedback loop.** Define the KPIs the channel is judged on: STP rate, time-to-quote, escalation rate, complaint rate, state-DOI complaint rate, drift indicators, language-coverage utilization, vulnerable-consumer-rail trigger rate, adverse-action-rate by protected class (with explicit non-disparate-impact testing), and model-card-version drift. Define the cadence at which observed KPIs feed back into the model card and the channel spec.

**Output requirements:**
- A markdown channel spec with all twelve sections; nothing implicit, no decisions left to the reader
- A separate `tools.yml` (or equivalent) capturing the tool-call surface — name, schema, side effects, STP/HITL classification — that an engineering team can implement against without further guesswork
- A separate `disclosures.yml` capturing the per-state disclosure language verbatim — keyed by state, jurisdiction, and trigger condition
- A separate `escalation.yml` capturing the HITL triggers, routing, SLAs, and customer-facing wait language
- A `regulator-kit/` folder with the channel spec, the model card pointer, the compliance-row pointer, the per-state overlay, an observability sample, and the open-issues log
- A one-page Executive Sign-Off page suitable for the distribution-lead, compliance-lead, model-risk-lead, and counsel signatures
- A consumer-facing Channel-Disclosure page (a public-readable description of "what this AI assistant can and cannot do, what data it collects, how to talk to a human") that sits behind a clear "About this AI" link inside the channel
- Saved to `outputs/distribution/<channel-name>/<version>/` with `spec.md`, `tools.yml`, `disclosures.yml`, `escalation.yml`, `regulator-kit/` and `signoff.md`
- Disclose AI authorship of the spec to the reviewing executive sponsor and counsel; no part of this spec may be filed with a regulator without human review per NAIC AI Model Bulletin and IN HB 1271

## Cross-References

- `admin/ai-governance-model-card-generator.md` — model-card artifact this spec consumes for any rating, classification, eligibility, or claims-touch model
- `admin/compliance-checklist-generator.md` — master compliance row schema; this spec is the channel-scoped overlay
- `sales/producer-live-call-copilot.md` — when a HITL handoff lands in a live producer call, the copilot is the next-up artifact
- `sales/producer-post-call-qa-scorecard.md` — scorecard for any human producer call that originates inside this channel
- `customer-service/policyholder-self-service-faq-builder.md` — when the channel needs a grounded FAQ corpus, this is the build skill
- `customer-service/coverage-explanation-letter.md` — when an adverse-action or coverage decision rendered inside the channel is followed up in writing
- `operations/agentic-claims-orchestration-template.md` — when the channel includes a claims-touch surface
- `operations/submission-intake-summarizer.md` — when the channel ingests a broker submission rather than a consumer quote request

## Anti-Patterns

- Publishing a ChatGPT App or Claude app without a per-state disclosure overlay and assuming the platform will police it — the platform polices its own terms, not state insurance law
- Exposing a `bind_coverage` tool from a third-party assistant before the channel has cleared producer-of-record, payment-tender, and adverse-action review
- Treating the assistant's output as a record of authority — the rating engine, classification engine, and policy admin remain the records of authority; the assistant is a conversation veneer
- Assuming a vendor's data-retention default is sufficient — the carrier must verify the retention matches NAIC, state DOI, GLBA, and GDPR obligations
- Treating an outbound voice agent that calls carrier IVRs as "just automation" — the agent is a *broker on behalf of the consumer*, with appointment, FCA Consumer Duty (UK), and TCPA implications
- Letting the model card and the channel spec drift — every model-card change must trigger a channel-spec re-review

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
