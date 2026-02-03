# ⚡ Quick Start Guide

Get up and running with the Autonomous Research Agent in minutes.

## Prerequisites
* [Azure Developer CLI (azd)](https://learn.microsoft.com/en-us/azure/developer/azure-developer-cli/install-azd)
* [Python 3.10+](https://www.python.org/)
* Git

## Installation

Clone the repository:
```bash
git clone https://github.com/gabrielm407/Project-Snapshot.git
cd Project-Snapshot
```

## Deployment (The Magic Command)

This project uses `azd` to provision all necessary Azure resources (AI Foundry, Search, Hosting) automatically.

### Step 1: Authenticate with Azure
```bash
azd auth login
```

### Step 2: Deploy Resources and Application
```bash
azd up
```

You'll be prompted for:
- **Environment Name:** Give it a unique name (e.g., `agent-demo-dev`)
- **Region:** Recommend **East US 2** or **Sweden Central** for best GPT-4o availability

The deployment will create:
- Azure AI Foundry Project
- Azure AI Search for vector storage
- Azure Container Apps for hosting
- Application Insights for monitoring
- All necessary network and security configurations

## Post-Deployment Configuration

Once deployed successfully, you must manually enable the tools in the Azure AI Foundry Portal:

### 1. Enable Bing Search Grounding
1. Navigate to your Azure AI Foundry Project > **Agents**
2. Select your agent
3. In the **Tools** section, add a "Grounding with Bing" resource:
   - Create a new Bing Search resource in Azure Portal if needed
   - Connect it to your agent

### 2. Enable File Search
1. In your agent's **Tools** section, add **File Search**
2. Upload `Travel_Policy.pdf` (found in `data/`) to the Vector Store
3. This enables the agent to cross-reference documents

### 3. Set Correct Model
1. In the agent settings, ensure you're using **`gpt-4o`** (not `gpt-4o-mini`)
2. Save your changes

## Usage Example

### The "Impressive" Demo Flow

Try asking the agent a complex question that requires both external data and internal rules:

```
User: "Find 3 flight options from NYC to Tokyo for next week. 
List the prices and tell me if they fit our company travel policy."
```

### What Happens Behind the Scenes

1. **Agent thinks:** "I need flight prices" → Calls `Bing Search`
2. **Agent searches for data:** Fetches real-time flight options
3. **Agent thinks:** "I need to check the policy limit" → Calls `File Search`
4. **Agent retrieves:** Finds the $500 travel limit in Travel_Policy.pdf
5. **Agent responds:** "I found flights for $1,200 and $900. Both exceed the $500 limit in your Travel Policy."

## Project Structure

```
├── infra/              # Azure Bicep templates for resource creation
│   ├── main.bicep      # Main infrastructure definition
│   ├── main.json       # Generated ARM template
│   └── core/           # Modular Bicep components
├── src/                # Source code for the Agent application
│   ├── api/            # FastAPI application
│   ├── frontend/       # React/TypeScript UI
│   └── templates/      # HTML templates
├── docs/               # Documentation files
├── data/               # Sample documents for upload
└── azure.yaml          # Configuration for azd
```

## Next Steps

- See [DEPLOYMENT-DETAILED.md](02-DEPLOYMENT-DETAILED.md) for step-by-step deployment walkthrough
- See [TROUBLESHOOTING.md](03-TROUBLESHOOTING.md) for common issues and solutions
- See [USAGE-GUIDE.md](04-USAGE-GUIDE.md) for advanced usage patterns
- See [ARCHITECTURE.md](05-ARCHITECTURE.md) for technical deep dive
