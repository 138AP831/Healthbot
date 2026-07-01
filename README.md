# Riverside Family Clinic — Healthcare Intake Chatbot

A no-code AI chatbot for patient intake, FAQ support, and staff escalation — built with Zapier Chatbots and Zaps, with explicit safety guardrails against giving medical advice.


> Demo project. Riverside Family Clinic is fictional. All data in this repo (patient records, contact info, appointment details) is synthetically generated for testing — no real patients, no real PHI.

## Overview

This project simulates a real-world use case: a small clinic wants to reduce front-desk load by letting an AI chatbot handle routine intake, answer common questions, and route anything sensitive to a human — without ever giving medical advice. It's built entirely on Zapier's no-code platform to demonstrate an end-to-end automation pipeline (conversational AI → structured data capture → downstream workflow automation) without a custom backend.

## Live demo

Try it here: https://rivahealthbot.zapier.app/

Suggested things to try:
- Click "Book Appointment" and walk through the intake flow
- Click "Ask a Question" and ask about clinic hours or insurance
- Ask something symptom-related (e.g. "I have a headache, what should I do?") to see the safety escalation in action

## Features

- Three-intent quick-reply flow — Book Appointment / Ask a Question / Talk to Staff, shown immediately after greeting
- Knowledge-base-grounded FAQ answers — hours, insurance, billing, appointment policy, pulled from an uploaded FAQ doc rather than the model's general knowledge
- Structured intake capture — name, contact method, reason for visit, preferred date/time, insurance provider, captured via Zapier's Collect Leads logic (not free-text parsing)
- One-click automation trigger — a "Confirm Booking" button fires a Zap carrying the full conversation transcript and metadata to downstream apps
- Hard safety boundary — the bot is explicitly instructed to never diagnose, recommend treatment, or interpret results; any symptom or medical-advice language triggers an immediate escalation message and flags the conversation for staff review



## Architecture

```
User message
    │
    ▼
Greeting + quick-reply buttons  (Suggestions logic)
    │
    ├── Book Appointment ──▶ Collect Leads
    │                        (name, reason, contact, date/time, insurance)
    │                              │
    │                              ▼
    │                     "Confirm Booking" button
    │                              │
    │                              ▼
    │                     Zap button trigger ──▶ Zap actions
    │                                            (Google Sheets / Calendar / Slack)
    │
    ├── Ask a Question ──▶ Knowledge base lookup (FAQ doc)
    │                              │
    │                              └── low confidence / medical question
    │                                          │
    │                                          ▼
    │                                 Escalation message + staff flag
    │
    └── Talk to Staff ──▶ Immediate escalation, staff notified directly
```

## Tech stack

- Conversational AI: Zapier Chatbots (https://zapier.com/ai/chatbot)
- Logic / branching: Zapier Chatbots Logic tab (Suggestions, Collect Leads, Zap button)
- Automation: Zaps (Zap button trigger to Sheets / Calendar / Slack actions)
- Data storage (leads): Zapier Tables
- Knowledge base: Uploaded FAQ document (PDF/Markdown)

## Setup / how to reproduce

1. Create a free Zapier Chatbots account (https://zapier.com/app/chatbots).
2. Create a new chatbot and paste the contents of docs/chatbot_directive.md into the Directive field.
3. Upload docs/faq_knowledge_base.pdf as a Knowledge Source.
4. In the Logic tab, add:
   - A Suggestions block with three starter prompts: Book Appointment / Ask a Question / Talk to Staff
   - A Collect Leads block for intake fields (name, contact, reason for visit, preferred date/time, insurance)
   - A Zap button block labeled "Confirm Booking," linked to a new Zap
5. In the generated Zap, add action steps for wherever you want intake data to land (Google Sheets row, Calendar event, Slack alert).
6. Test using the chatbot's public share link (https://rivahealthbot.zapier.app/) — not the in-builder preview, since button-click triggers don't register from the preview pane.
7. Import data/synthetic_patient_intake.xlsx into your Sheet/Table to simulate historical data if you want to demo reporting on top of it.

## Design decisions

- Scope boundary as a feature, not a limitation. The bot explicitly refuses to diagnose, prescribe, or interpret results. This was a deliberate safety decision baked into the directive, not an accidental omission — and it's arguably the most important design choice in the whole project.
- Structured data over free text. Using Collect Leads instead of parsing free-form chat messages keeps the downstream Zap reliable, since it receives clean structured fields instead of needing to extract them from conversational text with regex or another AI step.
- Synthetic data throughout. All test/demo data was generated programmatically rather than using real patient information, keeping the project safe to share publicly.


## Status & roadmap

- [x] Greeting + 3-intent quick-reply flow
- [x] FAQ knowledge base with escalation rule
- [x] Collect Leads intake flow
- [x] Zap button wired to a Zap trigger
- [x] Deployed to a live shareable link
- [ ] Separate Collect Leads fields per intent (Book Appointment vs. Talk to Staff)
- [ ] Connect Zap actions to a live Google Sheet + Slack channel
- [ ] Add inline Calendly scheduling as an alternative booking path

## Disclaimer

This is a portfolio/demo project only. It is not affiliated with any real clinic, does not handle real patient data, and is not a substitute for a compliant healthcare intake system. Anyone adapting this for a real clinical setting should independently review HIPAA and other applicable regulatory requirements before handling real patient information.
