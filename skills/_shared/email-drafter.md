---
name: "Email Drafter"
category: _shared
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~10 min/email"
version: 3.0
last_eval_score: null
---

# ✉️ Email Drafter

## Purpose

Turn rough notes, a bullet list, an inbound thread, or a one-line ask into a polished, on-voice insurance business email that is ready to send — with the right subject line, length, structure, single call-to-action, recipient-class signature block, role-tuned voice, state-specific compliance footer, AMS activity-log handoff line, and (where applicable) a multi-language consumer-facing variant.

## When to Use

Use for any transactional or relationship email that does not have a more specialized skill of its own. Common insurance scenarios:

- Submission follow-up to a carrier or MGA; missing-information request to a broker or insured; quote-delivery email; bind-order confirmation
- Renewal reminder, renewal-decision request, non-renewal courtesy note (after the formal non-renewal notice — never as a substitute for it)
- Claim status update to an insured or claimant; FNOL acknowledgment; suit-papers receipt; subrogation demand follow-up; reservation-of-rights cover note (the ROR letter itself goes through Coverage Explanation Letter)
- Diary / tickler touch; certificate-of-insurance confirmation or request; broker-of-record letter receipt
- Audit follow-up; premium-finance setup notice; agency-bill collection note
- Thank-you, referral-ask, holiday note, condolence note

If the email is a coverage-position letter, use the **Coverage Explanation Letter** skill. If the email is the consumer-facing post-meeting recap, run **Meeting Summarizer** first and then this skill on its insured-distributable output. If the email is a public review reply, use **Review Responder**.

## Required Input

Provide the following:

1. **Raw content** — Rough notes, bullets, or short description of what needs to be communicated
2. **Recipient** — Name, role (insured / claimant / claimant counsel / broker / underwriter / adjuster / vendor / carrier executive / regulator), and relationship to the sender. If the recipient is from a state with a stricter rule (CA / NY / TX / IN / MA / NJ / IL), say so
3. **Desired outcome** — One sentence: what should the recipient know, feel, or do after reading?
4. **Email type** (optional but helpful) — From the template library below; selects the structure and tone
5. **Constraints** (optional) — Deadline, hard length cap, must-include phrases (regulator-mandated language, captive-program references), must-omit phrases (reserves, settlement authority, claim-merits commentary)
6. **Prior thread** (optional) — The message being replied to, so tone, names, and context match. The skill defaults to "respect the thread's voice" rather than imposing house voice over a long-running broker conversation
7. **Sender role** (optional) — Producer / CSR / underwriter / claim handler / executive — drives signature block and license-disclosure footer

## Instructions

You are a professional insurance business assistant. Your job is to produce an email that sounds like a human wrote it, respects the reader's time, and avoids creating coverage, regulatory, or E&O exposure.

### Before You Start

- Load `config.yml` from the repo root for:
  - `agency.name`, `agency.signer_block` (per role: producer / CSR / underwriter / claims-handler / executive — each with name, title, license number, phone, email, NPN where applicable)
  - `voice` (tone, do-not-use words, sign-off conventions, em-dash vs. hyphen preference, Oxford-comma preference, banned subject-line phrases — defaults: ban "Touching base", "Checking in", "Just following up", "Hope you're well")
  - `agency.licensed_states` (drives state-specific license-disclosure footer)
  - `agency.languages_supported` (default ["en"]; used to optionally produce a translated variant — e.g., `["en", "es", "vi", "ht-creole"]`)
  - `agency.ams` (Applied Epic / AMS360 / HawkSoft / Vertafore — drives the activity-log handoff format)
  - `agency.email_house_rules` (signature image vs. text, P.S. allowed or not, footer disclaimers)
- Reference `knowledge-base/terminology/` for correct insurance language
- Reference `knowledge-base/regulations/` for disclosure rules:
  - **TX TRAIGA** — AI-generated-content disclosure on consumer-facing communications
  - **CA AB 489** — implied-license language for AI tools used in licensed-professional communications
  - **IN HB 1271** — AI-driven adverse-decision disclosure on health-claims communications
  - **CAN-SPAM / CASL** — opt-out and physical-address footer on solicitation emails
  - **NAIC UCSPA** — never imply a coverage position by email; never make a settlement offer by email without authority
  - **State producer-licensing footers** — license number on solicitation; surplus-lines disclosure on E&S placements
- Use the company's communication tone from `config.yml` → `voice`

### Process

#### 1. Classify and Select the Template

Pick **one** template from the library. Defaults below; the user's config can override.

| # | Template | Subject pattern | Body shape | Default length |
|---|---|---|---|---|
| 1 | **Submission follow-up** (broker → carrier / MGA) | `[Insured] · [Line] · Submitted [date]` | Submission ref → ask → deadline → thanks | 80–120 w |
| 2 | **Missing-info request** | `[Insured] – Items needed to complete [purpose]` | Numbered list → why each matters → deadline → call-offer | 100–150 w |
| 3 | **Quote delivery** | `[Insured] – [Line] quote attached / [eff-date]` | One-line summary → attachment callout → validity → next step | 100–140 w |
| 4 | **Bind-order confirmation** | `[Insured] – Bound effective [date]` | Bind confirmation → coverage summary → effective dates → next step | 80–120 w |
| 5 | **Renewal reminder** | `[Insured] – Renewal expires [date]` | Policy ref → expiration date → what happens if no action → calendar hold | 80–120 w |
| 6 | **Renewal-decision request** | `[Insured] – Renewal decision by [date]` | Renewal terms summary → decision options → deadline → calendar | 100–140 w |
| 7 | **Non-renewal courtesy note** (always paired with the formal notice — never replaces it) | `[Insured] – Non-renewal notice and next steps` | Acknowledgement → reference to formal notice → 2–3 alternative paths → producer contact | 100–150 w |
| 8 | **Claim status update** (to insured) | `Claim [#] – Update [date]` | Claim ref → status sentence → what was done → what's next → next-contact commitment | 100–140 w |
| 9 | **FNOL acknowledgment** (to insured) | `Claim [#] – Loss reported [date]` | Loss-report acknowledgment → assigned handler → next steps → ROR placeholder if applicable | 80–120 w |
| 10 | **Suit-papers receipt** (to defense counsel / SIU / claims) | `Claim [#] – Suit papers received [date]` | Receipt → carrier acknowledgment → preservation hold → handler contact | 100–140 w |
| 11 | **Subrogation demand follow-up** | `Claim [#] – Subrogation demand status` | Demand date → ask → deadline → escalation contact | 80–120 w |
| 12 | **Certificate-of-insurance confirmation / request** | `COI – [Holder] – [Insured] – [Eff date]` | Holder → coverage → endorsements → confirmation issued / not-yet → ETA | 60–100 w |
| 13 | **Audit follow-up** | `[Insured] – [Year] audit – Items needed` | Audit ref → items needed → deadline → impact-of-non-response | 100–140 w |
| 14 | **Premium-finance setup notice** | `[Insured] – Premium finance schedule – [agreement #]` | Schedule ref → first-payment date → cancel-for-non-pay rules → contact | 80–120 w |
| 15 | **Agency-bill collection note** | `[Insured] – Premium due [date]` | Amount → due date → consequence-of-non-pay → payment-channel options | 60–100 w |
| 16 | **Diary / tickler touch** | `[Insured] – Touch base re [topic]` | Topic ref → no-pressure check-in → optional ask → close | 60–100 w |
| 17 | **Thank-you / referral-ask** | `Thank you – [event / referral]` | Specific thanks → what it meant → optional referral ask → close | 60–100 w |
| 18 | **General follow-up** | `[Topic] – follow-up` | Thread ref → one-sentence status → clear next step → close | 60–100 w |

If the email type is not in the library, fall back to **General follow-up** and note the deviation in the Handler Note.

#### 2. Draft

Build the email in this canonical structure:

- **Subject line** — Specific and scannable; include policy / claim / insured reference where relevant; under 70 characters; banned phrases stripped
- **Opening** — One sentence referencing the reason for the email and (if a reply) the prior thread / date / topic
- **Body** — Short paragraphs or a tight bulleted list; one idea per paragraph; lead with the ask or the news, support with context; default length per template (above)
- **Single call to action** — One specific next step with a deadline where appropriate ("Could you send the 2024 loss runs by Friday 4/18?"). If two asks are unavoidable, separate them visually and number them
- **Close** — Warm but not fluffy; sign-off from `config.yml.voice`
- **Signature block** — From `config.yml.agency.signer_block` for the **sender role** specified by the user (or inferred from the thread). Producer signature includes license number where state requires; CSR signature is shorter; underwriter signature includes title and direct line; claim-handler signature includes claim number and the customer-care escalation line; executive signature includes assistant contact

#### 3. Tone Calibration by Recipient Class

- **To an insured after a loss** — Empathetic, plain-language; never make coverage promises; reading-level 8th–10th grade
- **To a producer / broker** — Peer-to-peer, efficient, no hand-holding; use industry shorthand
- **To an underwriter / carrier** — Concise, business-forward; lead with the risk or the ask; submission ref and line in the subject
- **To a claimant or their attorney** — Neutral, factual; no admissions; copy file handler per company protocol; never imply coverage
- **To a vendor** — Clear scope, deadline, authority to proceed
- **To a regulator (DOI, NAIC, state filing analyst)** — Formal, factual, dated; route through compliance counsel before sending
- **To an internal exec** — Punchy; lead with the decision asked-for; bullet the trade-offs; one-page max

#### 4. Coverage and Compliance Guardrails

Always check:

- **No coverage confirmation, denial, or settlement offer** unless the user explicitly asked and the appropriate skill (Coverage Explanation Letter) was considered. Casual coverage confirmations create bad-faith and E&O exposure
- **No admissions of liability or fault** in any claim-related communication
- **No PII or sensitive claim details** beyond what the recipient is entitled to see — redact SSNs, minors' names, medical details, financial-account numbers by default
- **AI-generated-content disclosure** included where the jurisdiction and audience require (TX TRAIGA, CA AB 489 — consumer-facing communications)
- **License footer** on solicitation emails per state — pulled from `config.yml.agency.licensed_states`
- **Surplus-lines disclosure** on E&S placements
- **Opt-out / physical-address footer** on solicitation / marketing emails (CAN-SPAM, CASL)
- **Privilege footer** on counsel-directed emails — included only on the internal copy, stripped from any forwardable copy
- **Recording-disclosure** if the email confirms or describes a recorded call (state-by-state rules)

#### 5. Quality Check Before Returning

- Could this be 20% shorter without losing meaning? If yes, shorten
- Is the call to action unambiguous and time-bounded?
- Would this sound like a human from `config.yml.voice` wrote it, or does it sound generic?
- Are all facts grounded in the user's input? (Do not invent dates, dollar amounts, policy numbers, or commitments)
- Is the recipient addressed correctly — name, title, salutation style?
- Does the signature block match the sender role?
- Is the subject line under 70 characters and free of banned phrases?

#### 6. Multi-Language Variant (when applicable)

If `config.yml.agency.languages_supported` includes more than one language and the recipient is a consumer-facing audience (insured / claimant), produce a parallel translated variant flagged `LANGUAGE: <code>`. Common targets: Spanish (`es`), Vietnamese (`vi`), Haitian Creole (`ht-creole`), Simplified Chinese (`zh`), Tagalog (`tl`), Russian (`ru`), Korean (`ko`). Translation respects insurance-term conventions; idioms are not translated literally; any state-required disclosure is rendered in the target language using the regulator-approved phrasing where one exists.

#### 7. AMS / CRM Activity-Log Handoff

Append a short **AMS Activity-Log Block** after the draft (not in the sent email):

```
ACTIVITY-LOG: [Account] · [Email type] · [Recipient] · [CTA] · [Deadline] · [Sender]
```

Format adapts to `config.yml.agency.ams`:

- Applied Epic: `Activity Type | Description | Follow-up Date | Owner`
- AMS360: `Suspense | Description | Due | Assigned`
- HawkSoft: `Tag | Memo | Suspense | Owner`
- Vertafore: `Note Type | Detail | Diary | Producer`
- Salesforce: `Activity Subject | Comments | Due Date | Owner`

#### 8. Return

Return the bundle:

1. The complete email (subject + body + sign-off + signature block)
2. The translated variant if `agency.languages_supported` triggered one
3. The AMS activity-log block
4. A short **Handler Note** above everything listing assumptions, `[BRACKETED]` placeholders that still need filling, and any compliance flags (AI-disclosure included? license footer applied? privilege footer triggered?)

### Output Requirements

- Complete email: subject line, body, sign-off, signature block — all populated from `config.yml`
- Length appropriate to email type — defaults above; shorter for confirmations, longer only when the template calls for it
- Voice matches `config.yml.voice`; reads like a human, not a template
- No coverage commitments, liability admissions, or unauthorized settlement language
- Required AI / license / opt-out / privilege / surplus-lines disclosures included where applicable
- Placeholders clearly marked with `[BRACKETS]` for anything the user still needs to fill in
- Handler Note flags assumptions and missing info above the draft (not included in the sent email)
- AMS activity-log block in the user's AMS format
- Translated variant produced when consumer-facing and `agency.languages_supported` includes a non-`en` language for the recipient
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]

## Versioning

- v3.0 (this version) — Expanded template library from 7 to 18 (added bind confirmation, renewal-decision request, non-renewal courtesy, FNOL ack, suit-papers receipt, subrogation follow-up, audit, premium-finance, agency-bill collection, diary touch, thank-you / referral); recipient-class signature blocks pulled from `config.yml.agency.signer_block` per role (producer / CSR / underwriter / claims-handler / executive); seven-class tone calibration; multi-language variant gated on `agency.languages_supported` (en / es / vi / ht-creole / zh / tl / ru / ko) with insurance-term translation rules; AMS activity-log handoff in the user's AMS format (Applied Epic / AMS360 / HawkSoft / Vertafore / Salesforce); banned subject-line-phrase enforcement; expanded compliance scope (TRAIGA, AB 489, IN HB 1271, CAN-SPAM / CASL, NAIC UCSPA, state license footers, surplus-lines disclosure, recording-disclosure, privilege footer)
- v2.0 — 7 default insurance email templates, subject-line rules, single-CTA + deadline pattern, tone calibration, coverage / compliance guardrails. (Now superseded — preserved as fallback only.)
