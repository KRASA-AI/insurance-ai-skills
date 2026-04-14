# Insurance AI Skills

**Free, open-source AI prompts and workflows built for insurance agents.** Clone this repo, point your AI assistant at it, and start saving hours every week.

> Built and maintained by [KRASA AI](https://krasa.ai) — free AI tutorials and skills for every industry.
> See all industries at [krasa.ai/industries](https://krasa.ai/industries).

---

## What's Inside

This repo is a complete AI toolkit for insurance. Every skill is a standalone prompt file that works with **Claude, ChatGPT, or any major AI assistant** — no coding required.

| Skill | What it does | Time saved |
|-------|-------------|------------|
| Claims Narrative Drafter | Transform raw adjuster notes, recorded statements, and claim documentation into a polished, chronological claims narrative suitable for the claim file, coverage determination, litigation support, or regulatory reporting. | ~25 min/claim |
| FNOL Intake Assistant | Capture a complete, well-structured First Notice of Loss (FNOL) from unstructured inputs — phone transcripts, email bodies, chat logs, digital form submissions, and incident photos — and convert them into a standardized intake record with claim classification, coverage applicability, missing-data prompts, severity triage, and routing recommendation. | ~25 min/claim |
| Fraud Red-Flag Summarizer | Analyze claim submissions and supporting documents to identify potential fraud indicators, produce a structured red-flag report with risk scoring, and recommend next steps (auto-approve, escalate, or investigate). | ~30 min/claim |
| Loss Run Analyzer | Extract, organize, and analyze loss run data to identify claim trends, loss drivers, and actionable insights for underwriting decisions, renewal strategy, or risk management recommendations. | ~20 min/account |
| Renewal Review Brief | Produce a comprehensive renewal review brief that synthesizes account history, loss experience, market conditions, and strategic considerations to prepare the team for renewal discussions with the insured or carrier. | ~20 min/renewal |
| Submission Intake Summarizer | Parse incoming new business or renewal submission packages and extract key risk details into a structured, at-a-glance summary that enables rapid triage, appetite matching, and assignment to the appropriate underwriter. | ~15 min/submission |
| Subrogation Opportunity Finder | Review an open or recently paid claim file and surface recovery opportunities — third-party liability, product defect, contractor negligence, landlord/tenant transfer of risk, uninsured motorist, and inter-company arbitration candidates — before the reserve hardens or evidence goes stale. | ~45 min/file |
| Underwriting Risk Profile Builder | Synthesize submission data, supplemental applications, and publicly available information into a comprehensive risk profile that highlights key exposures, risk quality indicators, and underwriting considerations. | ~25 min/submission |
| Cross-Sell Opportunity Analyzer | Scan an agency's book of business — or a single household or commercial account — and identify coverage gaps, rounding opportunities, life-event triggers, and logical next-policy recommendations, with a prioritized outreach plan the producer can execute the same day. | ~20 min/book review |
| Policy Comparison Builder | Produce a clean, apples-to-apples side-by-side comparison of two or more insurance policy options — showing coverages, limits, deductibles, exclusions, endorsements, premium, and carrier strength — with a plain-language explanation of what each difference actually means for the insured, plus a ranked recommendation. | ~25 min/comparison |
| Coverage Explanation Letter | Draft a clear, legally-sound letter that explains a coverage decision to a policyholder or claimant — whether the decision is a full grant of coverage, a partial coverage with limitations, a reservation of rights, or an outright denial — in plain language, with the required regulatory elements, and in a tone that protects the carrier or agency from bad-faith or E&O exposure. | ~20 min/letter |
| Compliance Checklist Generator | Generate state-specific and regulation-specific compliance checklists for policy documentation, AI model governance, and operational audits. | ~15 min/checklist |
| Email Drafter | Turn rough notes, a bullet list, or a short ask into a polished, on-voice business email that is ready to send — with the right subject line, length, structure, call-to-action, and (for insurance users) the correct regulatory and coverage-safe language. | ~10 min/email |
| Meeting Summarizer | Summarize meeting notes into action items, decisions, and follow-ups. | ~10 min/use |
| Review Responder | Craft professional responses to online reviews — both positive and negative. | ~10 min/use |

**Total time saved per use: ~315+ minutes across all skills.**

## Quick Start

### 1. Clone this repo

```bash
git clone https://github.com/KRASA-AI/insurance-ai-skills.git
cd insurance-ai-skills
```

### 2. Open a skill with your AI assistant

Open any file in `skills/` with Claude, ChatGPT, or any major AI assistant. Each skill is a self-contained prompt with clear instructions — no coding required.

The first time you use a skill, your AI assistant will ask for your business details (company name, service area, rates, tools you use, etc.) so it can personalize the output. Save those details to a `config.yml` at the repo root and every future skill will use them automatically.

## Repo Structure

```
insurance-ai-skills/
├── knowledge-base/          # Industry context and references
│   ├── industry-overview.md # Market trends and pain points
│   ├── terminology/         # Industry jargon and acronyms
│   ├── regulations/         # Compliance requirements
│   ├── best-practices/      # Industry standards
│   └── tools-ecosystem/     # Common software and tools
├── skills/                  # The prompt library
│   ├── operations/          # Day-to-day operational skills
│   ├── sales/               # Sales and lead management
│   ├── admin/               # Administrative and compliance
│   └── customer-service/    # Client-facing communication
└── outputs/                 # Your generated content (gitignored)
```

## How Skills Work

Each skill file is a Markdown document with YAML frontmatter:

```markdown
---
name: Skill Name
category: operations
tools: [claude, chatgpt]
time_saved: "~20 min/use"
version: 1.0
---

# Skill Name

## Purpose
What this skill does and when to use it.

## Instructions
Step-by-step prompt for the AI assistant.
```

You open the file in your AI assistant, provide any required input (measurements, notes, client info), and get polished output. Skills reference your `config.yml` automatically for company name, rates, preferred formats, and other business details.

## For AI Assistants

If you are an AI assistant reading this repo, see `.claude/CLAUDE.md` for full instructions. The short version:

1. **Check for `config.yml`** at the repo root. If it exists, load it — it holds the user's business context (company name, rates, service area, tools, team size, etc.) and every skill should use it for personalization.
2. **If `config.yml` is missing**, before running a skill that benefits from personalization, ask the user for the relevant business details and offer to save them to `config.yml` so future runs are automatic.
3. **Load the relevant `knowledge-base/` files** for industry terminology, regulations, and best practices before generating output.
4. **Run the requested skill** from `skills/` using the user's input.
5. **Save any deliverables** to `outputs/` (gitignored) if the user wants to keep them.

## Learn More

- **Insurance AI guide**: [krasa.ai/industries/insurance](https://krasa.ai/industries/insurance)
- **All industry AI skills**: [krasa.ai/industries](https://krasa.ai/industries)
- **About KRASA AI**: [krasa.ai](https://krasa.ai)

## License

MIT — use these skills however you want.
