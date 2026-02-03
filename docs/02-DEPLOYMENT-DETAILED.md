# 📋 Detailed Deployment Guide

Complete step-by-step walkthrough for deploying the Autonomous Research Agent to Azure.

## Prerequisites Checklist

- [ ] Azure account with active subscription
- [ ] [Azure Developer CLI (azd)](https://learn.microsoft.com/en-us/azure/developer/azure-developer-cli/install-azd) installed
- [ ] [Python 3.10+](https://www.python.org/) installed
- [ ] [Git](https://git-scm.com/) installed
- [ ] Sufficient Azure quota in your chosen region
- [ ] Administrator access to Azure resources

## Step 1: Clone and Prepare

```bash
git clone https://github.com/gabrielm407/Project-Snapshot.git
cd Project-Snapshot
```

Optional: Create a Python virtual environment for local development:
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
pip install -r requirements-dev.txt
```

## Step 2: Authenticate with Azure

```bash
azd auth login
```

This opens your browser to authenticate. Sign in with your Azure account.

**Verify authentication:**
```bash
az account show
```

If multiple subscriptions exist, set the correct one:
```bash
az account set --subscription "<subscription-id>"
```

## Step 3: Configure and Deploy

```bash
azd up
```

The command will prompt you for:

### 1️⃣ Environment Name
- **What it is:** A unique identifier for this deployment (e.g., `agent-demo-dev`, `research-agent-prod`)
- **Why it matters:** Creates a separate resource group and isolated resources
- **Convention:** Use lowercase, hyphens for separation, include environment suffix

Example: `research-agent-dev`

### 2️⃣ Region Selection
- **Recommended regions for GPT-4o:**
  - ✅ **East US 2** (Primary, best availability)
  - ✅ **Sweden Central** (European option)
  - ⚠️ **West Europe** (Check quotas before deploying)
  - ⚠️ **France Central** (Limited GPT-4o availability)

- **If quota issues arise:**
  - Try a different region
  - Or request quota increase in Azure Portal > Quotas

### What Gets Deployed

The `azd up` command provisions:

| Resource | Purpose | SKU |
|----------|---------|-----|
| **Azure AI Foundry** | Agent platform | Standard |
| **Azure AI Search** | Vector search for documents | Basic/Standard |
| **Azure OpenAI (GPT-4o)** | LLM inference | Standard |
| **Bing Search** | Live web search tool | Standard |
| **Container Apps** | Hosting the web app | Consumption |
| **Application Insights** | Monitoring & logging | Pay-as-you-go |
| **Log Analytics** | Log storage | Standard |
| **Storage Account** | Document & artifact storage | Standard |
| **Container Registry** | Docker image storage | Basic |

### Deployment Monitoring

The terminal shows real-time progress:
```
Preparing for azd deployment...
Provisioning Azure resources...
Building and pushing application image...
Deploying to Container Apps...
✅ Deployment completed successfully!
```

**Save the output URL** — this is your deployed application.

## Step 4: Post-Deployment Setup

### Access Azure AI Foundry

1. Go to [Azure Portal](https://portal.azure.com)
2. Search for **AI Foundry** or your environment name
3. Open your AI Foundry Project
4. Navigate to **Agents** section

### Configure Tools for Your Agent

#### 4A: Add Bing Search Grounding

1. In AI Foundry, go to **Agents** > Your Agent
2. Click **Edit** or **Design**
3. In the **Tools** section, click **+ Add Tool**
4. Select **Grounding with Bing**
5. If prompted, create a new Bing Search resource in Azure Portal:
   - Search for "Bing Search v7"
   - Create a Standard tier (recommended)
   - Copy the connection details to AI Foundry
6. **Save**

**Note:** If the button is missing, see [TROUBLESHOOTING.md](03-TROUBLESHOOTING.md#cannot-find--add-tool-button)

#### 4B: Add File Search (Vector Store)

1. In the same **Tools** section, click **+ Add Tool**
2. Select **File Search**
3. Upload **Travel_Policy.pdf** from the `data/` folder:
   - This file is used for policy compliance checks
   - The agent will search this document when answering policy questions
4. **Save**

#### 4C: Set Correct Model

1. In agent settings, find **Model Deployment**
2. Change from `gpt-4o-mini` to **`gpt-4o`**
   - Mini versions don't support the Bing Grounding tool
   - Standard GPT-4o is required for tool use
3. **Save**

## Step 5: Verify Deployment

### Test the Application

1. Navigate to the Container Apps URL (provided by `azd`)
2. You should see the Autonomous Research Agent UI
3. Try a simple query: *"What is the current weather in New York?"*
4. Verify it returns web search results

### Test Agent Tools

Try this comprehensive test:
```
"Find me a flight from NYC to Tokyo for next week with price, 
and tell me if it complies with a $500 travel budget policy."
```

Expected behavior:
- Agent searches Bing for actual flight prices
- Agent checks Travel_Policy.pdf for the budget limit
- Agent synthesizes both and provides an answer

### Check Logs

Monitor deployment with:
```bash
azd monitor
```

This opens Application Insights with real-time logs.

## Cleanup

To remove all Azure resources (and stop charges):

```bash
azd down
```

This deletes the entire environment, resource group, and all provisioned resources.

**Warning:** This is irreversible. Make sure you don't need the data before running this.

## Common Issues During Deployment

| Issue | Solution |
|-------|----------|
| "Region doesn't support GPT-4o" | Use East US 2 or Sweden Central |
| "Quota exceeded" | Request quota increase or use different region |
| "Authentication failed" | Re-run `azd auth login` |
| Partial deployment (some resources missing) | Run `azd up` again to retry |

For detailed troubleshooting, see [TROUBLESHOOTING.md](03-TROUBLESHOOTING.md).

## Next Steps

- [USAGE-GUIDE.md](04-USAGE-GUIDE.md) — Learn advanced prompting techniques
- [ARCHITECTURE.md](05-ARCHITECTURE.md) — Understand the system design
- [TROUBLESHOOTING.md](03-TROUBLESHOOTING.md) — Resolve deployment issues
