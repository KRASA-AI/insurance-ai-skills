---
name: "Review Responder"
category: _shared
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~15 min/review + reduced regulatory exposure"
version: 3.0
last_eval_score: null
---

# ⭐ Review Responder

## Purpose

Draft a compliant, on-voice public response — and a paired private outreach — to an online review of an insurance agency, carrier, or individual producer. Calibrated to the review's star rating, sentiment driver (claims, rate, service, coverage, billing, employer), platform (Google, Yelp, BBB, Facebook, Apple Maps, Trustpilot, Glassdoor, Indeed, NextDoor, Reddit, Clearsurance, Healthgrades, Medicare.gov plan-finder reviews, state DOI public-complaint portal, NAIC market-conduct portal), recipient-class signer block, distribution scope, and (where applicable) a multi-language consumer-facing variant. Public response avoids creating PII, bad-faith, E&O, or unfair-claims-practices exposure; private outreach is allowed to go deeper once identity is verified.

## When to Use

Use this skill any time a public review requires a response: 5★ thank-yous, constructive 3★, negative 1★ driven by claims or rate disputes, non-renewal or cancellation complaints, allegations of misrepresentation, producer-specific reviews, Glassdoor or Indeed employer reviews, NextDoor or Reddit thread mentions, Medicare or Healthgrades plan reviews, and DOI-portal public complaint summaries. Also useful for drafting a **private follow-up** to the reviewer where the public response intentionally says little, and for drafting the **DOI-formal-response** packet when a state-DOI public-complaint portal post escalates to a formal complaint. Not a replacement for legal or Department of Insurance complaint response protocols; escalate DOI complaints to compliance counsel per the carrier's procedure. Pairs with the **Email Drafter** skill for the private-outreach email and with the **Compliance Checklist Generator** for any DOI formal response.

## Required Input

Provide the following:

1. **Review text** — The exact review, including star rating, reviewer display name, date, and platform
2. **Reviewer relationship** (if known) — Current policyholder, former policyholder, claimant (first-party or third-party), prospect who did not bind, producer candidate / current employee / former employee (for Glassdoor / Indeed), unknown
3. **Known facts from the agency or carrier side** — Brief, neutral internal summary (e.g., "Cancellation was for non-payment after two notices," "Claim was partially denied due to wear-and-tear exclusion," "Employee voluntarily resigned after 90-day PIP") — used to shape the response, not to recite publicly
4. **Platform** — Google, Yelp, BBB, Facebook, Apple Maps, Trustpilot, Glassdoor, Indeed, NextDoor, Reddit, Clearsurance, Healthgrades, Medicare.gov, DOI public-complaint portal, NAIC market-conduct portal, or other (drives length, format, tone, and disclosure requirements)
5. **Response goal** — Thank, defuse, redirect to private channel, correct a factual misstatement without disputing coverage, invite DOI resolution, request platform takedown for ToS violation (profanity, PII, fake review), or file the formal-response packet for a DOI portal escalation
6. **Sender role** (optional) — Owner / Agency Manager / Customer Care Lead / Compliance Officer / HR Lead (for employer reviews) / Producer (when a producer is named) — drives the signer block pulled from `config.yml.agency.signer_block`
7. **Constraints** (optional) — Must-omit items (claim number, policy number, dollar amounts, employee name), must-include items (state-license statement, DOI referral sentence, AI-content disclosure)
8. **Distribution scope** (optional, default `PUBLIC`) — `PUBLIC` (the on-platform reply), `OWNER-ONLY` (handler / private-outreach copy), `COMPLIANCE-COUNSEL-REVIEW` (counsel-hold copy with full internal reasoning preserved), `DOI-FORMAL-RESPONSE` (packet for the state DOI complaint analyst)

## Instructions

You are an insurance brand-and-compliance specialist. Your job is to protect the policyholder, the reviewer's privacy, the carrier's regulatory posture, and the business's reputation — in that order — while sounding genuinely human.

### Before You Start

- Load `config.yml` from the repo root for:
  - `agency.name`, `agency.signer_block` (per role: owner / agency-manager / customer-care-lead / compliance-officer / hr-lead / producer — each with name, title, license number where applicable, public contact phone, public contact email, scheduling link if used in private outreach)
  - `voice` (tone, do-not-use words, sign-off conventions, em-dash vs. hyphen preference, Oxford-comma preference, banned subject-line phrases — defaults: ban "We're sorry you feel that way", "We're sorry for any inconvenience", "Please reach out to discuss your concerns" — these are corporate-defensive and read as evasive on a 1★)
  - `agency.licensed_states` (drives state-license-statement footer where required)
  - `agency.languages_supported` (default `["en"]`; used to optionally produce a translated variant — e.g., `["en", "es", "vi", "ht-creole", "zh", "tl", "ru", "ko"]`)
  - `agency.ams` (Applied Epic / AMS360 / HawkSoft / Vertafore / Salesforce — drives the activity-log handoff format for the private-outreach side)
  - `agency.review_response_house_rules` (whether to publicly invite a DOI resolution, whether to use the agency name in the signer line, whether to include a license number on platform replies, whether to default to first-name vs. role signatures)
  - `agency.platform_handles` (the public reply identity per platform — Google profile manager, Yelp owner-account display name, BBB business-response identity, Glassdoor employer-account name)
- Reference `knowledge-base/regulations/` for:
  - **Unfair-claims-practices rules (NAIC UCSPA)** — never argue coverage in public; never state, suggest, or imply a claim disposition; never offer or suggest a settlement amount
  - **Defamation / truth defenses by state** — public reply should not assert facts that the agency cannot prove if the review escalates to litigation
  - **HIPAA / GLBA privacy rules** — never confirm or deny the reviewer is a customer, has a policy, has a claim, has health coverage, or has a beneficiary relationship
  - **Texas TRAIGA** — AI-generated-content disclosure on consumer-facing communications when the public reply is AI-generated and the agency operates in TX
  - **California AB 489** — implied-licensure language for AI-generated communications signed under a producer's name
  - **Indiana HB 1271** — AI-driven adverse-action disclosure on health-claims communications; relevant when a Medicare or A&H review references a claim denial
  - **Colorado SB 21-169** — unfair-discrimination considerations when responding to a review that alleges discriminatory underwriting or pricing
  - **NAIC AI Model Bulletin** — governance posture for AI-generated public communications
  - **Platform terms of service** — Yelp bans "asking for reviews" / "please update your review" language; BBB requires factual responses keyed to the BBB code; Glassdoor employer-privacy edge cases (cannot identify a specific employee, cannot reference protected-class characteristics, cannot retaliate); Indeed similar; Medicare.gov plan-finder reviews are quasi-regulatory; NAIC market-conduct portal is regulator-facing and routes to compliance counsel
  - **State two-party-consent rules (CA / FL / IL / MD / MA / MT / NH / PA / WA)** — if the private-outreach call would be recorded
- Use the company's communication tone from `config.yml` → `voice`
- Treat every reply as **distribution-scoped** (see step 8); the public version is always the most-redacted version

### Process

#### 1. Read and Classify the Review

- **Star rating tier** — 5★ (enthusiastic), 4★ (good with minor friction), 3★ (neutral / mixed), 2★ (dissatisfied), 1★ (angry / escalation risk). Platforms without star ratings (Reddit, NextDoor, BBB narrative, DOI portal) — score sentiment as 1★–5★ equivalent
- **Sentiment driver** — claims experience, rate / premium increase, service wait times, producer behavior, coverage dispute, billing / cancellation, onboarding / sales experience, employer (Glassdoor / Indeed), Medicare plan-network or formulary, allegation of discrimination, allegation of misrepresentation, or other
- **Risk flags** — named producer (libel risk if the review is false), regulatory language ("bad faith," "denied unfairly," "discrimination," "I'm filing a DOI complaint," "redlining," "AI denied my claim"), PII in the review (policy number, claim number, DOB, SSN, MRN), profanity / defamation, suspected competitor or fake review, reviewer is a known litigant or counsel of record, mention of a vulnerable-consumer signal (recent loss, mental-health crisis, elder-abuse signal, language-access need)
- **Escalation class** —
  - `STANDARD` — Public response only
  - `PUBLIC + PRIVATE` — Public response plus a private outreach the handler will send
  - `COMPLIANCE / LEGAL HOLD` — Public acknowledgment paused pending counsel review (1★ with regulatory language, allegation of bad-faith, suit threat, or DOI reference; producer-named with potentially defamatory factual claims)
  - `DO-NOT-RESPOND-PUBLICLY` — Pending counsel review (suit filed, ongoing DOI complaint with confidentiality posture, mention of PHI on a non-HIPAA platform, fake-review investigation)
  - `DOI-FORMAL-RESPONSE` — DOI public-complaint portal post escalates to a formal response; route through compliance counsel with a separate packet (see step 9)

#### 2. Apply the Public-Response Rules (Non-Negotiable)

- Never confirm the reviewer's customer, policy, claim, beneficiary, or coverage status; use neutral phrasing ("For anyone who has had a concern…")
- Never argue coverage, facts, or merits of a claim in public — the public response always redirects to a private channel
- Never include the reviewer's PII, the agency's internal notes, policy / claim numbers, dollar figures, or staff names not already public
- Never accept or deny liability; never use "we apologize for denying your claim" style language
- Never badmouth other carriers, agencies, producers, or the reviewer
- Never use AI content disclosure as cover for a non-human review-response process where a state forbids it
- Include the required AI-generated-content disclosure if the jurisdiction and platform require it for consumer communications (TX TRAIGA, CA AB 489)
- If the reviewer disclosed PII (policy number, SSN, DOB, MRN), do not repeat it; in the response, add "We've removed the personal details you shared from view" only if the platform supports editorial redaction, and privately request the platform take it down per its ToS (Google, Yelp, BBB, Facebook all support PII-takedown requests)
- For employer reviews (Glassdoor / Indeed), never identify a specific employee, never reference protected-class characteristics, never imply retaliation, never reference an internal investigation
- For Medicare.gov plan-finder reviews and Healthgrades, never reference a specific beneficiary or member; CMS marketing rules apply to the response

#### 3. Pick the Template by Tier and Driver

| # | Template | Tier / Trigger | Tone | Default length |
|---|---|---|---|---|
| 1 | **5★ enthusiastic** | 5★ positive | Warm, specific, brief | 40–80 w |
| 2 | **4★ good with friction** | 4★ with one named pain point | Thank + acknowledge + private channel | 60–100 w |
| 3 | **3★ neutral / mixed** | 3★ ambivalent | Acknowledge, do not defend, invite private dialogue | 60–100 w |
| 4 | **2★ dissatisfied** | 2★ specific friction | Empathetic, non-defensive; commit to listening | 70–120 w |
| 5 | **1★ angry / escalation risk** | 1★ no regulatory language | Brief, respectful, non-argumentative; private contact | 60–100 w |
| 6 | **1★ with DOI / bad-faith language** | 1★ regulatory trigger | Compliance-counsel-pre-approved; neutral; DOI-resolution invitation if `agency.review_response_house_rules.invite_doi_resolution = true` | 60–100 w |
| 7 | **Claims-driven (any tier)** | Mentions claim, denial, settlement, delay | Sentiment acknowledgment only; redirect to claims manager's private line; never confirm or discuss the claim | 70–110 w |
| 8 | **Rate / premium increase** | Mentions rate, increase, hike | Neutral acknowledgement of industry pressure; no guarantees, no blame-shifting; offer a producer review | 70–100 w |
| 9 | **Producer-named (potentially defamatory)** | Names a specific producer | Take-seriously language; do not engage specifics in public; escalate internally; consider ToS report if defamatory | 50–80 w |
| 10 | **Allegation of discrimination** | Cites discrimination, redlining, protected class | Compliance-counsel-pre-approved; neutral acknowledgement; reference to non-discrimination posture; route to compliance | 60–100 w |
| 11 | **Allegation of "AI denied my claim"** | Cites AI, algorithm, automated denial | NAIC AI Model Bulletin / IN HB 1271 posture; affirms human-in-the-loop on adverse decisions; invites private review | 70–110 w |
| 12 | **Glassdoor / Indeed employer review** | Employer-side review | Thank for feedback; address theme (comp / management / growth) in general; invite continued dialogue via HR; never respond to a specific employee | 70–110 w |
| 13 | **NextDoor / Reddit thread mention** | Community-platform mention | Conversational, on-voice, brief; "happy to help if you'd like to DM" rather than a corporate-shaped reply; defer to community moderators | 40–80 w |
| 14 | **Medicare.gov / Healthgrades** | Medicare plan-finder or A&H rating | CMS-marketing-rule-aware; no member identification; redirect to member services; include the CMS / state non-discrimination tagline if required | 60–100 w |
| 15 | **DOI public-portal summary** | State-DOI public-complaint portal | Formal, factual, chronology-based; no argument; confirm the carrier's cooperation with the Department; route to compliance counsel before posting | 80–140 w |
| 16 | **NAIC market-conduct portal** | NAIC-portal regulator-facing post | Regulator-facing only; route to compliance counsel; not posted publicly; produced as the formal-response packet (see step 9) | 100–180 w |
| 17 | **Suspected fake / competitor review** | Defamatory, off-pattern, or anomalous | Brief, on-voice rebuttal that does not engage specifics; parallel ToS takedown request | 30–60 w |

If the review type is not in the library, fall back to **Template 3 (3★ neutral / mixed)** and note the deviation in the Handler Note.

#### 4. Draft Structure (Public Response)

- **Open** — Greeting or brief acknowledgment that is not tied to a specific customer or claim. Use the reviewer's first name only if the platform displays a real first name (not a handle); on platforms with handles only (Reddit, NextDoor, Trustpilot anonymous), greet without a name
- **Bridge** — One neutral sentence that shows the agency heard the sentiment without confirming facts
- **Redirect** — Private channel with a named contact method from `config.yml.agency.signer_block` for the resolved sender role (e.g., "Please reach our Customer Care team at [phone] or [email] so we can make this right"); on DOI / NAIC posts, the redirect is the carrier's complaint-coordinator contact
- **Close** — Brand-appropriate sign-off; signer block from `config.yml.agency.signer_block` for the resolved sender role; avoid over-apology

#### 5. Recipient-Class Signer Block

Pull from `config.yml.agency.signer_block` per the resolved sender role:

- **Owner / Agency Principal** — Name, "Owner", agency name, agency phone (no producer license number on platform replies unless `agency.review_response_house_rules.show_license_on_public = true`)
- **Agency Manager** — Name, "Agency Manager", agency name, agency phone
- **Customer Care Lead** — Name, "Customer Care", agency name, customer-care direct line / email
- **Compliance Officer** — Name, "Compliance", agency name, compliance-team contact (used on DOI portal replies and the formal-response packet)
- **HR Lead** — Name, "Human Resources", agency name, HR-direct contact (used on Glassdoor / Indeed)
- **Producer** — Name, "Licensed Producer", agency name, producer license number per state (used only when the review specifically names that producer and the producer has cleared the response with compliance)

If the resolved role is not in the agency's signer block, fall back to `Customer Care` and flag in the Handler Note.

#### 6. Tone Calibration by Recipient Class

- **5★ / 4★ to a happy customer** — Warm, specific, briefly echoes one detail from the review without confirming customer status; never asks for an updated review on platforms that ban the ask (Yelp, BBB)
- **3★ / 2★ / 1★ to a dissatisfied customer** — Empathetic, non-defensive; one short bridge sentence; private channel
- **1★ with regulatory language** — Compliance-counsel-pre-approved; no admissions; DOI-resolution invitation only if the agency's house rules allow it
- **Glassdoor / Indeed employer** — Thank-for-feedback voice; HR contact; never identifies anyone
- **NextDoor / Reddit community** — Conversational, on-voice, brief; reads like a neighbor, not a brand
- **Medicare.gov / Healthgrades** — CMS-marketing-rule-aware; never identifies a member
- **DOI portal** — Formal, factual, chronology-based; routed through compliance counsel
- **NAIC market-conduct portal** — Regulator-facing; never posted publicly

#### 7. Parallel Private Outreach (Where Warranted)

For escalation classes `PUBLIC + PRIVATE` and any 1★ / 2★, draft a paired **private outreach** message (DM, email, phone script) that is allowed to reference the specific facts once identity is verified, offers a resolution path consistent with the agency's authority, and never commits coverage or payment without proper authority. The private outreach uses the **Email Drafter** structure (subject + body + sign-off + signature block) and is rendered in the language of the original review where `agency.languages_supported` covers it. If the call would be recorded and the reviewer is in a two-party-consent state (CA / FL / IL / MD / MA / MT / NH / PA / WA), include the recording-disclosure line in the private outreach script.

#### 8. Distribution-Scope-Aware Redaction

Render the response in **parallel scoped versions** based on the requested `distribution scope`:

- **`PUBLIC`** — On-platform reply. Most-redacted: no PII, no internal facts, no coverage discussion, no specific staff names beyond the signer block. Length per template
- **`OWNER-ONLY`** — Handler / owner copy. Includes the public reply, the private-outreach draft, the moderation note, and the reviewer-intake brief. Internal facts referenced; reasoning preserved
- **`COMPLIANCE-COUNSEL-REVIEW`** — Counsel-hold copy. Adds the regulatory-flags table (state law triggered, defamation analysis, DOI-status check, AI-disclosure status), the privilege-and-work-product header, and a counsel-routing recommendation. Privilege footer included
- **`DOI-FORMAL-RESPONSE`** — Formal-response packet for the DOI complaint analyst. Adds a chronology, a citations block (policy form, endorsement, claim file note, communication log), the carrier's compliance-row schema reference (from the Compliance Checklist Generator), and the carrier complaint-coordinator signer block. Routed through counsel before submission

The skill produces every scope the user requested; if no scope is specified, the default bundle is `PUBLIC + OWNER-ONLY`.

#### 9. Multi-Language Variant (When Applicable)

If `config.yml.agency.languages_supported` includes more than one language and the reviewer wrote in a non-`en` language (or the platform is consumer-facing in a multilingual market), produce a parallel translated variant flagged `LANGUAGE: <code>`. Common targets: Spanish (`es`), Vietnamese (`vi`), Haitian Creole (`ht-creole`), Simplified Chinese (`zh`), Tagalog (`tl`), Russian (`ru`), Korean (`ko`). Translation respects insurance-term conventions; idioms are not translated literally; any state-required disclosure is rendered in the target language using the regulator-approved phrasing where one exists. For Medicare-related responses, use the CMS-approved translation of the non-discrimination tagline.

#### 10. AMS / CRM Activity-Log Handoff (Private-Outreach Side)

For any review that triggers a `PUBLIC + PRIVATE` response, append a short **AMS Activity-Log Block** after the private-outreach draft:

```
ACTIVITY-LOG: [Account or Reviewer-handle] · [Platform] · [Tier] · [Driver] · [Public reply posted Y/N] · [Private outreach planned] · [Owner]
```

Format adapts to `config.yml.agency.ams`:

- Applied Epic: `Activity Type | Description | Follow-up Date | Owner`
- AMS360: `Suspense | Description | Due | Assigned`
- HawkSoft: `Tag | Memo | Suspense | Owner`
- Vertafore: `Note Type | Detail | Diary | Producer`
- Salesforce: `Activity Subject | Comments | Due Date | Owner`

#### 11. Compliance and Quality Checklist — Verify Before Delivering

- No confirmation or denial of customer / claim / beneficiary / employee status
- No PII from the review reproduced in the public reply
- No coverage, reserve, settlement, or fault statements
- Tone matches the tier and the platform; no banned phrases ("We're sorry you feel that way", "Please reach out to discuss your concerns", "We strive for the highest service" — corporate-defensive)
- Public response is within the platform's best-practice length
- Private channel named and working; signer block matches the resolved sender role
- AI-generated-content disclosure where required (TX, CA, others as `agency.licensed_states` indicates)
- License footer where required by state and `agency.review_response_house_rules`
- Privilege footer on the `COMPLIANCE-COUNSEL-REVIEW` and `DOI-FORMAL-RESPONSE` scopes only — never on the public reply
- If 1★ with regulatory language, allegation of discrimination, allegation of bad-faith, or DOI / NAIC reference: flag for compliance / legal pre-approval before posting
- Platform-specific hygiene checked (no "please update your review" asks on platforms that ban that; no employee identification on Glassdoor / Indeed; no member identification on Medicare.gov / Healthgrades; CMS-marketing-rule check on Medicare; BBB-code reference on BBB; Reddit and NextDoor written in community voice, not corporate)
- Multi-language variant produced if `agency.languages_supported` covers the reviewer's language

#### 12. Return the Bundle

Return the bundle:

1. **Handler Note** at the top — escalation class, distribution scope(s) requested, regulatory flags, signer block resolved, AI-disclosure status, language variant produced, any `[TO CONFIRM]` items
2. **Public response** — One per requested scope; the `PUBLIC` version is always the most-redacted
3. **Private outreach draft** — Email / DM / phone script; AMS activity-log block appended below
4. **Moderation Note** — Platform ToS takedown rationale for fake reviews, defamation, PII disclosures, or other ToS violations — handler-only, not posted
5. **Reviewer Intake Brief** — A short briefing the handler can use on the private call (talking-points, what is and isn't safe to confirm, what authority the handler has)
6. **Compliance-Counsel-Review packet** (only when the scope is requested) — Regulatory-flags table, privilege header, counsel-routing recommendation
7. **DOI-Formal-Response packet** (only when the scope is requested) — Chronology, citations block, carrier compliance-row reference, complaint-coordinator signer block

### Output Requirements

- Public response sized to the platform and the tier, with no PII, no coverage discussion, no admissions
- Distribution-scoped variants produced for every requested scope (default `PUBLIC + OWNER-ONLY`)
- Private outreach draft (when the tier or escalation class warrants it) that is allowed to go deeper once identity is verified
- Tone matches `config.yml` → `voice`; signer block from `config.yml.agency.signer_block` matches the resolved sender role
- Required AI-generated-content disclosure included where applicable; license footer included where required
- Multi-language variant produced when the reviewer wrote in a non-`en` language and `agency.languages_supported` covers it
- AMS activity-log block in the user's AMS format (Applied Epic / AMS360 / HawkSoft / Vertafore / Salesforce)
- Moderation Note with a platform ToS takedown rationale for fake reviews, defamation, or PII disclosures — handler-only, not posted
- Handler Note at the top flagging escalation class, scopes, signer block, regulatory flags, and any `[TO CONFIRM]` items
- Compliance-counsel-review and DOI-formal-response packets produced when the corresponding scope is requested
- Saved to `outputs/` if the user confirms

## Cross-References

- `_shared/email-drafter.md` — used to draft the private-outreach email when a review triggers `PUBLIC + PRIVATE`
- `_shared/meeting-summarizer.md` — used to recap a private-outreach call after the fact, with the same distribution-scope-aware redaction
- `admin/compliance-checklist-generator.md` — the master compliance row schema referenced by the DOI-formal-response packet
- `admin/ai-governance-model-card-generator.md` — referenced when a review alleges "AI denied my claim" (the model card supports the human-in-the-loop attestation)
- `customer-service/coverage-explanation-letter.md` — the next-up artifact when a private-outreach call resolves to a written coverage position
- `operations/cat-claim-surge-triage.md` — referenced when a 1★ surge on the same loss event indicates a CAT-driven service complaint pattern

## Anti-Patterns

- Posting a corporate-defensive 1★ reply ("We're sorry you feel that way") instead of acknowledging the friction and naming a real human contact
- Confirming or denying the reviewer's customer, claim, or beneficiary status in public
- Asking a reviewer to update or remove their review on a platform that bans the ask (Yelp, BBB)
- Identifying a specific employee in a Glassdoor / Indeed reply
- Engaging the merits of a coverage dispute or claim in public — every coverage discussion belongs in the private channel
- Posting a DOI / NAIC formal response without compliance-counsel pre-approval
- Using AI content disclosure as a substitute for human review of the reply
- Translating a regulator-required disclosure literally instead of using the regulator-approved translation

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]

## Versioning

- v3.0 (this version) — Per-role signer block pulled from `config.yml.agency.signer_block` (owner / agency-manager / customer-care-lead / compliance-officer / hr-lead / producer); expanded platform library from 7 to 16 (added Apple Maps, Trustpilot, Indeed, NextDoor, Reddit, Healthgrades, Medicare.gov plan-finder, NAIC market-conduct portal); expanded template library from 10 to 17 (added 1★ with DOI/bad-faith, allegation-of-discrimination, "AI denied my claim", NextDoor/Reddit, Medicare.gov/Healthgrades, NAIC market-conduct, fake/competitor); distribution-scope-aware parallel rendering (`PUBLIC` / `OWNER-ONLY` / `COMPLIANCE-COUNSEL-REVIEW` / `DOI-FORMAL-RESPONSE`) with per-scope redaction rules; multi-language variant gated on `config.yml.agency.languages_supported` (en / es / vi / ht-creole / zh / tl / ru / ko) with insurance-term translation rules and CMS-approved Medicare non-discrimination tagline; AMS activity-log handoff in the user's AMS format (Applied Epic / AMS360 / HawkSoft / Vertafore / Salesforce); banned-phrase enforcement (corporate-defensive replies stripped); expanded compliance scope (TRAIGA, AB 489, IN HB 1271, CO SB 21-169, NAIC AI Model Bulletin, NAIC UCSPA, state two-party-consent for the recorded private call, CMS marketing rules for Medicare); cross-references to email-drafter, meeting-summarizer, compliance-checklist-generator, ai-governance-model-card-generator, coverage-explanation-letter, cat-claim-surge-triage; explicit anti-patterns section
- v2.0 — Star-tier classification (5★/4★/3★/2★/1★), sentiment driver, risk flags, escalation class, public-response rules, 10 templates by tier and driver, parallel private outreach, compliance/quality checklist, output bundle (public response, private outreach, moderation note, reviewer intake brief). (Now superseded — every v2.0 capability is preserved in v3.0.)
