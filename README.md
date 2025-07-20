# MVP-1-Stateful-AI-Assistant-Instant-RCA-

**In short, the Stateful AI Assistant is the always‑present central service of Instant RCA: it remembers everything about an incident thread, orchestrates reasoning and evidence gathering, grows smarter with each accepted fix, and stays active until the developer confirms the solution is correct.**

---

## MVP 1 — Stateful AI Assistant (Instant RCA)

A Stateful AI Assistant is the central conversational service that:

1. **Creates a dedicated working context for each incident** (keyed by `incident_id`) and stores it in a live scratch‑pad, preserving every chat turn, clarifying question, planner thought, and confidence score until the incident is resolved or escalated.
2. **Runs a multi‑step reasoning loop** — **Fetch → RAG → Reason → Generate → Critique → Present**

   * **RAG** uses a **Sentence‑Transformer + Chroma** vector store to surface the most relevant past RCAs and fixes.
   * **Critique** is performed by an independent fast‑check model (**LLM – TACHYON**) that validates structure and logic before results reach the user.
3. **Decides when to ask for additional evidence**, then incorporates that input into the next reasoning cycle.
4. **Streams responses to the Web UI or IDE in real time**, token‑by‑token, while logging every step to the scratch‑pad.
5. **Persists new knowledge** by writing every accepted root cause and fix into the Chroma vector store, so future incidents benefit from proven solutions.
6. **Enforces guardrails** — automatic PII scrubbing (Presidio), role‑based access control, and confidence thresholds — before invoking LLMs or triggering any automated action.
7. **Connects IDE clients via an MCP gateway** — VS Code and IntelliJ plug‑ins send their requests through a lightweight MCP server, which forwards them to the assistant, while the Web UI calls the assistant API directly.

### Core Purpose

* **Retrieve** relevant past RCAs, fixes, FAQs, and engineering docs via its Sentence‑Transformer + Chroma RAG layer.
* **Iterate** with the engineer—asking for missing evidence, reasoning over new data, and producing high‑confidence RCA + fix responses—validated by **LLM – TACHYON**—until the user accepts.

---

## MVP 1 — Two‑Phase Roll‑Out Plan

### Phase 1: Web UI Launch

* **Target users:** L1/L2 incident engineers and SREs.
* **Action:** Deploy the Web UI connected to the Stateful AI Assistant and start using it during real incidents.
* **Measure:** Track the number of RCAs the engineers accept and monitor the similarity score; goal is ≥ 0.8 over a few months.
* **Outcome:** Collect qualitative feedback and quantitative metrics for management review.

### Decision Gate

* Present the accepted‑RCA count and similarity‑score results to management.
* If the targets are met, proceed to Phase 2.

### Phase 2: IDE Integration

* **Target users:** Developers working in VS Code or IntelliJ.
* **Action:** Enable IDE plug‑ins via the MCP gateway so developers can interact directly with the assistant when opening or updating tickets.
* **Post‑rollout evaluation:** Monitor adoption rates and ticket quality improvements.
