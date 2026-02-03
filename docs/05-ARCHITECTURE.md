# 🏗️ Architecture & Design

Technical deep dive into how the Autonomous Research Agent works.

---

## System Overview

```
┌─────────────────────────────────────────────────────────────────┐
│                     User Interface (React/TypeScript)           │
│                    Running in Container Apps                     │
└────────────────────────┬────────────────────────────────────────┘
                         │
                         │ HTTP/REST
                         ▼
┌─────────────────────────────────────────────────────────────────┐
│                   FastAPI Backend (Python)                      │
│                   src/api/main.py                               │
│  ┌──────────────────┬──────────────────┬──────────────────┐    │
│  │ Agent Logic      │ Tool Management  │ File Operations  │    │
│  │ (routes.py)      │ (routes.py)      │ (blob_store...)  │    │
│  └──────────────────┴──────────────────┴──────────────────┘    │
└────────────────────────┬──────────────────────────────────────┬─┘
                         │                                      │
         ┌───────────────┴────────────────┐                    │
         │                                │                    │
         ▼                                ▼                    │
    ┌──────────────────┐         ┌──────────────────┐          │
    │ Azure AI Foundry │         │  Azure AI Search │          │
    │   (Agent        │         │  (Vector Store)  │          │
    │   Service)      │         │  File Search     │          │
    │                 │         │                  │          │
    │ GPT-4o Model    │         │  Travel_Policy.│          │
    │ (LLM Brain)     │         │  pdf embedded   │          │
    └────────┬────────┘         └──────┬───────────┘          │
             │                        │                       │
             │      Tool Calls        │                       │
             └────────┬───────────────┘                       │
                      │                                        │
                 ┌────┴────────────────────┐                  │
                 │                         │                  │
                 ▼                         ▼                  │
          ┌──────────────┐         ┌──────────────┐           │
          │ Bing Search  │         │ File Search  │           │
          │ (Live Web)   │         │ (Documents)  │           │
          └──────────────┘         └──────────────┘           │
                                                               │
                                                               ▼
                                                    ┌──────────────────┐
                                                    │  Storage Account  │
                                                    │  (Logs, Artifacts)
                                                    └──────────────────┘
```

---

## Core Components

### 1. Frontend (React/TypeScript)

**Location:** `src/frontend/`

**Key Files:**
- `src/components/App.tsx` — Main component
- `src/components/agents/` — Agent UI components
  - `AgentPreviewChatBot.tsx` — Chat interface
  - `UserMessage.tsx` / `AssistantMessage.tsx` — Message rendering
  - `AgentIcon.tsx` — Agent visual representation

**Responsibilities:**
- Render chat interface
- Send user messages to backend
- Display agent responses and reasoning traces
- Handle real-time updates

**Technology Stack:**
- React 18+
- TypeScript
- Vite (build tool)
- CSS Modules for styling

**Communication:**
```typescript
// User sends message
POST /api/chat
{
  "message": "Find flights to Tokyo under $2000",
  "conversation_id": "uuid"
}

// Backend returns
{
  "response": "Found 3 flights...",
  "sources": ["Bing Search", "File Search"],
  "thinking": "Agent reasoning trace..."
}
```

### 2. Backend API (Python/FastAPI)

**Location:** `src/api/`

**Key Files:**
- `main.py` — FastAPI application entry point
- `routes.py` — API endpoints and logic
- `search_index_manager.py` — Vector search operations
- `blob_store_manager.py` — Document storage
- `util.py` — Helper functions

**Key Endpoints:**

#### POST `/api/chat`
```python
# Request
{
  "message": str,
  "conversation_id": str,
  "include_thinking": bool
}

# Response
{
  "response": str,
  "sources": List[str],
  "thinking_trace": str,
  "timestamp": datetime
}
```

**Responsibilities:**
- Receive user messages
- Call Azure AI Foundry Agent Service
- Manage tool execution (Bing Search, File Search)
- Format and return responses
- Handle errors gracefully

**Technology Stack:**
- Python 3.10+
- FastAPI (web framework)
- Azure SDK (Python)
- Gunicorn (production server)

### 3. Azure AI Foundry Agent

**What it is:** The "brain" of the system — orchestrates LLM reasoning with tools.

**Configuration:**
- **Model:** GPT-4o (Azure OpenAI)
- **System Instructions:** Define agent behavior and tool use
- **Tools:**
  1. **Bing Search** — Live web search
  2. **File Search** — Vector search over documents
  3. **Custom Tools** — Can be added as needed

**How it works:**

```
User Question
     │
     ▼
┌─────────────────────────────────────────┐
│  Agent Receives Question                │
│  Analyzes: "What tools do I need?"     │
└────────┬────────────────────────────────┘
         │
         ▼
    ┌────────────────────┐
    │ Decision: Use      │
    │ Bing Search +      │
    │ File Search        │
    └────────┬───────────┘
             │
             ▼
    ┌────────────────────────────────────┐
    │ Tool 1: Bing Search                │
    │ (Searches web for flight prices)   │
    └────────┬───────────────────────────┘
             │
             ▼
    ┌────────────────────────────────────┐
    │ Tool 2: File Search                │
    │ (Finds policy in documents)        │
    └────────┬───────────────────────────┘
             │
             ▼
    ┌────────────────────────────────────┐
    │ Agent Synthesizes:                 │
    │ - Flights: $1200, $900, $1800      │
    │ - Policy Limit: $500               │
    │ - Conclusion: All over budget      │
    └────────┬───────────────────────────┘
             │
             ▼
    ┌────────────────────────────────────┐
    │ Response to User with Sources      │
    └────────────────────────────────────┘
```

**Chain of Thought (CoT):**
The agent generates a reasoning trace showing:
1. Why tools were chosen
2. What data was retrieved
3. How conclusions were reached

Example trace:
```
Thinking: The user is asking for flight prices - I need current data.
Action: Call Bing Search for "flights NYC to Tokyo 2026"
Observation: Found 5 flights ranging from $900-$2000
Thinking: Now I need to check company policy limits
Action: Call File Search for "travel budget"
Observation: Company allows max $500 international flights
Conclusion: All options exceed policy limits
```

### 4. Data Storage

#### Azure AI Search (Vector Store)

**Purpose:** Index and retrieve documents for File Search tool

**Current Content:**
- `Travel_Policy.pdf` — Company travel guidelines
- Can be expanded with more documents

**How it works:**
1. Document uploaded to Azure Portal
2. Converted to embeddings (vector representation)
3. Stored in search index
4. Agent can query: "Find travel policy restrictions"
5. Returns relevant document excerpts

**Search Process:**
```
Query: "What's the flight budget limit?"
    │
    ▼
Vector embedding of query
    │
    ▼
Search vector database
    │
    ▼
Find most similar document sections (cosine similarity)
    │
    ▼
Return: "Policy Section 2.1: International flights limited to $500/ticket"
```

#### Azure Storage Account

**Purpose:** Store logs, uploaded files, and artifacts

**Typical contents:**
- Application logs
- Document metadata
- Conversation history
- Generated reports

### 5. Monitoring & Logging

#### Application Insights

**Collects:**
- API response times
- Error rates and stack traces
- Tool call success/failure rates
- User query patterns
- Performance metrics

**Access:** Azure Portal → Application Insights → Metrics/Logs

#### Log Analytics

**Stores:**
- Detailed log entries
- Query traces
- System events
- Custom metrics from the application

**Query Example:**
```kusto
// Find all tool calls that took > 5 seconds
traces 
| where message contains "tool_call"
| where duration > 5000
| project timestamp, message, duration
```

---

## Data Flow Deep Dive

### Scenario: User Asks About Flight Compliance

```
USER
"Find flights to Tokyo. Are they within policy?"
    │
    ▼
FRONTEND (React)
- Sends POST /api/chat with message
    │
    ▼
BACKEND (FastAPI)
- routes.py receives request
- Calls Azure AI Foundry Agent Service
    │
    ▼
AGENT (GPT-4o)
1. Parses: "Need current flight data + policy check"
2. Decision: Use BingSearch + FileSearch tools
    │
    ├─────────────────────────────────────┐
    │                                     │
    ▼                                     ▼
BingSearch                          FileSearch
(API Call)                          (Vector Search)
│                                   │
└─────────────────────┬─────────────┘
                      │
                      ▼
              Search Results:
              - Flights: $900-$2000
              - Policy: max $500
                      │
                      ▼
            Agent Synthesizes Answer:
            "Flights exceed policy"
                      │
                      ▼
                BACKEND
                - Formats response
                - Logs to Application Insights
                - Returns JSON
                      │
                      ▼
                FRONTEND
                - Renders message
                - Shows sources
                - Updates conversation history
                      │
                      ▼
                USER
                Sees synthesized answer
                with sources cited
```

---

## Configuration Files

### `azure.yaml`
Defines Azure Developer CLI configuration:
```yaml
metadata:
  template: Project-Snapshot@0.1.0
name: Project-Snapshot
services:
  api:
    project: src/api
    language: py
    host: containerapp
  frontend:
    project: src/frontend
    language: ts
    host: staticwebapp
```

### `infra/main.bicep`
Infrastructure as Code (IaC) defining all Azure resources:
- Container Apps (hosting)
- Azure AI Foundry Project
- Azure OpenAI (GPT-4o)
- Azure AI Search
- Storage Account
- Application Insights
- Log Analytics

### `docker-compose.yaml`
Local development environment:
```yaml
services:
  api:
    build: ./src/api
    ports:
      - "8000:8000"
  frontend:
    build: ./src/frontend
    ports:
      - "5173:5173"
```

---

## Deployment Architecture

```
┌──────────────────────────────────────────────────────┐
│         Azure Subscription                           │
│  ┌────────────────────────────────────────────────┐  │
│  │ Resource Group: [environment-name]             │  │
│  │                                                │  │
│  │ ┌──────────────────┐  ┌──────────────────┐   │  │
│  │ │ Container Apps   │  │ Static Web App   │   │  │
│  │ │ (API Backend)    │  │ (React Frontend) │   │  │
│  │ └──────────────────┘  └──────────────────┘   │  │
│  │                                                │  │
│  │ ┌──────────────────────────────────────────┐  │  │
│  │ │ Azure AI Foundry                         │  │  │
│  │ │ ├─ GPT-4o Deployment                    │  │  │
│  │ │ └─ Agent Service                        │  │  │
│  │ └──────────────────────────────────────────┘  │  │
│  │                                                │  │
│  │ ┌──────────────────┐  ┌──────────────────┐   │  │
│  │ │ AI Search        │  │ Storage Account  │   │  │
│  │ │ (Vector Store)   │  │                  │   │  │
│  │ └──────────────────┘  └──────────────────┘   │  │
│  │                                                │  │
│  │ ┌──────────────────────────────────────────┐  │  │
│  │ │ Application Insights + Log Analytics     │  │  │
│  │ └──────────────────────────────────────────┘  │  │
│  │                                                │  │
│  │ ┌──────────────────┐  ┌──────────────────┐   │  │
│  │ │ Container        │  │ Key Vault        │   │  │
│  │ │ Registry         │  │ (Secrets)        │   │  │
│  │ └──────────────────┘  └──────────────────┘   │  │
│  └────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────┘
```

---

## Security Architecture

### Authentication
- **Users → Frontend:** Azure AD (recommended)
- **Frontend → Backend:** Session tokens/Bearer tokens
- **Backend → Azure Services:** Managed Identity

### Secrets Management
- API keys stored in Azure Key Vault
- Never hardcoded or in environment files
- Rotated regularly

### Data Protection
- HTTPS for all communications
- Encryption at rest in Storage Account
- Private endpoints for sensitive resources

---

## Scaling Considerations

### Horizontal Scaling
- Container Apps auto-scales based on CPU/memory
- Multiple replicas serve traffic
- Load balancer distributes requests

### Vertical Scaling
- Increase CPU from 0.5 to 1.0+
- Increase memory from 1.0Gi to 2.0Gi+
- GPT-4o throughput determined by OpenAI quotas

### Optimization Techniques
1. Cache agent responses for common queries
2. Batch multiple requests
3. Reduce document size for File Search
4. Optimize system prompts for efficiency

---

## Error Handling

### Agent Failures
```python
# When agent tool call fails
if tool_result.status == "error":
    # Log to Application Insights
    # Return user-friendly error message
    # Suggest alternative actions
```

### Tool Timeouts
- Bing Search timeout → Return partial results
- File Search timeout → Use broader search
- GPT-4o timeout → Return cached response

### Frontend Error Boundaries
- Catch rendering errors
- Show fallback UI
- Log to backend for monitoring

---

## Future Enhancements

### Possible Extensions
1. **Multi-turn Conversations** — Remember context across messages
2. **Custom Tools** — Add more specialized tools
3. **User Persistence** — Save conversation history
4. **Fine-tuning** — Customize agent for domain-specific tasks
5. **Real-time Streaming** — Stream agent responses as they're generated
6. **Feedback Loop** — User ratings to improve agent accuracy

---

## Next Steps

- [DEPLOYMENT-DETAILED.md](02-DEPLOYMENT-DETAILED.md) — Deploy this architecture
- [TROUBLESHOOTING.md](03-TROUBLESHOOTING.md) — Diagnose issues
- [USAGE-GUIDE.md](04-USAGE-GUIDE.md) — Effective prompting
