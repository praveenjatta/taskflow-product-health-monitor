# TaskFlow Product Health Monitor

A multi-agent voice AI system built in ElevenLabs that lets PMs and TPMs query product health data across user feedback, support tickets, and product metrics — by simply asking questions out loud.

---

## The Problem It Solves

**TaskFlow** is a project management SaaS with 9,764 paying customers and $14.88M ARR experiencing growing pains:

- Support ticket volume up 25% month-over-month
- NPS dropped from 45 to 38
- Mobile app usage down 15%
- Free-to-paid conversion rate declined from 11.2% to 8.4%

Answering questions like *"What are the top complaints this month?"* or *"Which enterprise accounts are at risk of churning?"* previously required opening three separate dashboards, exporting data, and cross-referencing manually — 30–60 minutes every time.

**This agent eliminates that.** Ask the question out loud. Get the answer in seconds.

---

## Example Queries

- *"What are users complaining about the most?"*
- *"Which enterprise accounts are at risk of churning?"*
- *"What's happening with our conversion rate?"*
- *"What does NPS data say about mobile performance?"*
- *"Walk me through the free-to-paid conversion funnel."*

---

## Architecture

**5 nodes · 14 edges · No code**

```
Start
  └── Greeter (routes intent only — no KB, no hallucination)
        ├── User Feedback Analyst
        ├── Support Ticket Analyst
        └── Product Metrics Analyst
                    └── End
```

**Routing:**
- 3 forward edges (Greeter → each specialist)
- 3 end edges (each specialist → End)
- 6 cross-routing edges (all specialists ↔ bidirectional)
- 1 Start → Greeter edge
- **Total: 14 edges**

---

## Specialist Agents

| Agent | Knowledge Base | Scope | Tone |
|---|---|---|---|
| User Feedback Analyst | KB_User_Feedback.txt | NPS scores and trends, detractor themes, app store reviews, user interview insights, in-app feedback | Empathetic — acknowledges user frustration before analysis |
| Support Ticket Analyst | KB_Support_Tickets.txt | Ticket volume trends, category breakdowns, churn risk accounts, CSAT scores, escalation rates | Crisp and data-driven — operational decisions need facts fast |
| Product Metrics Analyst | KB_Product_Metrics.txt | Conversion funnel, mobile DAU/MAU, retention cohorts, feature adoption, performance benchmarks | Precise and analytical — exact numbers, no vague language |

**Greeter:** Routes intent only. No KB assigned. Never answers questions directly.

---

## Knowledge Base

Three domain-scoped knowledge base files with realistic synthetic data:

- **KB_User_Feedback.txt** — NPS survey results (score 38, down from 45), detractor themes, iOS/Android app store reviews, 5 user interview transcripts, in-app feedback submissions with vote counts
- **KB_Support_Tickets.txt** — 6-month ticket volume trend (25% MoM spike), category breakdown, top 10 ticket topics, 6 enterprise churn-risk accounts with names and open ticket counts, CSAT by agent, escalation rate trend
- **KB_Product_Metrics.txt** — Mobile DAU/MAU with 8-week trend, free-to-paid conversion funnel (8.4% vs 11.2% baseline), 6-month retention cohorts, feature adoption for top 10 features, API latency benchmarks, WAU trend

**KB scoping is strict** — each specialist only has access to its own file. No specialist sees all three.

---

## Key Design Decisions

**Greeter as dispatcher, not concierge** — No KB on the Greeter. Routes on intent keywords only. Prevents hallucination at the entry point.

**Strict KB scoping** — Each specialist sees exactly one file. Prevents information leakage between domains and keeps guardrails clean.

**Cross-routing on all 6 edges** — Users can switch from asking about NPS to asking about conversion funnels without restarting the conversation. Every specialist hands off to every other.

**Distinct specialist tones** — User Feedback Analyst is empathetic (users are people, not data points). Support Ticket Analyst is crisp (operational urgency). Product Metrics Analyst is precise (no hedging on numbers).

**Guardrails in every specialist** — Out-of-scope questions get a polite redirect: *"That's outside my area — I focus on user feedback. For ticket data, ask about the support track."*

---

## Stack

- **Platform:** ElevenLabs Conversational AI
- **Model:** Flash (low latency, voice-optimized)
- **Architecture pattern:** Multi-agent orchestration with LLM-based intent routing
- **Knowledge base:** Plain text files, domain-scoped per specialist
- **Data:** Realistic synthetic data generated for TaskFlow (fictional SaaS product)

---

## Files in This Repo

```
├── README.md
├── Build_Steps.md                  # Step-by-step build instructions with all prompts
├── KB_User_Feedback.txt            # NPS, reviews, interviews, in-app feedback
├── KB_Support_Tickets.txt          # Zendesk analytics, churn risk accounts, CSAT
└── KB_Product_Metrics.txt          # DAU/MAU, conversion funnel, retention, benchmarks
```

---

## Business Impact

| Metric | Before | After |
|---|---|---|
| Time to answer product health question | 30–60 min (manual) | ~30 seconds (voice query) |
| Dashboards required | 3 separate tools | 1 voice interface |
| Churn risk visibility | Weekly manual review | On-demand voice query |
| Cross-domain insight | Manual data joining | Automatic cross-routing |

---

## Reusable Pattern

The multi-agent orchestration pattern used here — Greeter routing to domain specialists with scoped KB files and cross-routing edges — applies to any data-heavy operational context. Swap in your own KB files for any three data domains and the architecture works identically.

**Related projects:**
- [project-nightingale-launch-center](https://github.com/praveenjatta/project-nightingale-launch-center) — Same architecture applied to product launch operations (Technical Readiness, GTM & Marketing, Executive Briefing)
