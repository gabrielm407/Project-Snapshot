# 🕵️ Autonomous Research Agent

An AI that doesn't just talk—it plans, researches, and acts.

This project demonstrates the shift from standard "Prompt Engineering" to **Agentic Orchestration**. Unlike a traditional chatbot that relies solely on pre-trained data, this agent is equipped with a Mission planner, a Toolbox (live web search and internal document retrieval), and a Reasoning Loop so it can solve multi-step problems autonomously.

**What this repository contains:** a FastAPI backend, a small React frontend, infra Bicep templates to provision Azure resources, and example documents and scripts to demonstrate an autonomous research workflow.

## 🧠 The Problem: The "Static Knowledge" Wall

Most AI implementations today suffer from three major hurdles that prevent them from being truly useful in professional settings:

- **Stale Data:** LLMs are frozen in time and cannot natively fetch today's flight prices, market changes, or live news.
- **Context Blindness:** Generic models lack awareness of your private rules and policies—they can't check a public result against your internal documents.
- **The "Babysitting" Requirement:** Traditional bots force users to orchestrate every step (find data, upload policy, ask for comparison). That manual orchestration eats time and creates human errors.

## 🚀 The Solution: Agency

This project moves from "talking to AI" to "hiring AI to do a job." Give the agent a Mission and a Toolbox and it will:

- Plan multi-step work
- Choose and call tools (web search, document retrieval) as needed
- Iterate until the mission is complete, returning an auditable reasoning trace

In short: the agent takes over the cognitive load of planning and execution.

## 🔑 Key Features & Implementations

1. Multi-Step Autonomous Reasoning
	- Instead of a single linear reply, the agent uses a ReAct (Reason + Act) pattern: it emits Thoughts, chooses Actions, observes outcomes, and repeats until completion.
	- Implementation: Orchestrated by the Azure AI Agent Service, which manages the reasoning loop and tool calls.

2. Live Web Grounding
	- Breaks free of the model's knowledge cutoff by performing real-time web queries.
	- Implementation: Integration with the Bing Search grounding tool for programmatic queries and result parsing.

3. Private Knowledge Integration (RAG)
	- Cross-references live results with private documents (PDFs, Markdown, etc.) so decisions respect internal rules.
	- Implementation: Vector Search via Azure AI Search indexes local documents for Retrieval-Augmented Generation (RAG).

4. Zero-Code Tool Switching
	- The agent dynamically selects the right tool for each step—no hand-coded if/else for when to search or when to read a PDF.
	- Implementation: Function-calling style tool selection driven by the agent's reasoning.

## 🛠️ Tech Stack

- **Core:** Python, FastAPI, Azure AI Agent Service
- **Brain:** GPT-4o (Azure OpenAI)
- **Tools:** Bing Search grounding, Azure AI Search (vector store) for RAG
- **Infra:** Azure Container Apps (provisioned via `azd`)

## 📚 Documentation

Complete docs live in the `docs/` folder. Start here:

| Document | Purpose | Time |
|---|---:|---:|
| [00-INDEX.md](docs/00-INDEX.md) | Navigation guide for all docs | 5 min |
| [01-QUICK-START.md](docs/01-QUICK-START.md) | Get running in 15–30 minutes | 5 min |
| [02-DEPLOYMENT-DETAILED.md](docs/02-DEPLOYMENT-DETAILED.md) | Step-by-step deployment | 15 min |
| [03-TROUBLESHOOTING.md](docs/03-TROUBLESHOOTING.md) | Common issues & solutions | Reference |
| [04-USAGE-GUIDE.md](docs/04-USAGE-GUIDE.md) | How to use the agent | 10 min |
| [05-ARCHITECTURE.md](docs/05-ARCHITECTURE.md) | Technical deep dive | 20 min |

## ⚡ Quick Start (TL;DR)

### Prerequisites

- `azd` (Azure Developer CLI)
- Python 3.10+
- Git

### 1. Install & Deploy

```bash
git clone https://github.com/gabrielm407/Project-Snapshot.git
cd Project-Snapshot
azd auth login
azd up
```

When prompted use:
- **Environment Name:** `agent-demo-dev`
- **Region:** `East US 2`

### 2. Post-Deployment Configuration

In the Azure AI Foundry Portal:
1. Add the **Bing Search** grounding.
2. Upload your internal documents (e.g., `Travel_Policy.pdf`) to File Search.
3. Set the model to **gpt-4o** for best results.

### 3. Start Using

Ask the agent: "Find 3 flights from NYC to Tokyo. Are they under our $500 travel policy limit?"

## 🧪 Usage Example — The "Agency" Demo

User prompt: "Find 3 flight options from NYC to Tokyo for next week. List prices and check if they fit our $500 travel policy." 

Agentic loop (high level):

1. Search — agent decides it needs live data → calls Bing Search.
2. Discovery — agent finds flights (e.g., $900, $1,200, $1,800).
3. Policy Check — agent decides it needs internal rules → calls File Search/RAG.
4. Retrieval — finds policy text: "$500 cap on international economy."
5. Synthesis — agent compares and returns: "All options exceed the $500 limit specified in your corporate policy." along with the action trace.

## 📂 Project Structure (overview)

Top-level folders include `docs/`, `infra/`, `src/`, and sample `data/` documents like `Travel_Policy.pdf`.

## ❓ Common Questions

- **How do I deploy this?** See [02-DEPLOYMENT-DETAILED.md](docs/02-DEPLOYMENT-DETAILED.md).
- **Something's broken?** See [03-TROUBLESHOOTING.md](docs/03-TROUBLESHOOTING.md).
- **How do I use it?** See [04-USAGE-GUIDE.md](docs/04-USAGE-GUIDE.md).

## 📜 License

This project is based on the Azure AI Agents Starter Kit.

---

Ready to build the future of autonomous work? Start with [00-INDEX.md](docs/00-INDEX.md).
