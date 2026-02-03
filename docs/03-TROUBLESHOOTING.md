# 🔧 Troubleshooting Guide

Common issues encountered when deploying or running the Autonomous Research Agent, with solutions.

---

## 🔴 Error: "Model gpt-4o-mini cannot be used with bing_grounding"

### Symptoms
* You see a `400 Bad Request` error when the agent tries to search the web
* Error message mentions "Responses API compatible tools"
* The agent fails to perform web searches but works otherwise

### Root Cause
The "Mini" versions of GPT models (and some Preview "Reasoning" models) do not yet support the specific API definitions required for the Bing Grounding tool in Azure AI Agent Service. This is a limitation of Azure's AI service compatibility.

### Solution

**Option 1: Switch to gpt-4o (Recommended)**
1. Go to **Azure AI Foundry** → **Agents**
2. Select your agent
3. Click **Edit** or **Design**
4. Find **Model Deployment** settings
5. Change from `gpt-4o-mini` to `gpt-4o`
6. **Save** and test

**Option 2: Remove Bing Search Temporarily**
- If you cannot access gpt-4o, temporarily disable Bing Search tool
- The agent can still use File Search with mini models

### Prevention
Always verify your model deployment supports tool use before adding tools to your agent.

---

## 🟡 Agent Recites Policy Instead of Searching

### Symptoms
* User asks: *"Find me a flight to Tokyo."*
* Agent immediately responds: *"Flights to Tokyo are likely over your $500 limit"* without searching for actual prices
* Agent never calls the Bing Search tool
* Seems to only use File Search (internal documents)

### Root Cause
The agent is using a "lazy evaluation" strategy. It determined that answering the implied question was possible using only internal knowledge, so it didn't trigger an external search. This can happen due to:
1. Vague prompt wording that doesn't explicitly require live data
2. Agent's system instructions prioritizing internal data
3. Insufficient system prompts about when to search externally

### Solution

**Step 1: Use Explicit Prompts**
Instead of: *"Find flights to Tokyo"*
Try: *"Search the web for current flight prices from NYC to Tokyo for next week, then tell me if they comply with our $500 travel policy."*

**Step 2: Update System Instructions**
1. In AI Foundry, go to **Agents** > Your Agent > **Setup**
2. Find the **System Instructions** or **System Prompt** field
3. Add or update with:

```
You must ALWAYS perform a live web search to find real-world, up-to-date information 
before responding to any questions about prices, availability, weather, news, or time-sensitive data.

Only use internal documents (File Search) AFTER you've gathered current web data.

Never assume you have current information. Always search first.
```

4. **Save** and test

**Step 3: Verify Tools are Configured**
- Ensure Bing Search tool is properly connected (see [DEPLOYMENT-DETAILED.md](02-DEPLOYMENT-DETAILED.md))
- Check that the model is `gpt-4o` (not mini)
- Verify the agent has permission to call external tools

**Step 4: Test Tool Calls**
Use this prompt to force tool usage:
```
"Use the Bing Search tool to find the current average price of a plane ticket 
from New York to Tokyo. Then check our policy document to see if it's within budget."
```

---

## 🟠 Deployment Fails (`azd up` Error)

### Symptoms
* Terminal shows "Deployment failed"
* Error mentions "Resource quota exceeded" or "Operation failed"
* Some resources create successfully, others fail
* Timeout errors during deployment

### Root Cause: Insufficient GPU/TPU Quota

GPT-4o requires specific compute quota in your Azure region. Some regions:
- Have limited availability for new deployments
- Require quota increase requests (can take 24-48 hours)
- Don't support GPT-4o at all

### Solution

**Step 1: Try a Different Region**

1. Delete the current environment:
   ```bash
   azd down
   ```

2. Run deployment again in a recommended region:
   ```bash
   azd up
   ```

3. When prompted for region, select:
   - **East US 2** (Most reliable)
   - **Sweden Central** (European alternative)

**Step 2: Check Your Subscription Tier**

If you're on a **Free Trial** subscription:
- Some resource tiers are restricted
- Upgrade to **Pay-as-You-Go** for full access
- Check quotas in Azure Portal > **Quotas**

**Step 3: Check Resource Quotas**

1. Go to [Azure Portal](https://portal.azure.com)
2. Search for **Quotas**
3. Select your subscription
4. Look for **Azure OpenAI Service** quotas
5. If "gpt-4o" shows insufficient quota:
   - Click **Request quota increase**
   - Set desired quota
   - Submit request (typically approved within 24-48 hours)

**Step 4: Verify Bicep Configuration**

Check `infra/main.bicep` for resource SKUs:
- Ensure all SKUs are compatible with your subscription type
- Look for any hardcoded "Premium" tiers that might require paid subscription

### Prevention
- Always start in **East US 2** for first deployments
- Verify quota **before** running `azd up`
- Use `azd up --preview` to see what will be deployed

---

## 🔵 Cannot Find "+ Add Tool" Button

### Symptoms
* In Azure AI Foundry, the button to add Bing Search is missing or grayed out
* Cannot access the Tools section
* UI doesn't show a way to add File Search or other tools

### Root Cause
- UI lag or cache issues
- Missing resource connections
- Wrong view/mode selected in AI Foundry
- Browser compatibility issues

### Solution

**Step 1: Clear Cache and Refresh**
1. Clear browser cache (Ctrl+Shift+Del on most browsers)
2. Close and reopen Azure AI Foundry
3. Navigate back to your agent

**Step 2: Check Resources are Connected**

1. In AI Foundry, go to **Settings** (⚙️ icon)
2. Select **Connected Resources** or **Management Center**
3. Look for:
   - ✅ Azure AI Search (should be listed)
   - ✅ Bing Search resource (should be in Azure Portal first)
4. If missing, click **+ Add** and create/connect resources

**Step 3: Switch to Design/Edit View**

1. In **Agents**, select your agent
2. Switch from **"Playground"** to **"Design"** or **"Edit"** mode
3. The Tools section should now be visible and editable

**Step 4: Create Bing Search Resource First**

If Bing Search is missing:
1. Go to **Azure Portal** > **Create a resource**
2. Search for **"Bing Search v7"**
3. Create a **Standard** tier resource
4. Once created, go back to AI Foundry
5. In **Connected Resources**, add this resource
6. Now the Tools section should recognize it

**Step 5: Try Different Browser**

Some older browsers have compatibility issues:
- Try **Chrome**, **Edge**, or **Firefox**
- Clear all extensions temporarily
- Test in incognito/private mode

---

## 🟢 Agent Responds Slowly or Times Out

### Symptoms
* Agent takes 30+ seconds to respond
* Requests timeout (504 error)
* "Request pending" message appears indefinitely

### Root Cause
- Web search (Bing) is slow or returning large results
- Vector search (File Search) is processing large documents
- Language model inference taking time (GPT-4o is slower than mini)
- Network latency or resource scaling issues

### Solution

**Step 1: Optimize System Instructions**
1. Reduce prompt length
2. Ask agent to search for less data:
   ```
   "Find the top 3 results only, not all 10."
   ```

**Step 2: Check Azure Resource Health**
1. Go to Azure Portal
2. Open your **Application Insights** resource
3. Check **Response time** graph
4. Look for bottlenecks

**Step 3: Scale Container Apps (if available)**
1. In **Container Apps**, increase **CPU** from 0.5 to 1.0
2. Increase **Memory** from 1.0Gi to 2.0Gi
3. Increase **Replica count** from 1 to 2-3

**Step 4: Use Simpler Queries**
Test with:
```
"What is the weather in NYC?"
```
If this is fast, the issue is with complex multi-tool reasoning. If slow, infrastructure is the issue.

---

## 🟣 Agent Returns Hallucinated Information

### Symptoms
* Agent makes up facts or numbers
* Claims to have searched but makes incorrect statements
* Provides information clearly not from web search or documents

### Root Cause
- Agent is relying on pre-training instead of calling tools
- System prompt doesn't emphasize source verification
- Model configuration allows generation without tool calls

### Solution

**Step 1: Update System Instructions**
```
Always cite your sources. For web searches, mention "According to search results...". 
For document searches, mention "According to the company policy...".

Never make up data or statistics. If you don't know, say so.

If a user asks for current information, you MUST search the web first.
```

**Step 2: Enable Tool Use Enforcement**
- Check if your AI Foundry agent has a setting for "Require tool use"
- Enable it to force the agent to use tools for relevant queries

**Step 3: Test with Verification Prompts**
Ask the agent to explain its reasoning:
```
"Find the latest GPT-4o pricing on Azure and show me your search results."
```

The agent should cite specific sources if it's actually searching.

---

## 📊 Performance and Monitoring

### Check Agent Logs

View real-time logs of agent execution:
```bash
azd monitor
```

This opens **Application Insights** showing:
- ✅ Successful tool calls
- ❌ Failed searches
- ⏱️ Response times
- 📊 Error rates

### Analyze Traces

1. In Application Insights, go to **Traces** or **Logs**
2. Query for errors:
   ```
   traces | where severityLevel >= 2
   ```
3. Look for tool call failures or timeouts

---

## Getting Help

If your issue isn't listed above:

1. **Check logs:** `azd monitor`
2. **Review system messages:** Full error text usually indicates the problem
3. **Try a fresh deployment:** `azd down` then `azd up` in a different region
4. **Check prerequisites:** Ensure azd, Python, and Azure CLI are up to date

For Azure-specific issues, see [Azure OpenAI documentation](https://learn.microsoft.com/en-us/azure/ai-services/openai/).

---

## Common Error Messages Quick Reference

| Error | Cause | Solution |
|-------|-------|----------|
| `400 Bad Request - gpt-4o-mini cannot be used with bing_grounding` | Wrong model | Switch to gpt-4o |
| `401 Unauthorized` | Auth expired | Re-run `azd auth login` |
| `404 Not Found - Resource not found` | Missing resource | Recreate with `azd up` |
| `503 Service Unavailable` | Rate limited or overloaded | Wait and retry |
| `Operation timeout` | Deployment taking too long | Check region quota |
| `CORS error in browser` | Frontend/backend mismatch | Check Container Apps URL |
