# ⚡ Enterprise AI Agent Orchestration with Antigravity & Model Context Protocol (MCP)

## 🎯 The Challenge / Scenario
Severe winter storms caused massive retail shipping delays for Cymbal Direct, overwhelming customer support channels with package status inquiries and refund requests. To resolve this surge efficiently without compromising policy consistency, the engineering team required an autonomous AI agent capable of securely interfacing with internal logistics databases, applying loyalty-tiered compensation policies, and drafting empathetic customer resolutions. This project demonstrates how to connect real-time data backends using the Model Context Protocol (MCP), enforce workspace-level guardrails, scaffold agent services with the Google Agents CLI, and deploy production endpoints to Google Cloud Agent Runtime.

---

## 🧰 Tech Stack & Skills Learned
* **Cloud & Platform:** Google Cloud Platform (GCP), Agent Runtime / Vertex AI Reasoning Engine, Cloud Shell Editor
* **AI & Agent Tooling:** Antigravity CLI (`agy`), Google Agents CLI (`google-agents-cli`), FastMCP, Model Context Protocol (MCP)
* **Languages & Package Management:** Python 3, `uv` (Fast Python package installer and toolchain)
* **Architectural Paradigms:** Tool Use / Function Calling, Declarative Agent Skills, Workspace Guardrails & Rules, Decoupled Backend Service Integration

---

## 🚀 Key Milestones & Architecture
1. **Mock Logistics Backend Integration via MCP:** Decoupled external data retrieval by configuring a standard Model Context Protocol (MCP) server running local Python tools (`get_order_status`, `get_customer_loyalty_info`, and `issue_disruption_compensation`).
2. **Policy Guardrails & Skill Declarations:** Defined declarative workspace rules (`GEMINI.md`) and standard operating procedure skills (`SKILL.md`) governing coding style, copyright requirements, and loyalty-tiered compensation matrices.
3. **Local Prototyping & Playground Validation:** Automated application scaffolding using the Google Agents CLI, running an interactive local agent playground server to validate multi-step tool execution.
4. **Cloud-Native Deployment to Agent Runtime:** Packaged and deployed the production reasoning engine into Google Cloud Agent Runtime as a managed, scalable endpoint.

---

## 🛠️ Step-by-Step Hands-On Guide

### Step 1: Set Up Cloud Shell Editor and Authenticate Antigravity CLI
* **Goal:** Initialize the development workspace, ensure hidden configuration directories are visible, and authenticate the Antigravity developer assistant (`agy`).
* **Actions & Commands:**
  1. Open Cloud Shell Editor from the Google Cloud Console.
  2. Toggle visibility for hidden directories by selecting **View** > **Toggle Hidden Files** (this ensures directories like `.agents` can be managed).
  3. Open a new terminal in Cloud Shell Editor (**Terminal** > **New Terminal**).
  4. Export the project ID and start the Antigravity CLI:
     ```bash
     export GOOGLE_CLOUD_PROJECT=$(gcloud config get-value project)
     agy
     ```
  5. Select the following prompts in the interactive CLI:
     * Authentication method: `2` (Use a Google Cloud project)
     * Sign-in method: `1` (Use your browser to authenticate)
     * Complete the OAuth flow via the displayed URL, then paste the generated authorization code back into the prompt.
     * Google Cloud project ID: `<YOUR_PROJECT_ID>`
     * Region: `global`
     * License: `Agent Platform - Pay as you go provisioned for your Google Cloud project`
     * Accept default display preferences, skip extension imports, and agree to the terms.
  6. Switch model and verify connectivity:
     ```text
     /model
     ```
     Select your designated foundation model, then type:
     ```text
     Hello
     ```
* **Verification / Expected Result:** The CLI returns an initial assistant response confirming that the workspace session is trusted and connected.

<!-- 📸 Screenshot (Step 1 - Set Up Cloud Shell Editor and Authenticate Antigravity CLI): Uncomment the line below when you add your screenshot -->
<!-- ![Step 1 Screenshot](./screenshots/step-1.png) -->

---

### Step 2: Configure the Cymbal Logistics MCP Server
* **Goal:** Extend the agent’s capabilities with external tools by implementing an MCP server that exposes logistics and loyalty data.
* **Actions & Commands:**
  1. Exit the interactive CLI session by typing `/exit`.
  2. Create the project structure and retrieve the logistics database MCP script:
     ```bash
     mkdir -p ~/genai144-challenge/mcp
     gcloud storage cp gs://${GOOGLE_CLOUD_PROJECT}-lab-scripts/cymbal_direct_mcp.py ~/genai144-challenge/mcp/ 2>/dev/null || \
     gcloud storage cp gs://*-lab-scripts/cymbal_direct_mcp.py ~/genai144-challenge/mcp/
     cd ~/genai144-challenge
     ```
  3. Install the required runtime dependencies:
     ```bash
     pip3 install "mcp<2" fastmcp pydantic-settings --break-system-packages
     ```
  4. Register the MCP server in the Antigravity configuration directory:
     ```bash
     mkdir -p .agents
     cat << 'EOF' > .agents/mcp_config.json
     {
        "mcpServers": {
           "cymbal-logistics": {
                 "command": "python3",
                 "args": [
                    "mcp/cymbal_direct_mcp.py"
                 ]
           }
        }
     }
     EOF
     ```
  5. Launch `agy` and verify server registration:
     ```bash
     agy
     ```
     Inside the CLI, run `/mcp` and ensure `cymbal-logistics` initializes successfully. Press `ESC` to return to chat.
  6. Query the MCP server capabilities and test live lookups:
     ```text
     What tools do you have in your cymbal-logistics MCP server?
     ```
     ```text
     What is the status of order CYM-EC-9921?
     ```
* **Verification / Expected Result:** The assistant invokes `get_order_status` and confirms that order `CYM-EC-9921` is delayed due to severe winter conditions.

<!-- 📸 Screenshot (Step 2 - Configure the Cymbal Logistics MCP Server): Uncomment the line below when you add your screenshot -->
<!-- ![Step 2 Screenshot](./screenshots/step-2.png) -->

---

### Step 3: Author Declarative Workspace Rules and Agent Skills
* **Goal:** Implement persistent engineering standards via workspace rules and formalize compensation logic via a custom skill.
* **Actions & Commands:**
  1. In the active `agy` session, prompt the assistant to enforce workspace standards:
     ```text
     Add rules to a GEMINI.md file in the root of my workspace (~/genai144-challenge/). The rules must be configured as always_on and enforce the following code organization rules:
     1. All new Python code files must start with a docstring at the top of the file explaining what the file does.
     2. All new Python code files must start with a commented copyright statement for "Cymbal Direct" at the top of the file.
     3. All custom agent skills must be created in the root of the workspace under the .agents/skills/ directory.
     ```
  2. Verify active rules:
     ```text
     What rules have been set for this workspace?
     ```
  3. Instruct the assistant to generate the triage operational skill:
     ```text
     Create an agent skill named winter-storm-triage. The skill should dictate the standard operating procedures for customers whose packages are delayed due to severe winter storms. It must:
     1. Require the agent to use get_order_status and get_customer_loyalty_info to verify the order.
     2. Apply the following compensation based on loyalty tier:
        - Platinum: $100 credit, Next-Day Air shipping upgrade
        - Gold: $50 credit, Next-Day Air shipping upgrade
        - Silver: $25 credit, 3-Day Select shipping upgrade
        - Member: $10 credit, Priority Shipping upgrade
     3. Require the agent to call issue_disruption_compensation to apply the refund.
     4. Draft an empathetic customer response detailing the resolution.
     ```
  4. Test the generated skill directly:
     ```text
     /winter-storm-triage a customer's package (order CYM-EC-9921) is lost in the winter storm. Please find their customer ID and loyalty tier, and write a response to the customer.
     ```
* **Verification / Expected Result:** The agent executes the triage workflow, identifies the customer as Gold tier, issues a $50 credit with a Next-Day Air shipping upgrade, and crafts an empathetic customer service resolution.

<!-- 📸 Screenshot (Step 3 - Author Declarative Workspace Rules and Agent Skills): Uncomment the line below when you add your screenshot -->
<!-- ![Step 3 Screenshot](./screenshots/step-3.png) -->

---

### Step 4: Scaffold and Test Agent Application with Google Agents CLI
* **Goal:** Convert the agent prototype into a deployable application package using the `google-agents-cli` and validate it in a local interactive playground.
* **Actions & Commands:**
  1. Configure `agy` to run in auto-edit mode to bypass manual file-creation prompts:
     * Type `/config`, set **Tool Permission** to `always-proceed`, and press `ESC`.
  2. Open a second terminal window in Cloud Shell (keep `agy` open in the first tab) and install `uv` along with the Agents CLI:
     ```bash
     curl -LsSf https://astral.sh/uv/install.sh | sh
     export PATH="$HOME/.local/bin:$PATH"
     uvx google-agents-cli setup
     ```
  3. Return to the first terminal (`agy`) and prompt the assistant to generate the production application:
     ```text
     Use your agents-cli skills to build a winter storm triage agent based on the MCP server. Name the project "winter-storm-triage" and place it inside the ~/genai144-challenge/ directory. Configure the agent to use the default model.
     ```
  4. Return to the second terminal, navigate to the generated project directory, install dependencies into the virtual environment, and launch the playground:
     ```bash
     cd ~/genai144-challenge/winter-storm-triage
     uv pip install "mcp<2" fastmcp pydantic-settings
     agents-cli playground
     ```
  5. In Cloud Shell, click **Web Preview** > **Preview on port 8080**.
  6. In the playground dropdown, select `app` and test the query:
     ```text
     Where is my order CYM-EC-1002?
     ```
  7. Terminate the local playground by pressing `CTRL + C` in the second terminal.
* **Verification / Expected Result:** The agent executes locally, queries customer loyalty details, detects Platinum tier status, and responds offering a $100 compensation credit and shipping upgrade.

<!-- 📸 Screenshot (Step 4 - Scaffold and Test Agent Application with Google Agents CLI): Uncomment the line below when you add your screenshot -->
<!-- ![Step 4 Screenshot](./screenshots/step-4.png) -->

---

### Step 5: Deploy the Agent to Google Cloud Agent Runtime
* **Goal:** Deploy the packaged agent application to Google Cloud Agent Runtime (Vertex AI Reasoning Engine) as a managed, production-ready cloud API.
* **Actions & Commands:**
  1. Return to the `agy` session in the first terminal.
  2. Instruct the agent to package the local database server script and execute deployment:
     ```text
     Deploy the winter storm triage agent to Agent Runtime in the default region. Make sure to copy the local `cymbal_direct_mcp.py` file into the agent's `app/` directory before deployment.
     ```
  3. Monitor deployment logs until the CLI confirms completion and outputs the Reasoning Engine deployment ID.
  4. Navigate to the Google Cloud Console:
     * Open **Agent Platform** > **Agents** > **Deployments**.
     * Locate the deployed `winter-storm-triage` instance.
     * Switch to the **Playground** tab and run:
       ```text
       Where is my order CYM-EC-1002?
       ```
* **Verification / Expected Result:** The live deployed endpoint executes and returns the Platinum tier resolution, issuing a $100 compensation credit.

<!-- 📸 Screenshot (Step 5 - Deploy the Agent to Google Cloud Agent Runtime): Uncomment the line below when you add your screenshot -->
<!-- ![Step 5 Screenshot](./screenshots/step-5.png) -->

---

## 📊 Business Logic / Rules Implemented

### Customer Loyalty Compensation Matrix
The automated triage workflow enforces the following business tiers when disruption events occur:

| Loyalty Tier | Disruption Credit | Shipping Upgrade Entitlement | Mandatory MCP Verification Tools |
| :--- | :--- | :--- | :--- |
| **Platinum** | $100.00 | Next-Day Air | `get_order_status`, `get_customer_loyalty_info` |
| **Gold** | $50.00 | Next-Day Air | `get_order_status`, `get_customer_loyalty_info` |
| **Silver** | $25.00 | 3-Day Select | `get_order_status`, `get_customer_loyalty_info` |
| **Member** | $10.00 | Priority Shipping | `get_order_status`, `get_customer_loyalty_info` |

### Architectural Guardrails (`GEMINI.md`)
* **Header Docstrings:** All new Python files must begin with a comprehensive docstring describing utility scope.
* **Licensing Enforcements:** Every source file must contain a top-level copyright header: `# Copyright (c) Cymbal Direct`.
* **Skill Isolation:** All custom capabilities must strictly reside within `.agents/skills/<skill-name>/SKILL.md`.

---

## 🔑 Key Takeaways & Best Practices
* **Decoupled Data Layers via MCP:** Implementing the Model Context Protocol isolates enterprise backend services from agent definitions. Tool schemas can be updated without retraining or redesigning agent logic.
* **Declarative Agent SOPs:** Encoding operational procedures into dedicated skills (`SKILL.md`) guarantees deterministic business policy enforcement while retaining the flexibility of natural language generation.
* **Shift-Left Local Validation:** Utilizing `agents-cli playground` and local virtual environments allows engineers to evaluate tool calling and token costs locally before deploying billable cloud resources.
* **Serverless Production Hosting:** Deploying agents directly to Google Cloud Agent Runtime abstracts container orchestration, scaling, and endpoint authorization into a managed Reasoning Engine service.