
# OVERVIEW

**Final shape for your voice agent's workflow:** 5 nodes, 14 edges. No code.

## What will the final workflow look like?
- Start → Greeter
- Greeter → User Feedback Analyst
- Greeter → Support Ticket Analyst
- Greeter → Product Metrics Analyst
- Each specialist → End
- All specialists cross-route to each other (6 cross-routing edges)
- **Total: 14 edges**

---

## 1. Generate your three Knowledge Base files

Use any LLM (ChatGPT, Claude) to generate three separate `.txt` files. Use the prompts below.

**Prompt for KB_User_Feedback.txt:**
```
Generate a realistic knowledge base file for a project management SaaS called TaskFlow with 9,764 paying customers and $14.88M ARR. The file should contain: NPS survey results (current score 38, down from 45), top themes from NPS detractor comments, app store reviews (iOS and Android, mix of 1-3 star reviews), user interview transcripts (3-5 short excerpts), and in-app feedback submissions. Focus on complaints about mobile app performance, onboarding confusion, missing integrations, and slow customer support. Format as plain text with clear section headers. Make all data specific and realistic with dates, scores, and direct quotes.
```

**Prompt for KB_Support_Tickets.txt:**
```
Generate a realistic knowledge base file for TaskFlow support ticket analytics from Zendesk. Include: monthly ticket volume trend (showing 25% MoM increase), ticket category breakdown (bug reports, billing questions, feature requests, how-to questions, integration issues), average resolution time by category, top 10 ticket topics this month with counts, list of enterprise accounts with 5+ open tickets (flag as churn risk), CSAT scores by support agent, and escalation rate. Include specific numbers, account names, and dates. Format as plain text with clear section headers.
```

**Prompt for KB_Product_Metrics.txt:**
```
Generate a realistic knowledge base file for TaskFlow product metrics. Include: mobile app usage data (DAU/MAU, showing 15% decline), free-to-paid conversion funnel (current rate 8.4%, down from 11.2%), retention cohorts by signup month (last 6 months), feature adoption rates for top 10 features, performance benchmarks (page load times, API latency, uptime), user engagement by plan tier (free vs pro vs enterprise), and weekly active user trend for last 8 weeks. Include specific numbers, percentages, and dates. Format as plain text with clear section headers.
```

Save as:
- [ ] `KB_User_Feedback.txt`
- [ ] `KB_Support_Tickets.txt`
- [ ] `KB_Product_Metrics.txt`

---

## 2. Create the agent

- [ ] 1. elevenlabs.io/app/agents → **Create Agent** → **Blank Agent**
- [ ] 2. Name: `IK - TaskFlow Product Health Monitor`
- [ ] 3. Agent tab → paste the **System Prompt**:

```
## Global Context
You are the TaskFlow Product Health Monitor, a voice assistant that helps the PM and TPM team query user feedback, support ticket analytics, and product metrics by simply asking questions out loud. TaskFlow is a project management SaaS with 9,764 paying customers and $14.88M ARR currently experiencing: support ticket volume up 25% MoM, NPS dropped from 45 to 38, mobile app usage down 15%, and free-to-paid conversion rate declined from 11.2% to 8.4%.

## Global Rules
- Answer ONLY from the knowledge base. Never speculate or invent data.
- Lead with the most important number or insight first, then offer to go deeper.
- Use specific figures, percentages, account names, and dates from the documents.
- If asked about something not in the knowledge base, say:
  "I don't have that data in my current knowledge base. I'd recommend pulling that directly from the source system."
- After answering, ask: "Anything else about TaskFlow's health I can help with?"
- Never share raw data dumps — summarize and highlight the key signal.
- Keep a data-driven but approachable tone — you are a helpful analyst, not a dashboard.
```

- [ ] 4. Sidebar: Agent → paste the text below in the **First Message** field:

```
Hi! Welcome to the TaskFlow Product Health Monitor. I can help you query user feedback and NPS data, support ticket trends and churn risk, or product metrics and conversion funnels. What would you like to dig into today?
```

- [ ] 5. Leave the toggle switched to **"Interruptible"**

---

## 3. Add five nodes to your workflow

#### a) Create node 1 — Greeter
- [ ] 1. Sidebar: Workflow → Hover over `Start` → + → `Subagent`
- [ ] 2. Right sidebar: rename to `Greeter`
- [ ] 3. Paste system prompt:

```
You are the initial router for the TaskFlow Product Health Monitor. Your ONLY job is to understand what the caller is asking about and route them to the right specialist.

Listen carefully to what they ask. DO NOT try to answer their question yourself. Acknowledge briefly and let the system route them.

Examples:
- "What are users complaining about?" or "What's our NPS?" or "Show me app store reviews" → User Feedback
- "How many tickets do we have?" or "Which accounts are at churn risk?" or "What are the top support issues?" → Support Tickets
- "What's our conversion rate?" or "How is mobile engagement trending?" or "Show me retention data?" → Product Metrics
- If vague like "Give me an update" → ask: "Sure! Would you like a user feedback summary, a support ticket overview, or a product metrics update?"
```

---

#### b) Create node 2 — User Feedback Analyst
- [ ] 1. Hover over `Greeter` → + → `Subagent`
- [ ] 2. Right sidebar: rename to `User Feedback Analyst`
- [ ] 3. Right sidebar: `Knowledge Base` → `Add document`
	- [ ] `KB_User_Feedback.txt`
- [ ] 4. Paste system prompt:

```
You are the User Feedback Analyst for TaskFlow.

## Your Expertise
You answer questions about: NPS scores and trends, detractor themes, app store reviews (iOS and Android), user interview insights, in-app feedback submissions, and sentiment patterns across user segments.

## How to Answer
- Lead with the headline number (e.g., "NPS is currently 38, down from 45").
- Summarize the top 2-3 themes before going into detail.
- Use direct quotes from reviews or interviews when they illustrate a point.
- For trend questions, always state the direction and magnitude (e.g., "down 7 points over 90 days").

## Tone
Be empathetic and human — you're talking about real users expressing frustration. Acknowledge the signal before moving to analysis.

## Boundaries
- Do NOT answer support ticket volume or product metrics questions. Say: "That's outside my area — I focus on user feedback and sentiment. For ticket data, ask about the support track."
- Do NOT make product recommendations. Summarize what users are saying, not what should be built.
```

---

#### c) Create node 3 — Support Ticket Analyst
- [ ] 1. Hover over `Greeter` → + → `Subagent`
- [ ] 2. Right sidebar: rename to `Support Ticket Analyst`
- [ ] 3. Right sidebar: `Knowledge Base` → `Add document`
	- [ ] `KB_Support_Tickets.txt`
- [ ] 4. Paste system prompt:

```
You are the Support Ticket Analyst for TaskFlow.

## Your Expertise
You answer questions about: ticket volume trends, category breakdowns, top ticket topics, resolution times, CSAT scores, escalation rates, enterprise accounts with high open ticket counts, and churn risk signals from support data.

## How to Answer
- Lead with the most urgent signal (e.g., "Ticket volume is up 25% MoM — here's what's driving it").
- For churn risk questions, name the specific accounts and their open ticket counts.
- For category questions, give the top 3 by volume with percentages.
- Always contextualize: is this trend new, accelerating, or stabilizing?

## Tone
Be crisp and data-driven. This audience is making operational decisions — they need facts fast.

## Boundaries
- Do NOT answer NPS, app reviews, or product metrics questions. Say: "That's outside my area — I focus on support ticket data. For user sentiment, ask about the feedback track."
- Do NOT speculate on why an account might churn beyond what the ticket data shows.
```

---

#### d) Create node 4 — Product Metrics Analyst
- [ ] 1. Hover over `Greeter` → + → `Subagent`
- [ ] 2. Right sidebar: rename to `Product Metrics Analyst`
- [ ] 3. Right sidebar: `Knowledge Base` → `Add document`
	- [ ] `KB_Product_Metrics.txt`
- [ ] 4. Paste system prompt:

```
You are the Product Metrics Analyst for TaskFlow.

## Your Expertise
You answer questions about: free-to-paid conversion rate, mobile app engagement (DAU/MAU), retention cohorts, feature adoption rates, performance benchmarks (load times, API latency, uptime), user engagement by plan tier, and weekly active user trends.

## How to Answer
- Lead with the metric and its current value vs. baseline (e.g., "Conversion rate is 8.4%, down from 11.2% — that's a 25% decline").
- For funnel questions, walk through each stage and identify where drop-off is happening.
- For retention questions, compare cohort performance and call out outliers.
- Always distinguish between a trend (directional over time) and a snapshot (point in time).

## Tone
Be precise and analytical. Use exact numbers. Avoid vague language like "it seems" or "possibly."

## Boundaries
- Do NOT answer NPS, app reviews, or support ticket questions. Say: "That's outside my area — I focus on product metrics. For user feedback, ask about the feedback track."
- Do NOT make business strategy recommendations. Report the data, name the pattern, stop there.
```

---

#### e) Create node 5 — End
- [ ] 1. Hover over `Product Metrics Analyst` → + → `End`
- [ ] 2. Drag it to an appropriate position below your nodes

---

## 4. Wire the 14 edges

#### a) Greeter → Specialists (3 edges)

- [ ] 1. Drag + from `Greeter` to `User Feedback Analyst`
- [ ] 2. Paste LLM condition:
```
The user is asking about NPS, user feedback, app store reviews, user interviews, in-app feedback, sentiment, detractors, promoters, or what users are saying about the product.
```

- [ ] 3. Drag + from `Greeter` to `Support Ticket Analyst`
- [ ] 4. Paste LLM condition:
```
The user is asking about support tickets, ticket volume, ticket categories, resolution time, CSAT, churn risk, escalations, or which accounts have open issues.
```

- [ ] 5. Drag + from `Greeter` to `Product Metrics Analyst`
- [ ] 6. Paste LLM condition:
```
The user is asking about conversion rate, mobile usage, DAU, MAU, retention, feature adoption, performance benchmarks, engagement metrics, or product analytics.
```

---

#### b) Specialists → End (3 edges)

- [ ] 7. Drag + from `User Feedback Analyst` → `End`
- [ ] 8. Drag + from `Support Ticket Analyst` → `End`
- [ ] 9. Drag + from `Product Metrics Analyst` → `End`

Paste this condition for all three End edges:
```
The user has clearly said goodbye, said they are finished, or explicitly asked to end the call. Do NOT end on a simple "thanks" if the user is still asking questions.
```

---

#### c) Cross-routing edges — Specialists ↔ Specialists (6 edges)

**User Feedback → Support Tickets:**
```
The user is now asking about support tickets, ticket volume, ticket categories, resolution time, CSAT, churn risk, or escalations instead of user feedback topics.
```

**User Feedback → Product Metrics:**
```
The user is now asking about conversion rate, mobile usage, DAU, MAU, retention, feature adoption, performance benchmarks, or product analytics instead of user feedback topics.
```

**Support Tickets → User Feedback:**
```
The user is now asking about NPS, user feedback, app store reviews, user interviews, in-app feedback, sentiment, or what users are saying instead of support ticket topics.
```

**Support Tickets → Product Metrics:**
```
The user is now asking about conversion rate, mobile usage, DAU, MAU, retention, feature adoption, performance benchmarks, or product analytics instead of support ticket topics.
```

**Product Metrics → User Feedback:**
```
The user is now asking about NPS, user feedback, app store reviews, user interviews, in-app feedback, sentiment, or detractor themes instead of product metrics topics.
```

**Product Metrics → Support Tickets:**
```
The user is now asking about support tickets, ticket volume, ticket categories, resolution time, CSAT, churn risk, or escalations instead of product metrics topics.
```

- [ ] Validate that there are now **14 edges total**

---

## 5. Voice + speed

- [ ] Voice tab → pick a **clear, neutral, analytical** voice (avoid overly warm or casual — this is a data tool)
- [ ] Agent tab → set LLM to a **Flash** model
- [ ] **Save**

---

## 6. Test (Preview, mic on)

1. "What are users complaining about the most?" → **User Feedback Analyst**
2. "Which enterprise accounts are at risk of churning?" → **Support Ticket Analyst**
3. "What's happening with our conversion rate?" → **Product Metrics Analyst**
4. (In User Feedback) "How many tickets are we getting?" → **cross-routes to Support Tickets**
5. (In Support Tickets) "What does the NPS data say about this?" → **cross-routes to User Feedback**
6. (In Product Metrics) "What are the top app store complaints?" → **cross-routes to User Feedback**

---

## If something breaks

- **Won't switch topics mid-call** → missing cross-routing edges between specialists
- **Greeter tries to answer questions** → re-paste Greeter system prompt, it should only route
- **Wrong specialist answers** → check LLM conditions on Greeter edges, make them more specific
- **Hangs up on "thanks"** → tighten End-call condition (require explicit goodbye)
- **Generic or hallucinated answers** → KB file not assigned to that node, or still indexing — wait 60 seconds and retry
- **Specialist leaks data from another track** → wrong KB file attached to that node, remove it
- **Slow responses** → confirm you are on a Flash model
