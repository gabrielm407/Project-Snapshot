# 📖 Usage Guide

Learn how to effectively use the Autonomous Research Agent and advanced prompting techniques.

---

## Understanding the Agent

The Autonomous Research Agent is different from a standard chatbot:

| Aspect | Standard Chatbot | Autonomous Agent |
|--------|------------------|------------------|
| **Knowledge** | Fixed pre-training only | Pre-training + real-time web search |
| **Decision Making** | Responds based on prompt | Analyzes query and decides which tools to use |
| **Verification** | Assumes correctness | Cross-references multiple sources |
| **Reasoning** | Single-step | Multi-step with visible reasoning |

---

## Basic Usage

### Simple Query (Web Search)
```
"What is the current weather in Seattle?"
```

**What happens:**
1. Agent recognizes this requires current information
2. Calls Bing Search tool
3. Retrieves latest weather data
4. Returns synthesized response

### Policy Compliance Query (Multiple Tools)
```
"I'm traveling to London next month. What's a reasonable hotel budget, 
and does a $150/night option fit our company policy?"
```

**What happens:**
1. Agent calls Bing Search to find current London hotel prices
2. Agent calls File Search to find company policy document
3. Agent synthesizes both sources
4. Provides compliance recommendation

### Internal Knowledge Only
```
"What is the company's policy on home office expenses?"
```

**What happens:**
1. Agent recognizes this requires internal knowledge only
2. Uses File Search to find company policies
3. Returns specific policy document excerpts

---

## Prompting Best Practices

### 1. Be Explicit About Data Requirements

❌ **Vague:**
```
"Is this a good time to buy stocks?"
```

✅ **Clear:**
```
"Search for today's S&P 500 performance and major market news. 
Then tell me if current market conditions suggest this is a good buying opportunity."
```

### 2. Specify When to Use Each Tool

❌ **Ambiguous:**
```
"Tell me about AI trends."
```

✅ **Specific:**
```
"Search the web for the latest AI industry news from the last month. 
Then cross-reference with our internal AI guidelines to see which trends align with our strategy."
```

### 3. Request Source Citations

✅ **Good practice:**
```
"Find the current price of Tesla stock. Show me your search results."
```

The agent will respond with:
- Search results with URLs
- Specific data points
- Timestamps (when available)

### 4. Handle Multi-Step Reasoning Explicitly

✅ **For complex tasks:**
```
"Complete these steps:
1. Search for current Bitcoin prices in USD
2. Check our investment policy for crypto limits
3. Tell me if we can currently buy according to policy
4. Show your reasoning for each step"
```

### 5. Time-Sensitive Queries Require Explicit Search

✅ **Best approach:**
```
"It's February 2026. Search the web for current airfare prices from NYC to Tokyo 
and tell me if they're above or below historical averages."
```

❌ **Will likely hallucinate:**
```
"How much is a flight to Tokyo?"
```
(Agent might use pre-training knowledge from 2023)

---

## Advanced Patterns

### Pattern 1: Comparative Analysis

```
"Search for the latest pricing for Azure OpenAI GPT-4o vs. OpenAI's GPT-4o API. 
Compare them on:
1. Cost per 1M tokens (input and output)
2. Latency/performance
3. Rate limits
4. Availability in different regions

Then cross-reference with our budget constraints in the finance policy."
```

**Agent's process:**
1. Performs multiple web searches
2. Checks File Search for budget constraints
3. Creates structured comparison
4. Highlights budget-compliant options

### Pattern 2: Compliance Checking

```
"I want to hire a contractor from Canada for $50,000/year. 
Please:
1. Search for current Canadian tax implications for contractor payments
2. Check our hiring policy for contractor approval thresholds
3. Tell me if this hiring needs approval and what approvals are required"
```

**Agent's process:**
1. Searches for tax information
2. Retrieves hiring policy from File Search
3. Identifies decision point (approval threshold)
4. Provides clear recommendation

### Pattern 3: Real-Time Decision Making

```
"Our meeting is tomorrow at 2 PM in New York. 
Search for:
1. Current weather forecast for tomorrow
2. Traffic conditions and recommended travel time to venue
3. Any major events that might affect NYC traffic

Help me decide if I need to leave early."
```

**Agent's process:**
1. Gets weather and traffic data
2. Synthesizes into travel recommendation
3. Provides contingency planning

### Pattern 4: Document-Based Deep Dive

```
"Using our company's Travel Policy document:
1. What is the maximum hotel cost per night in Europe?
2. What is the approval process for expenses exceeding $5,000?
3. Are there any restrictions for travel to specific countries?"
```

**Agent's process:**
1. Searches File Search for policy document
2. Extracts specific information
3. Provides direct quotes with line references

### Pattern 5: Verification and Fact-Checking

```
"A colleague claims that 'Azure Storage accounts now cost $0.01 per GB monthly'. 
Please:
1. Search Azure's official pricing for current Storage account costs
2. Check different regions (US East, Europe, Asia)
3. Tell me if the claim is accurate or outdated"
```

**Agent's process:**
1. Performs authoritative web search
2. Retrieves current pricing
3. Verifies or debunks the claim with sources

---

## Handling Agent Limitations

### When Web Search Doesn't Help

Some queries can't be answered with web search:
```
❌ "What are next week's lottery numbers?"
❌ "Will our company be profitable in 2027?"
❌ "Is Company X planning to acquire Company Y?" (not yet public)
```

**How to handle:**
- Agent will indicate it cannot search for non-public information
- Provide context: "These are predictions, not facts"
- Ask for historical data instead: "What were Company X's acquisition patterns in 2024-2025?"

### When File Search Returns Too Much Data

If documents are too large or numerous:
```
"Search the policy documents for ONE specific answer: 
What is the maximum daily meal allowance for business travel in North America? 
Give me just the number and the policy section."
```

This focuses the agent's output.

### Handling Conflicting Information

If web sources conflict:
```
"You may find conflicting information about Python 3.13 release date 
from different sources. Cite the official Python.org announcement 
and any other authoritative sources you find."
```

The agent will note discrepancies and prioritize official sources.

---

## Testing Your Agent

### Test Suite for Verification

Use these queries to verify your agent is working correctly:

**Test 1: Web Search Only**
```
"What was the Nasdaq closing price yesterday? Provide the exact number."
```
✅ Should return current data with source
❌ Should NOT use pre-training from 2023

**Test 2: File Search Only**
```
"What is the exact policy on remote work frequency?"
```
✅ Should quote the policy document
❌ Should NOT make up policy information

**Test 3: Multi-Tool Reasoning**
```
"Find the current price of a one-way flight from NYC to Tokyo. 
Is it within our $2,000 international travel budget from the policy documents?"
```
✅ Should use both tools and synthesize answer
❌ Should NOT skip web search or file search

**Test 4: Source Citation**
```
"Show me where you found the information about current flight prices. 
Include the website URL."
```
✅ Should cite Bing Search results
❌ Should NOT cite "my training data"

---

## Performance Optimization

### Query Optimization

**Too detailed:**
```
"Find me everything about climate change, global warming, 
carbon emissions, renewable energy, and their environmental impact."
```

**Better:**
```
"What were the top 3 climate change developments in 2025?"
```

### Reducing Response Time

1. **Limit search scope:**
   ```
   "Find the top 3 results for..." (not all 20)
   ```

2. **Specify document sections:**
   ```
   "Find only the pricing section of the contract"
   ```

3. **Ask for summaries:**
   ```
   "Summarize in 3 bullet points..."
   ```

---

## Common Response Types

### Type 1: Direct Answer
```
User: "What is 2+2?"
Agent: "2+2 equals 4. This doesn't require web search; it's basic math."
```

### Type 2: Search-Based Answer
```
User: "What's the current Bitcoin price?"
Agent: "According to Bing Search [timestamp], Bitcoin is trading at $XXXXX. 
Source: [CoinMarketCap, Reuters]"
```

### Type 3: Multi-Source Answer
```
User: "Can I approve a $5,000 business lunch expense?"
Agent: "Based on web search and company policy:
- Market rate for executive business meals: $150-300/person
- Company policy limit: $5,000/transaction requires CFO approval
- Recommendation: Your expense falls within policy but requires approval."
```

### Type 4: Unable to Answer
```
User: "Will our company be acquired in 2026?"
Agent: "I cannot predict future events. I can search for:
- Current acquisition rumors
- Company's historical M&A activity
- Industry trends

Would you like me to search any of these instead?"
```

---

## Tips for Best Results

1. ✅ **Use recent dates:** "As of February 2026, what..."
2. ✅ **Be specific:** Use exact names, numbers, dates
3. ✅ **Specify tool preference:** "Search the web for..." vs. "Check the documents for..."
4. ✅ **Request citations:** "Show your sources"
5. ✅ **Ask for confidence:** "How confident are you in this data?"
6. ❌ **Avoid:** Vague pronouns, ambiguous timeframes, mixed contexts

---

## Next Steps

- [ARCHITECTURE.md](05-ARCHITECTURE.md) — Understand how the agent works internally
- [TROUBLESHOOTING.md](03-TROUBLESHOOTING.md) — Resolve any issues
- [QUICK-START.md](01-QUICK-START.md) — Back to deployment guide
