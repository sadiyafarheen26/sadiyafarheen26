# ⚡ Autonomous Incident Triage Agent with Antigravity, MCP, and Cloud Agent Runtime

## 🎯 The Challenge / Scenario
Severe winter storms frequently disrupt retail supply chains, generating severe spikes in customer support volume that overwhelm human response teams. This project implements an enterprise-grade AI triage agent for *Cymbal Direct* capable of securely interfacing with internal logistics systems, determining delayed package statuses, and autonomously issuing tier-based loyalty compensation. Deploying this architecture offloads repetitive triage workflows, cuts resolution times from hours to seconds, and maintains deterministic business rules under high-stress operational conditions.

## 🧰 Tech Stack & Skills Learned
* **AI Orchestration & Runtime:** Google Cloud Agent Runtime (Reasoning Engine), Gemini Models, Antigravity CLI (`agy`)
* **Tool & Data Integration:** Model Context Protocol (MCP), FastMCP, Pydantic
* **Scaffolding & Tooling:** Google Agents CLI (`google-agents-cli`), `uv` (Fast Python Package Manager)
* **Architecture & Paradigms:** Tool-Augmented Generation (TAG), Declarative Agent Skills (`SKILL.md`), Workspace Policy Enforcement (`GEMINI.md`), Microservice-based Tool Providers

---

## 🚀 Key Milestones & Architecture

```
                      ┌─────────────────────────────────────────┐
                      │        End User / Support API           │
                      └────────────────────┬────────────────────┘
                                           │
                                           ▼
┌────────────────────────────────────────────────────────────────────────────────────────┐
│ Google Cloud Agent Runtime / Vertex AI Reasoning Engine                                │
│                                                                                        │
│   ┌────────────────────────────────────────────────────────────────────────────────┐   │
│   │ Autonomous Agent (Gemini Core)                                                 │   │
│   │  • System Governance (GEMINI.md)                                               │   │
│   │  • Standard Operating Procedure: winter-storm-triage (SKILL.md)                │   │
│   └───────────────────────┬────────────────────────────────┬───────────────────────┘   │
│                           │                                │                           │
└───────────────────────────┼────────────────────────────────┼───────────────────────────┘
                            │ (JSON-RPC)                     │ (API Call)
                            ▼                                ▼
              ┌───────────────────────────┐    ┌───────────────────────────┐
              │ Cymbal Logistics MCP Tool │    │ Disruption Compensation   │
              │ • get_order_status        │    │ • issue_disruption_       │
              │ • get_customer_loyalty    │    │   compensation            │
              └───────────────────────────┘    └───────────────────────────┘
```

### 1. Tool Integration via Model Context Protocol (MCP)
* **What was built:** Configured a local Model Context Protocol (MCP) bridge exposing internal logistics functions: `get_order_status`, `get_customer_loyalty_info`, and `issue_disruption_compensation`.
* **How it works:** The agent connects to the FastMCP server through standard JSON-RPC over a declarative `.agents/mcp_config.json` definition, allowing the LLM to dynamically inspect schema definitions and invoke data retrieval tools on demand.
* **Validation:** Verified live order lookup (`CYM-EC-9921`) where the agent correctly identified severe weather shipping delays and fetched real-time package state.

### 2. Policy Enforcement & Declarative Agent Skills
* **What was built:** Defined workspace governance guidelines (`GEMINI.md`) and a standard operating procedure (SOP) skill (`.agents/skills/winter-storm-triage/SKILL.md`).
* **How it works:** 
  * Workspace policies force code generation boundaries (mandatory docstrings, corporate copyright assertions, and skill isolation rules).
  * The custom skill enforces strict operational behavior: authenticate the disrupted order, cross-reference customer loyalty tiers, trigger the appropriate compensation transaction, and generate an empathetic resolution message.
* **Validation:** Tested end-to-end prompt `/winter-storm-triage`, confirming the agent strictly bound actions to the compensation matrix without hallucinating reward values.

### 3. Service Scaffolding & Local Playground Emulation
* **What was built:** Transitioned the prototype into a production Python application using `google-agents-cli` and `uv`.
* **How it works:** Generated production boilerplate containing the agent definition, MCP bindings, and dependency isolation. Ran an interactive local sandbox environment on port `8080`.
* **Validation:** Simulated platinum-tier customer interactions (`CYM-EC-1002`) within the Agent Playground UI to inspect token streams, tool parameters, and response latency before committing resources to cloud infrastructure.

### 4. Enterprise Deployment to Cloud Agent Runtime
* **What was built:** Packaged and deployed the agent application to Google Cloud Agent Runtime (Reasoning Engine).
* **How it works:** Bundled runtime dependencies and MCP scripts into the application payload, provisioning a managed reasoning engine endpoint executed under a dedicated service account.
* **Validation:** Successfully triggered test requests against the managed regional endpoint in Google Cloud Console, verifying parity between local playground results and the deployed cloud service.

---

## 📊 Business Logic / Rules Implemented

### Compensation & Resolution Matrix
The `winter-storm-triage` agent skill executes deterministic customer remediations based on verified loyalty status:

| Customer Loyalty Tier | Financial Credit | Shipping Carrier Upgrade | Action Trigger |
| :--- | :--- | :--- | :--- |
| **Platinum** | $100 USD | Next-Day Air Upgrade | `issue_disruption_compensation` |
| **Gold** | $50 USD | Next-Day Air Upgrade | `issue_disruption_compensation` |
| **Silver** | $25 USD | 3-Day Select Upgrade | `issue_disruption_compensation` |
| **Member** | $10 USD | Priority Shipping Upgrade | `issue_disruption_compensation` |

### Workspace Governance Rules (`GEMINI.md`)
* **Standard Docstrings:** Every generated Python script must start with module-level documentation explaining architectural intent.
* **Copyright Header:** Explicit inclusion of `Copyright Cymbal Direct` on all authored assets.
* **Modular Skill Containment:** Custom agent routines must reside strictly within `.agents/skills/<skill-name>/`.

---

## 🔑 Key Takeaways & Best Practices

* **Decoupling Tools via MCP:** The Model Context Protocol separates tool implementation from model logic. If the backend logistics API changes, the agent requires zero prompt refactoring—only the MCP schema adapter updates.
* **Declarative Skills over Dense System Prompts:** Breaking complex organizational procedures into discrete `SKILL.md` workflows minimizes prompt bloat, lowers token costs, and makes multi-step triage auditable.
* **Shift-Left Agent Testing:** Using an interactive local playground (`agents-cli playground`) mirrors production behavior locally, catching parameter and tool-calling mismatches prior to cloud deployment.
* **Managed Reasoning Engines:** Deploying to a managed Agent Runtime eliminates the overhead of provisioning custom container clusters and websocket servers for real-time agentic execution.