# 🛠️ Step-by-Step Hands-On Guide: Automated Order Triage Agent with Antigravity, MCP & Agent Runtime

> ⬅️ **Navigation:** [Back to Overall Learning Summary (`README.md`)](./README.md)

This guide provides step-by-step instructions to configure, prototype, and deploy the customer service triage agent end-to-end.

---

### Step 1: Initialize Cloud Shell Editor and Authenticate Antigravity CLI
* **Goal:** Launch the Cloud Shell Editor environment, enable hidden file visibility, and configure the Antigravity CLI (`agy`) with your Google Cloud credentials.
* **Actions & Commands:**
  1. Open Cloud Shell and launch the Cloud Shell Editor by clicking **Open Editor** (or press `G` then `S`).
  2. In Cloud Shell Editor, open the top menu and select **View > Toggle Hidden Files** to ensure dotfiles (such as `.agents`) are visible.
  3. Open a new terminal inside the Editor (**Terminal > New Terminal**).
  4. Set your Google Cloud project variable and launch the Antigravity CLI:
     ```bash
     export GOOGLE_CLOUD_PROJECT=$(gcloud config get-value project)
     agy
     ```
  5. Follow the interactive prompts:
     * Authentication method: Select `2` (**Use a Google Cloud project**).
     * Sign-in method: Select `1` (**Use your browser to authenticate**).
     * Open the authorization link, authenticate with your lab credentials, and copy the verification code back into the prompt.
     * Google Cloud Project ID: Enter your `$GOOGLE_CLOUD_PROJECT`.
     * Location: Enter `global`.
     * Select Licenses: Select `Agent Platform - Pay as you go provisioned for your Google Cloud project`.
     * Accept default settings for styling, select **Next** on extensions, and choose **Done** to accept terms.
     * Press `ENTER` to trust the workspace directory.
  6. In the Antigravity chat prompt, set the model and test connectivity:
     ```text
     /model
     ```
     *(Select the default recommended Gemini model)*
     ```text
     Hello
     ```
* **Verification / Expected Result:** The agent responds to `Hello` without connection errors, confirming successful authentication and model binding.

<!-- 📸 Screenshot (Step 1 - Initialize Cloud Shell Editor and Authenticate Antigravity CLI): Uncomment the line below when you add your screenshot -->
<!-- ![Step 1 Screenshot](./screenshots/step-1.png) -->

---

### Step 2: Configure the Cymbal Logistics MCP Server
* **Goal:** Register an external Model Context Protocol (MCP) server running a mock logistics database so the agent can look up shipping states and customer loyalty tiers.
* **Actions & Commands:**
  1. Exit the current `agy` prompt:
     ```text
     /exit
     ```
  2. Create a dedicated workspace directory and fetch the mock logistics server script:
     ```bash
     mkdir -p ~/genai144-challenge/mcp
     cd ~/genai144-challenge
     # Download the logistics script from the lab storage bucket
     gcloud storage cp gs://${GOOGLE_CLOUD_PROJECT}-lab-scripts/cymbal_direct_mcp.py ~/genai144-challenge/mcp/ 2>/dev/null || \
     gcloud storage cp gs://*-lab-scripts/cymbal_direct_mcp.py ~/genai144-challenge/mcp/
     ```
  3. Install the required FastMCP dependencies:
     ```bash
     pip3 install "mcp<2" fastmcp pydantic-settings --break-system-packages
     ```
  4. Create the Antigravity MCP configuration file:
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
  5. Restart the Antigravity CLI:
     ```bash
     agy
     ```
  6. Verify MCP initialization by running:
     ```text
     /mcp
     ```
     *(Wait for `cymbal-logistics` status to show connected, then press `ESC`)*
  7. Query the agent to inspect the loaded tools and test an order lookup:
     ```text
     What tools do you have in your cymbal-logistics MCP server?
     ```
     ```text
     What is the status of order CYM-EC-9921?
     ```
  8. Generate and upload Task 2 summary report:
     ```text
     Create a file named summary_task2.md that lists:
     - The registered MCP server name
     - The names of the three available tools registered on this server
     Upload the summary to gs://${GOOGLE_CLOUD_PROJECT}-grading/
     ```
* **Verification / Expected Result:** The agent identifies tools (`get_order_status`, `get_customer_loyalty_info`, `issue_disruption_compensation`) and confirms order `CYM-EC-9921` is delayed due to severe winter weather.

<!-- 📸 Screenshot (Step 2 - Configure the Cymbal Logistics MCP Server): Uncomment the line below when you add your screenshot -->
<!-- ![Step 2 Screenshot](./screenshots/step-2.png) -->

---

### Step 3: Author Workspace Rules and Agent Skills
* **Goal:** Create a persistent declarative workspace rule (`GEMINI.md`) for code quality and a domain-specific agent skill (`winter-storm-triage`) for loyalty-based compensation.
* **Actions & Commands:**
  1. In the active `agy` session, prompt the agent to create the workspace rule:
     ```text
     Add rules to a GEMINI.md file in the root of my workspace (~/genai144-challenge/). The rules must be configured as always_on and enforce the following code organization rules:
     1. All new Python code files must start with a docstring at the top of the file explaining what the file does.
     2. All new Python code files must start with a commented copyright statement for "Cymbal Direct" at the top of the file.
     3. All custom agent skills must be created in the root of the workspace under the .agents/skills/ directory.
     ```
  2. Confirm active rules:
     ```text
     What rules have been set for this workspace?
     ```
  3. Create the storm triage skill by prompting `agy`:
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
  4. Test the newly created skill:
     ```text
     /winter-storm-triage a customer's package (order CYM-EC-9921) is lost in the winter storm. Please find their customer ID and loyalty tier, and write a response to the customer.
     ```
  5. Generate and upload Task 3 summary report:
     ```text
     Create a file named summary_task3.md in my project directory (~/genai144-challenge/) that lists:
     - The workspace rule file name (GEMINI.md) and its trigger configuration
     - The name of the custom agent skill created for winter storm triage
     - The compensation rules (credits and shipping upgrades) for all four loyalty tiers
     - The actual response generated by the agent for order CYM-EC-9921
     Upload the summary to gs://${GOOGLE_CLOUD_PROJECT}-grading/
     ```
* **Verification / Expected Result:** File `.agents/skills/winter-storm-triage/SKILL.md` is populated. The agent correctly assigns Gold tier compensation ($50 credit + Next-Day Air upgrade) for order `CYM-EC-9921`.

<!-- 📸 Screenshot (Step 3 - Author Workspace Rules and Agent Skills): Uncomment the line below when you add your screenshot -->
<!-- ![Step 3 Screenshot](./screenshots/step-3.png) -->

---

### Step 4: Scaffold and Test Agent Application Locally
* **Goal:** Use `google-agents-cli` to scaffold the project into a deployable Python application and validate execution in the local interactive playground.
* **Actions & Commands:**
  1. Inside the `agy` session, configure tool permissions to bypass manual prompts:
     * Type `/config`, set **Tool Permission** to `always-proceed`, and press `ESC`.
  2. Open a second Cloud Shell Editor terminal tab (leaving `agy` running in the first).
  3. In Terminal 2, install `uv` and configure `google-agents-cli`:
     ```bash
     curl -LsSf https://astral.sh/uv/install.sh | sh
     export PATH="$HOME/.local/bin:$PATH"
     uvx google-agents-cli setup
     ```
  4. Switch back to Terminal 1 (`agy`) and prompt the assistant to scaffold the application:
     ```text
     Use your agents-cli skills to build a winter storm triage agent based on the MCP server. Name the project "winter-storm-triage" and place it inside the ~/genai144-challenge/ directory. Configure the agent to use the Gemini model.
     ```
  5. Return to Terminal 2, navigate to the generated project, and install MCP dependencies:
     ```bash
     cd ~/genai144-challenge/winter-storm-triage
     uv pip install "mcp<2" fastmcp pydantic-settings
     ```
  6. Launch the local interactive playground:
     ```bash
     agents-cli playground
     ```
  7. Preview the web app:
     * Click **Web Preview > Preview on port 8080** in the top-right toolbar.
     * Select `app` from the dropdown menu.
     * Enter query: `Where is my order CYM-EC-1002?`
     * Verify Platinum tier compensation ($100 credit + Next-Day Air).
  8. Stop the playground in Terminal 2 by pressing `Ctrl + C`.
  9. In Terminal 1 (`agy`), generate and upload Task 4 summary:
     ```text
     Create a file named summary_task4.md in my project directory (~/genai144-challenge/) that lists:
     - The project name of the scaffolded agent (winter-storm-triage)
     - The primary configuration file path of the scaffolded agent (app/agent.py)
     - The command used to launch the playground locally
     - The actual response generated by the local agent for order CYM-EC-1002
     Upload the summary to gs://${GOOGLE_CLOUD_PROJECT}-grading/
     ```
* **Verification / Expected Result:** Local playground responds on port 8080 with proper triage compensation matching the customer's Platinum tier status.

<!-- 📸 Screenshot (Step 4 - Scaffold and Test Agent Application Locally): Uncomment the line below when you add your screenshot -->
<!-- ![Step 4 Screenshot](./screenshots/step-4.png) -->

---

### Step 5: Deploy the Agent to Google Cloud Agent Runtime
* **Goal:** Package the application, include the logistics MCP server, and deploy the agent as a managed Reasoning Engine endpoint on Google Cloud.
* **Actions & Commands:**
  1. In Terminal 1 (`agy`), prompt the agent to deploy to Agent Runtime:
     ```text
     Deploy the winter storm triage agent to Agent Runtime in the us-central1 region. Make sure to copy the local `cymbal_direct_mcp.py` file into the agent's `app/` directory before deployment.
     ```
     *(Note: Deployment may take 5–10 minutes as cloud resources and virtual environments are provisioned)*
  2. Verify deployment in the Google Cloud Console:
     * Navigate to **Agent Platform > Agents > Deployments**.
     * Confirm `winter-storm-triage` is listed with an **Active** status.
  3. Validate the production endpoint:
     * Click on the deployed agent and open the **Playground** tab.
     * Submit the validation prompt:
       ```text
       Where is my order CYM-EC-1002?
       ```
     * Confirm the agent retrieves the order, flags the winter storm delay, and offers $100 compensation.
  4. In Terminal 1 (`agy`), generate and upload the final summary report:
     ```text
     Create a file named summary_task5.md in my project directory (~/genai144-challenge/) that lists:
     - The live Reasoning Engine deployment resource ID (including project and location)
     - The region where the agent was deployed
     - The runtime service account email address used by the Reasoning Engine
     Upload the summary to gs://${GOOGLE_CLOUD_PROJECT}-grading/
     ```
* **Verification / Expected Result:** Deployment completes successfully with an active Reasoning Engine resource ID, responding accurately to production API queries.

<!-- 📸 Screenshot (Step 5 - Deploy the Agent to Google Cloud Agent Runtime): Uncomment the line below when you add your screenshot -->
<!-- ![Step 5 Screenshot](./screenshots/step-5.png) -->