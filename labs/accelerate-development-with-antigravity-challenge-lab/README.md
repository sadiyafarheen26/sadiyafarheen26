# ❄️ Automated Order Triage Agent with Antigravity, MCP & Agent Runtime

> 📖 **Navigation:** You are viewing the **Overall Learning Summary**. For the complete reproducible walkthrough, open the **[🛠️ Step-by-Step Hands-On Guide (`steps.md`)](./steps.md)**.

## 🎯 The Challenge / Scenario
Severe winter storms caused widespread supply chain disruptions and delivery delays for Cymbal Direct, triggering an unsustainable surge in customer service inquiries. Human support teams were overwhelmed manually looking up shipping records, validating customer loyalty tiers, and issuing appropriate compensation. 

To resolve this bottleneck, an autonomous AI customer support agent was designed, tested, and deployed to production. Using Google's **Antigravity CLI (`agy`)**, **Model Context Protocol (MCP)**, and **Google Cloud Agent Runtime**, the agent securely integrates with enterprise logistics databases, executes deterministic policy rules, and issues instant refunds and shipping upgrades.

## 🧰 Tech Stack & Skills Learned
* **Agentic Frameworks & CLI:** Google Antigravity CLI (`agy`), Google Agents CLI (`google-agents-cli`), FastMCP.
* **Protocols & Runtimes:** Model Context Protocol (MCP), Vertex AI Agent Runtime / Reasoning Engine.
* **Cloud & Developer Tools:** Google Cloud Shell Editor, Cloud Storage (`gcloud storage`), Python toolchain manager (`uv`).
* **Core Competencies:** Declarative workspace rules (`GEMINI.md`), Custom Agent Skills (`SKILL.md`), Tool Calling & Integration, Agent Scaffolding & Local Playground Validation, Cloud Deployment.

## 🚀 Key Milestones & Architecture
1. **Model Context Protocol (MCP) Integration:** Connected an external Python-based mock logistics service (`cymbal-logistics`) to provide real-time order queries and refund processing via standardized JSON-RPC schemas.
2. **Declarative Workspace Rules & Skills:** Enforced strict enterprise code standards (`GEMINI.md`) and encapsulated deterministic business logic into a modular skill (`winter-storm-triage`) covering 4 customer loyalty tiers.
3. **Application Scaffolding & Local Emulation:** Generated production-ready agent application boilerplate using `google-agents-cli` and `uv`, validating tool calls and responses in an interactive local playground web server on port 8080.
4. **Cloud Agent Runtime Deployment:** Deployed the production agent package onto Google Cloud Agent Runtime (Reasoning Engine), exposing an enterprise-ready, scalable AI service endpoint.

```
       +-------------------------------------------------------+
       |             Customer Support Inquiry                 |
       +-------------------------------------------------------+
                                  │
                                  ▼
       +-------------------------------------------------------+
       |     Agent Runtime / Reasoning Engine (Gemini)         |
       |  - Governed by GEMINI.md & winter-storm-triage Skill  |
       +-------------------------------------------------------+
                                  │
                  Tool Calls via Model Context Protocol (MCP)
                                  │
                                  ▼
       +-------------------------------------------------------+
       |           Cymbal Direct Logistics MCP Server          |
       |  - get_order_status()                                 |
       |  - get_customer_loyalty_info()                        |
       |  - issue_disruption_compensation()                    |
       +-------------------------------------------------------+
```

## 📊 Business Logic / Rules Implemented

The `winter-storm-triage` skill enforces automated compensation based on customer tier when delayed shipments are detected:

| Customer Tier | Credit Amount | Shipping Upgrade Granted | Verification Tools Required |
| :--- | :--- | :--- | :--- |
| **Platinum** | **$100.00** | Next-Day Air | `get_order_status`, `get_customer_loyalty_info` |
| **Gold** | **$50.00** | Next-Day Air | `get_order_status`, `get_customer_loyalty_info` |
| **Silver** | **$25.00** | 3-Day Select | `get_order_status`, `get_customer_loyalty_info` |
| **Member** | **$10.00** | Priority Shipping | `get_order_status`, `get_customer_loyalty_info` |

* **Execution Mandate:** Agents must call `issue_disruption_compensation` to programmatically apply credits and craft empathetic, structured responses outlining the mitigation steps.

## 🔑 Key Takeaways & Best Practices
* **Decoupled Architecture with MCP:** Standardizing tool exposure through the Model Context Protocol removes hard-coded API integrations, allowing agents to switch between local testing and production backends seamlessly.
* **Declarative Agent Guardrails:** Centralizing workspace rules (`GEMINI.md`) and SOP-driven skills ensures that generative responses strictly adhere to business policies, legal constraints, and brand tone.
* **Shift-Left Local Prototyping:** Validating agent actions in a local playground using `uv` and `google-agents-cli` prevents unnecessary cloud runtime costs and accelerates debugging before deployment.

---
👉 **[Continue to the Step-by-Step Hands-On Guide (`./steps.md`)](./steps.md)**