# 🕵️ Autonomous Research Agent

> **An AI that doesn't just talk—it plans, researches, and acts.**

This project demonstrates the shift from standard "Prompt Engineering" to **"Agentic Orchestration."** Unlike a traditional chatbot that relies solely on pre-trained data, this agent is equipped with **Tools** (live web search and internal document retrieval) and a **Reasoning Loop** to solve multi-step problems autonomously.

## 🚀 Key Features

* **Live Web Browsing:** Uses `Bing Search` to fetch real-time data (prices, news, weather).
* **Internal Compliance Check:** Uses `File Search` (RAG) to cross-reference findings against private documents (e.g., a "Corporate Travel Policy").
* **Autonomous Reasoning:** The agent autonomously decides *which* tool to use and *when*, creating a Chain of Thought (CoT) trace.

## 🛠️ Tech Stack

* **Core:** Python, Azure AI Agent Service
* **Brain:** GPT-4o (Azure OpenAI)
* **Tools:** Bing Search Grounding, Vector Search (Azure AI Search)
* **Infrastructure:** Azure Container Apps (via `azd`)

---

## 📚 Documentation

Complete documentation is available in the [docs/](docs/) folder. Start here for your needs:

| Document | Purpose | Time |
|----------|---------|------|
| **[00-INDEX.md](docs/00-INDEX.md)** | 📇 Navigation guide for all docs | 5 min |
| **[01-QUICK-START.md](docs/01-QUICK-START.md)** | ⚡ Get running in 15-30 minutes | 5 min |
| **[02-DEPLOYMENT-DETAILED.md](docs/02-DEPLOYMENT-DETAILED.md)** | 🔧 Step-by-step deployment | 15 min |
| **[03-TROUBLESHOOTING.md](docs/03-TROUBLESHOOTING.md)** | 🔴 Common issues & solutions | Reference |
| **[04-USAGE-GUIDE.md](docs/04-USAGE-GUIDE.md)** | 📖 How to use the agent | 10 min |
| **[05-ARCHITECTURE.md](docs/05-ARCHITECTURE.md)** | 🏗️ Technical deep dive | 20 min |

**👉 [Start with docs/00-INDEX.md](docs/00-INDEX.md) if you're new!**

---

## ⚡ Quick Start (TL;DR)

### Prerequisites

* [Azure Developer CLI (azd)](https://learn.microsoft.com/en-us/azure/developer/azure-developer-cli/install-azd)
* [Python 3.10+](https://www.python.org/)
* Git

### 1. Installation & Deployment

Clone and deploy:
```bash
git clone https://github.com/gabrielm407/Project-Snapshot.git
cd Project-Snapshot
azd auth login
azd up
```

Answer prompts:
- **Environment Name:** `agent-demo-dev`
- **Region:** `East US 2` (recommended for GPT-4o)

### 2. Post-Deployment Configuration

In [Azure AI Foundry Portal](https://ai.azure.com):
1. Add **Bing Search** grounding
2. Upload **Travel_Policy.pdf** to **File Search**
3. Set model to **gpt-4o** (not mini)

### 3. Start Using

Ask the agent: *"Find 3 flights from NYC to Tokyo. Are they under our $500 travel policy limit?"*

**For complete instructions, see [docs/01-QUICK-START.md](docs/01-QUICK-START.md) or [docs/02-DEPLOYMENT-DETAILED.md](docs/02-DEPLOYMENT-DETAILED.md)**

---

## 📂 Project Structure

```
├── docs/                    # 📚 Complete documentation
│   ├── 00-INDEX.md         # Navigation guide
│   ├── 01-QUICK-START.md
│   ├── 02-DEPLOYMENT-DETAILED.md
│   ├── 03-TROUBLESHOOTING.md
│   ├── 04-USAGE-GUIDE.md
│   └── 05-ARCHITECTURE.md
├── infra/                   # 🏗️ Azure Bicep templates
│   ├── main.bicep
│   └── core/               # Modular components
├── src/                     # 💻 Source code
│   ├── api/                # FastAPI backend
│   │   ├── main.py
│   │   └── routes.py
│   └── frontend/           # React/TypeScript UI
│       └── src/components/
├── data/                    # 📄 Sample documents
│   └── Travel_Policy.pdf
└── azure.yaml              # 🔧 azd configuration
```

---

## 🚀 Deployment (The Magic Command)

This project uses `azd` to provision all Azure resources automatically:

```bash
azd auth login
azd up
```

Provisions:
- Azure AI Foundry Project
- Azure AI Search (vector store)
- Azure OpenAI (GPT-4o)
- Container Apps (hosting)
- Application Insights (monitoring)

---

## 🧪 Usage Example

### The "Impressive" Demo

Ask: *"Find 3 flight options from NYC to Tokyo for next week. List prices and check if they fit our $500 travel policy."*

**What happens:**
1. **Agent thinks:** "I need real flight prices" → Calls **Bing Search**
2. **Agent searches:** Finds flights for $900, $1,200, $1,800
3. **Agent thinks:** "I need policy limit" → Calls **File Search**
4. **Agent retrieves:** Finds $500 limit in Travel_Policy.pdf
5. **Agent responds:** "All options exceed the $500 policy limit"

---

## ❓ Common Questions

| Question | Answer |
|----------|--------|
| **How do I deploy this?** | See [docs/02-DEPLOYMENT-DETAILED.md](docs/02-DEPLOYMENT-DETAILED.md) |
| **Something's broken** | See [docs/03-TROUBLESHOOTING.md](docs/03-TROUBLESHOOTING.md) |
| **How do I use it?** | See [docs/04-USAGE-GUIDE.md](docs/04-USAGE-GUIDE.md) |
| **How does it work?** | See [docs/05-ARCHITECTURE.md](docs/05-ARCHITECTURE.md) |
| **Where's the full guide?** | See [docs/00-INDEX.md](docs/00-INDEX.md) |

---

## 📜 License

This project is based on the Azure AI Agents Starter Kit.

---

**Ready to get started? 👉 [See docs/00-INDEX.md](docs/00-INDEX.md)**
