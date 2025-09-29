
# 2) Change-Point-First RCA Agent for Microservices

Modern microservice systems generate **anomaly storms**: one real fault triggers dozens of alerts across downstream services. Teams spend precious minutes chasing **symptoms** instead of the **first cause**, inflating **MTTR** and burning **error budgets**.  
**This project** is an always-on, vendor-agnostic **AI observability agent** that detects the **earliest change** in system behavior, maps how it **propagates** through services, and delivers an **evidence-backed explanation** and **fix suggestions** — in real time.

---

## 🚩 Problem We’re Solving

- **Slow root-cause analysis (RCA)** in large microservice graphs.  
- **Alert fatigue** (duplication, flapping, symptom noise) hides the true origin.  
- **Static or opaque topologies** make it hard to see how faults spread after a deploy, schema migration, or feature-flag change.

**Consequence:** Engineers lose time pivoting between metrics, logs, and traces; incidents last longer than they should.

---

## ✅ How We Solve It (High Level)

### 1) Change-Point-First Detection
The agent continuously watches **golden KPIs** (latency, errors, saturation) and flags **where the first statistically significant shift occurred**.

### 2) Live Causal/Service Graph
From **distributed traces** and **change events** (deploys/flags/migrations), it maintains a live **“who-calls-whom”** graph with current dependencies.

### 3) Propagation Reasoning
It verifies that **downstream services** show issues **after** the first change, constructing a **cause → symptom** path.

### 4) Explainable Incident Card (Real Time)
Posts **one card** (Slack/PagerDuty) with:
- **Ranked suspects + confidence**
- **Timestamps**
- **Affected endpoints / SLO burn**
- **Related logs/traces**
- **What changed**
- **Recommended first fixes** (e.g., rollback last migration)

### 5) On-Demand Investigations
You can ask:  
> “analyze checkout timeouts last 30m”  
…and the agent reproduces the same **evidence-backed RCA**.

---

## 🧪 Toy Example

```

10:06  inventory-db  write latency spikes   (earliest change)
10:07  inventory-svc slows
10:08  cart-api      times out
10:09  checkout      fails

```

**Agent card:**  
> *Earliest change at **inventory-db (10:06)** → propagated to **inventory-svc → cart-api → checkout**. Likely tied to migration **idx-2025-09-29**. Try **rollback** / **rebuild index**.*

---

## 📦 Project Goals (for this repo)

- Implement continuous **change-point detection** on golden KPIs.  
- Build and refresh a **live service graph** from traces + change events.  
- Correlate **earliest change** with **downstream symptoms** to propose a **propagation path**.  
- Emit an **explainable incident card** with evidence and suggested first actions.  
- Provide **CLI/Chat** entry points for **on-demand** investigations.

> This README describes the project concept and goals. See the `/docs` and `/examples` folders (to be added) for setup, datasets, and demos.

---

## 🛠️ High-Level Architecture (Planned)

```

[OTel Metrics/Logs/Traces]  [Change Events: deploys/flags/migrations]
\                    /
\                  /
[Ingest & Normalize]  <-------------------------------
|                                         |
[Service Graph Builder]  ←——— traces ————→  (keeps live map)
|
[Change-Point Detector]  (online, golden KPIs)
|
[Propagation & RCA Ranking]
|
[Incident Card Generator]
|
Slack / PagerDuty / Webhook API

```

---

## 📈 Why This Approach

Most tools group alerts or rely on static maps, which can elevate the **loudest symptom**.  
This agent **leads with time** (the **first** change) and validates a **propagation path** over a **live** dependency graph — producing faster, more trustworthy localization.

---


