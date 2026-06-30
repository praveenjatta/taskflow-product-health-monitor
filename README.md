# 📊 TaskFlow Product Health Monitor

A multi-agent voice AI system built with ElevenLabs. PMs and TPMs query product health data — user feedback, support tickets, and product metrics — by simply asking questions out loud. No dashboards, no manual cross-referencing.

---

## 🧩 The Problem It Solves

TaskFlow is a project management SaaS with 9,764 paying customers and $14.88M ARR experiencing growing pains:

- Support ticket volume up 25% month-over-month
- NPS dropped from 45 to 38
- Mobile app usage down 15%
- Free-to-paid conversion rate declined from 11.2% to 8.4%

Answering "What are the top complaints this month?" or "Which enterprise accounts are at risk of churning?" used to mean opening three dashboards and 30–60 minutes of manual work. This agent eliminates that.

---

## 🧠 Workflow Overview

**5-node multi-agent architecture:**

```
Start
    ↓
Greeter Agent              → Identifies intent, routes only (no KB, no answers)
    ↓
User Feedback Analyst      → NPS, app reviews, interviews, in-app feedback
Support Ticket Analyst     → Ticket volume, churn risk accounts, CSAT
Product Metrics Analyst    → Conversion funnel, retention, DAU/MAU, benchmarks
    ↓
End
```

🔁 6 cross-routing edges connect all three specialists bidirectionally — ask about NPS, then pivot to conversion rate, without restarting.

---

## 🛠️ Tech Stack

| Tool | Purpose |
|------|---------|
| ElevenLabs | Conversational voice AI platform |
| Flash Model | Low-latency LLM tuned for voice |
| Knowledge Base (scoped) | Domain-specific grounding per specialist |
| LLM Edge Conditions | Intent-based routing logic |

---

## 📁 Files

| File | Description |
|------|-------------|
| `Build_Steps.md` | Step-by-step build instructions, system prompts, KB generation prompts |
| `KB_User_Feedback.txt` | NPS trends, detractor themes, app store reviews, interviews, in-app feedback |
| `KB_Support_Tickets.txt` | Ticket volume, category breakdown, churn risk accounts, CSAT, escalations |
| `KB_Product_Metrics.txt` | Conversion funnel, DAU/MAU, retention cohorts, feature adoption, benchmarks |
| `README.md` | Project documentation |

---

## 🚀 How to Use

1. Clone or download this repo
2. Open [ElevenLabs Agents](https://elevenlabs.io/app/agents)
3. Create a blank agent and follow `Build_Steps.md` step by step
4. Upload each KB `.txt` file to its corresponding specialist node
5. Wire the 14 edges (3 forward, 3 end, 6 cross-routing, 1 start)
6. Test in Preview mode with mic on

---

## 💡 Example Use Case

**Query:** "What are users complaining about the most?"

**Agent routes to:** User Feedback Analyst

**Response:** NPS at 38, down from 45 — top theme is mobile app performance, cited by 41% of detractors, with direct quotes pulled from app store reviews.

**Follow-up:** "Which enterprise accounts are at risk of churning?"

**Agent cross-routes to:** Support Ticket Analyst — names specific accounts, open ticket counts, and risk signals.

---

## 🔑 Key Design Decisions

- **Greeter as dispatcher, not concierge** — No KB on the Greeter. Routes on intent keywords only.
- **Strict KB scoping** — Each specialist sees exactly one file. No specialist has access to all three.
- **Cross-routing on all 6 edges** — Full bidirectional handoff between specialists for natural topic switching.
- **Distinct specialist tones** — Feedback Analyst is empathetic, Ticket Analyst is crisp, Metrics Analyst is precise.
- **Guardrails baked into every prompt** — Out-of-scope questions get redirected, never guessed at.

---

## 📈 Business Impact

| Metric | Before | After |
|--------|--------|-------|
| Time to answer a product health question | 30–60 min (manual) | ~30 seconds (voice query) |
| Dashboards required | 3 separate tools | 1 voice interface |
| Churn risk visibility | Weekly manual review | On-demand voice query |

---

## 👤 Author

**Praveen Kumar Jatta** — Senior Technical Program Manager | AI Automation Consultant

- 🌐 [jattaai.com](https://jattaai.com)
- 💼 [linkedin.com/in/praveenjatta](https://linkedin.com/in/praveenjatta)
- 🐙 [github.com/praveenjatta](https://github.com/praveenjatta)
- 📅 [Book a free discovery call](https://calendly.com/praveenjatta/free-ai-automation-discovery-call)

---

## 📄 License

MIT License — free to use and modify with attribution.
