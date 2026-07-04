# Priya — Multilingual Voice Collections Agent
### Business Write-Up · Sunrise Finance (NBFC) PoC · Powered by Sarvam AI

*Prepared as for a client CTO / VP Operations. Companion to the 8-slide deck (`Priya_Business_Writeup.pdf`).*

---

## 1. The Problem

Collections in India runs on the phone. Indian lenders place an estimated **50M+ collection calls every month**, at a fully-loaded cost of **₹80–100 per human-agent call** (agent, telephony, supervision). Each overdue account needs **3–5 attempts per cycle**. The binding constraint is people: collection floors churn 40%+ annually, language coverage rarely extends beyond metro languages, off-script calls create RBI Fair Practices exposure, and capacity is fixed while due-date volumes spike. *(Figures are industry estimates; all assumptions are stated explicitly in §5.)*

## 2. Why AI — and who the end user is

The borrower is typically a Tier-2/3 salaried or self-employed customer, most comfortable in Hindi, Tamil, or code-mixed speech (Hinglish/Tanglish), often with limited digital literacy. Voice is the interface they already use — no app, no forms, no reading. A voice agent: (a) reaches **every** overdue account on day one, in the borrower's own language; (b) stays **on-script by design** — RBI Fair Practices tone is enforced in the agent's instructions, with no threats and guaranteed human hand-off on request; (c) absorbs due-date surges without headcount; and (d) escalates hardship and disputes to humans, so people handle judgement while AI handles Tier-1 volume.

## 3. Why Sarvam — India's own AI stack

| | Sarvam AI | Generic global stack |
|---|---|---|
| **Language** | 22+ Indian languages; Hinglish & Tanglish code-mixing handled natively (Saarika STT, Bulbul TTS, sarvam-105b) | English-first; code-mixed speech brittle, Indian accents degrade accuracy |
| **Data residency** | Models built and hosted in India — aligned with DPDP Act 2023 and RBI data-localisation expectations | Offshore inference; cross-border transfer review for every borrower conversation |
| **Deployment** | Public API today; on-premise / private-VPC options for regulated BFSI workloads | Cloud-only in most cases |
| **Sovereignty & economics** | India's foundational-model builder under the IndiaAI Mission; rupee-priced | Dollar-priced; roadmap driven by other markets |

Borrower conversations never need to leave the country.

## 4. Architecture summary (business view)

Three layers, built no-code in n8n, every AI step a Sarvam API:

1. **Call setup** — a CRM record drives the call: who we're calling, their registered language, EMI, and opening script. Priya opens in the customer's language (Bulbul TTS).
2. **Conversation** — the customer replies in any language or mix; Saarika transcribes it faithfully; sarvam-105b generates Priya's reply from a six-scenario collections playbook (pay-now, promise-to-pay, hardship, dispute, wrong-person, refusal) inside RBI-compliant guardrails; Bulbul speaks it back.
3. **Agentic post-call** — a second sarvam-105b call classifies the conversation (scenario, sentiment, promise-to-pay date, escalation flag) and **updates the CRM autonomously**. No human logs the call.

Demonstrated live with two calls through the *same* workflow: a Hinglish hardship call (empathy → senior-officer escalation flagged) and a Tanglish promise-to-pay call (commitment date **15-Jul-2026** extracted from speech, payment link sent). Only the CRM record changed between calls.

*PoC simulation boundaries:* telephony simulated with recorded clips (Plivo/LiveKit in production); dialer queue simulated by manual record entry; Google Sheets stands in for the CRM.

## 5. ROI / Business case

**Assumptions (mid-size NBFC):** 2,00,000 overdue accounts/month × 3 attempts = **6,00,000 calls/month** · human agent ₹80/call fully loaded · Priya ≈ **₹6/call** blended Sarvam STT + LLM + TTS at ~2-minute calls · **70% of Tier-1 calls automated**; hardship and disputes stay human.

| | Monthly cost |
|---|---|
| Human-only floor (6,00,000 × ₹80) | **₹4.8 Cr** |
| With Priya (70% automated at ₹6; 30% human at ₹80) | **₹1.7 Cr** |
| **Savings** | **≈ ₹3.1 Cr/month (~65%) · ₹37+ Cr annualised** |

This is before counting recovery-rate uplift from reaching every borrower, in their own language, on day one — and before agent-hiring/attrition costs avoided.

## 6. Limitations & next steps

**Honest PoC boundaries:** no live telephony yet; no identity verification before discussing loan details (mandatory for production BFSI); single-exchange calls (no long multi-turn memory); Sheets in place of a real CRM/LMS.

**90-day rollout:**
- **Days 0–30 — Pilot:** live telephony (Plivo / LiveKit + Sarvam streaming), identity verification, one language, one loan product.
- **Days 31–60 — Deepen:** multi-turn memory, CRM/LMS integration, payment-gateway links, three languages, human hand-off desk.
- **Days 61–90 — Scale:** on-prem / private-VPC deployment for full data sovereignty, QA dashboards, RBI-ready audit trails, all-language rollout.

Each step is an integration, not a redesign — the Sarvam pipeline demonstrated in this PoC stays the same.

---
*Abhinav Kumar Mishra · Sarvam AI Pre-Sales Engineer assignment · July 2026*
