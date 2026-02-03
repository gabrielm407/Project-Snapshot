# 📚 Documentation Index

Welcome to the Autonomous Research Agent documentation. Start here to find what you need.

---

## 🚀 For Getting Started

### [01-QUICK-START.md](01-QUICK-START.md) — **Start here if you're new**
- Prerequisites and installation
- `azd up` deployment command
- Post-deployment tool setup
- Basic usage example
- Project structure overview

**Time to read:** 5 minutes | **Time to deploy:** 15-30 minutes

---

## 🔧 For Deployment

### [02-DEPLOYMENT-DETAILED.md](02-DEPLOYMENT-DETAILED.md) — **Step-by-step walkthrough**
- Detailed prerequisite checklist
- Authentication setup
- Region and configuration choices
- Complete post-deployment configuration
- Resource provisioning details
- Verification and testing
- Cleanup instructions

**Time to read:** 15 minutes | **Complete deployment:** 30-45 minutes

---

## ⚠️ For Troubleshooting

### [03-TROUBLESHOOTING.md](03-TROUBLESHOOTING.md) — **Problem solving guide**
- "Model gpt-4o-mini cannot be used with bing_grounding" error
- Agent not using web search (using internal knowledge only)
- Deployment failures and quota issues
- Missing "+ Add Tool" button in AI Foundry
- Slow or timeout responses
- Hallucinated information issues
- Performance monitoring and logs
- Error message quick reference

**Use when:** Something isn't working as expected

---

## 📖 For Usage

### [04-USAGE-GUIDE.md](04-USAGE-GUIDE.md) — **How to use the agent effectively**
- Understanding agent vs. chatbot differences
- Basic usage patterns
- Prompting best practices
- Advanced patterns (compliance checking, real-time decisions, etc.)
- Handling agent limitations
- Test suite for verification
- Performance optimization
- Response type examples

**Time to read:** 10 minutes | **Practice:** 20+ minutes

---

## 🏗️ For Technical Understanding

### [05-ARCHITECTURE.md](05-ARCHITECTURE.md) — **Technical deep dive**
- System architecture diagram
- Core component breakdown
- Data flow examples
- Configuration files explained
- Deployment architecture
- Security architecture
- Scaling considerations
- Error handling approach
- Future enhancement ideas

**Best for:** Developers, engineers, technical leads

---

## Quick Navigation by Role

### 👨‍💼 Project Manager / Decision Maker
1. Read [01-QUICK-START.md](01-QUICK-START.md) for overview
2. Check timeline in [02-DEPLOYMENT-DETAILED.md](02-DEPLOYMENT-DETAILED.md)
3. Understand capabilities in [04-USAGE-GUIDE.md](04-USAGE-GUIDE.md)

### 👨‍💻 Developer / DevOps
1. Start with [01-QUICK-START.md](01-QUICK-START.md)
2. Follow [02-DEPLOYMENT-DETAILED.md](02-DEPLOYMENT-DETAILED.md) for setup
3. Reference [05-ARCHITECTURE.md](05-ARCHITECTURE.md) for customization
4. Use [03-TROUBLESHOOTING.md](03-TROUBLESHOOTING.md) as needed

### 🔬 Data Scientist / ML Engineer
1. Review [05-ARCHITECTURE.md](05-ARCHITECTURE.md) for agent design
2. Study [04-USAGE-GUIDE.md](04-USAGE-GUIDE.md) for prompting techniques
3. Check [03-TROUBLESHOOTING.md](03-TROUBLESHOOTING.md) for model issues

### 🏢 End User / Business Analyst
1. Skim [01-QUICK-START.md](01-QUICK-START.md) for context
2. Focus on [04-USAGE-GUIDE.md](04-USAGE-GUIDE.md) for effective usage
3. Refer to [03-TROUBLESHOOTING.md](03-TROUBLESHOOTING.md) for issues

### 🚨 Troubleshooter / Support
1. Go directly to [03-TROUBLESHOOTING.md](03-TROUBLESHOOTING.md)
2. Reference [05-ARCHITECTURE.md](05-ARCHITECTURE.md) for system understanding
3. Use [02-DEPLOYMENT-DETAILED.md](02-DEPLOYMENT-DETAILED.md) for re-deployment if needed

---

## Common Questions Quick Reference

### "How do I deploy this?"
→ [02-DEPLOYMENT-DETAILED.md](02-DEPLOYMENT-DETAILED.md)

### "Something's broken, how do I fix it?"
→ [03-TROUBLESHOOTING.md](03-TROUBLESHOOTING.md)

### "How do I use the agent effectively?"
→ [04-USAGE-GUIDE.md](04-USAGE-GUIDE.md)

### "How does this system work?"
→ [05-ARCHITECTURE.md](05-ARCHITECTURE.md)

### "Can I get started in 5 minutes?"
→ [01-QUICK-START.md](01-QUICK-START.md)

### "The agent isn't using web search, why?"
→ [03-TROUBLESHOOTING.md](03-TROUBLESHOOTING.md#-agent-recites-policy-instead-of-searching)

### "What if I'm getting a 400 Bad Request error?"
→ [03-TROUBLESHOOTING.md](03-TROUBLESHOOTING.md#-error-model-gpt-4o-mini-cannot-be-used-with-bing_grounding)

### "How can I improve agent performance?"
→ [04-USAGE-GUIDE.md](04-USAGE-GUIDE.md#performance-optimization)

---

## Document Organization

```
docs/
├── 01-QUICK-START.md          (5 min read, 15-30 min to deploy)
├── 02-DEPLOYMENT-DETAILED.md  (15 min read, 30-45 min to execute)
├── 03-TROUBLESHOOTING.md      (Reference, solve issues)
├── 04-USAGE-GUIDE.md          (10 min read, practice usage)
├── 05-ARCHITECTURE.md         (20 min read, technical reference)
└── 00-INDEX.md                (This file)
```

---

## Key Concepts Explained

### Autonomous Agent
An AI system that can break down complex tasks, decide which tools to use, and execute them without human intervention.

### Tools
- **Bing Search:** Live web data (prices, news, weather)
- **File Search:** Internal documents (policies, guidelines)

### Chain of Thought (CoT)
The agent's reasoning trace showing:
1. Why it chose specific tools
2. What data it retrieved
3. How it reached conclusions

### Vector Search / RAG
Retrieval-Augmented Generation — combining document search with LLM reasoning for grounded answers.

### Azure AI Foundry
Microsoft's platform for building and deploying AI agents.

---

## Troubleshooting by Error Code

| Error | Document | Section |
|-------|----------|---------|
| 400 Bad Request | 03-TROUBLESHOOTING | gpt-4o-mini error |
| 401 Unauthorized | 03-TROUBLESHOOTING | Authentication failed |
| 404 Not Found | 03-TROUBLESHOOTING | Resource not found |
| 503 Service Unavailable | 03-TROUBLESHOOTING | Rate limited |
| Operation timeout | 03-TROUBLESHOOTING | Deployment fails |
| Agent not searching | 03-TROUBLESHOOTING | Lazy evaluation |
| Slow responses | 03-TROUBLESHOOTING | Performance issues |

---

## Learning Path

### Beginner
1. [01-QUICK-START.md](01-QUICK-START.md) — Understand what this is
2. [02-DEPLOYMENT-DETAILED.md](02-DEPLOYMENT-DETAILED.md) — Get it running
3. [04-USAGE-GUIDE.md](04-USAGE-GUIDE.md) — Use it effectively

**Estimated time:** 1 hour

### Intermediate
1. Complete Beginner path
2. [05-ARCHITECTURE.md](05-ARCHITECTURE.md) — Understand how it works
3. [03-TROUBLESHOOTING.md](03-TROUBLESHOOTING.md) — Solve problems

**Estimated time:** 2 hours

### Advanced
1. Complete Intermediate path
2. Modify system prompts in Azure AI Foundry
3. Add custom tools to the agent
4. Deploy to production with scaling

**Estimated time:** 4+ hours

---

## External Resources

- [Azure AI Foundry Documentation](https://learn.microsoft.com/en-us/azure/ai-foundry/)
- [Azure OpenAI GPT-4o Documentation](https://learn.microsoft.com/en-us/azure/ai-services/openai/concepts/models)
- [Prompt Engineering Best Practices](https://platform.openai.com/docs/guides/prompt-engineering)
- [Azure Developer CLI (azd)](https://learn.microsoft.com/en-us/azure/developer/azure-developer-cli/)

---

## Contributing to Documentation

If you find outdated or incorrect information:
1. Note the document and section
2. Create an issue in the repository
3. Suggest improvements or corrections

---

## Document Versions

- **Version:** 1.0
- **Last Updated:** February 2026
- **Status:** Production Ready

---

**Let's get started! 👉 [Begin with 01-QUICK-START.md](01-QUICK-START.md)**
