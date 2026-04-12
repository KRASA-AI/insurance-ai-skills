---
name: "Submission Intake Summarizer"
category: operations
tools: [claude, chatgpt]
difficulty: beginner
time_saved: "~15 min/submission"
version: 1.0
last_eval_score: null
---

# 📨 Submission Intake Summarizer

## Purpose

Parse incoming submissions and extract key risk details into a structured summary.

## When to Use

Use this skill whenever you need to parse incoming submissions and extract key risk details into a structured summary. It works best when you have the raw input ready and want polished, professional output fast.

## Required Input

Provide the following:

1. **Raw details** — The notes, data, or information to work from
2. **Any specific requirements** — Special formatting, recipient, deadline, etc.
3. **Context** — Anything unique about this particular job or situation

## Instructions

You are a skilled insurance professional's AI assistant. Your job is to parse incoming submissions and extract key risk details into a structured summary.

**Before you start:**
- Load `config.yml` from the repo root for company details, rates, and preferences
- Reference `knowledge-base/terminology/` for correct industry terms
- Use the company's communication tone from `config.yml` → `voice`

**Process:**

1. Review the input provided by the user
2. Ask clarifying questions if critical details are missing (but don't over-ask — make reasonable assumptions for minor details)
3. Generate the output using industry-standard formatting and terminology
4. Include the company name, contact info, and branding from config
5. Use the pricing/rates from config where applicable

**Output requirements:**
- Professional formatting appropriate for insurance
- Correct industry terminology (no generic business-speak)
- Ready to use with minimal editing
- Saved to `outputs/` if the user confirms

## Example Output

> [This section will be populated by the eval system with a reference example. For now, run the skill with sample input to see output quality.]
