# 🐍 Python AI Service Specification

> **Parent Docs:** [Architecture](architecture.md), [Master Prompting](master_prompting.md), [BPA-3](bpa-3.md)
> **Service Role:** Agentic AI Brain for Sentient Factory
> **Last Sync:** 2024-12-27

---

## 1. Tech Stack & Setup

### 1.1 Technology Stack

| Category   | Technology    | Version | Purpose                |
| ---------- | ------------- | ------- | ---------------------- |
| Language   | Python        | 3.11+   | Runtime                |
| Framework  | LangChain     | 0.1.x   | Agent framework        |
| LLM        | Qwen-72B      | -       | Language model (local) |
| LLM Server | vLLM / Ollama | -       | Model serving          |
| Vector DB  | ChromaDB      | 0.4.x   | Document embeddings    |
| Messaging  | NATS          | nats-py | NestJS communication   |
| Database   | asyncpg       | 0.29.x  | PostgreSQL async       |
| Validation | Pydantic      | 2.x     | Data validation        |
| Async      | asyncio       | -       | Async runtime          |

### 1.2 Project Setup

```bash
# Create project
mkdir sentient-ai-service
cd sentient-ai-service

# Create virtual environment
python -m venv venv
source venv/bin/activate  # Linux/Mac
# or: venv\Scripts\activate  # Windows

# Install dependencies
pip install langchain langchain-community
pip install chromadb sentence-transformers
pip install nats-py asyncpg pydantic
pip install python-dotenv uvicorn fastapi
pip install vllm  # or: pip install ollama

# Run service
python -m app.main
```

### 1.3 Environment Config

```env
# .env
# LLM Configuration
LLM_MODEL=qwen2.5-72b-instruct
LLM_BASE_URL=http://localhost:8080/v1
LLM_MAX_TOKENS=4096
LLM_TEMPERATURE=0.1

# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=sentient_factory
DB_USER=postgres
DB_PASS=secret

# NATS
NATS_URL=nats://localhost:4222

# Vector DB
CHROMA_PATH=./chroma_db

# Logging
LOG_LEVEL=INFO
```

---

## 2. Project Structure

```
sentient-ai-service/
├── app/
│   ├── __init__.py
│   ├── main.py                 # Entry point
│   ├── config.py               # Settings
│   ├── agent/
│   │   ├── __init__.py
│   │   ├── orchestrator.py     # Main agent logic
│   │   ├── prompts.py          # Prompt templates
│   │   └── memory.py           # Conversation memory
│   ├── tools/
│   │   ├── __init__.py
│   │   ├── base.py             # Tool base class
│   │   ├── sql_tool.py         # SQL query tool
│   │   ├── rag_tool.py         # Document search
│   │   ├── calculator_tool.py  # Business math
│   │   └── alert_tool.py       # Trigger alerts
│   ├── messaging/
│   │   ├── __init__.py
│   │   ├── nats_client.py      # NATS connection
│   │   └── handlers.py         # Message handlers
│   ├── database/
│   │   ├── __init__.py
│   │   └── connection.py       # PostgreSQL pool
│   └── models/
│       ├── __init__.py
│       ├── request.py          # Input schemas
│       └── response.py         # Output schemas
├── prompts/
│   ├── global_persona.txt
│   ├── sales_quotation.txt
│   ├── maintenance_guide.txt
│   └── executive_brief.txt
├── tests/
├── .env
├── requirements.txt
└── README.md
```

---

## 3. LLM Integration

### 3.1 LLM Client

```python
# app/agent/llm_client.py

from langchain_community.llms import VLLMOpenAI
from langchain_community.chat_models import ChatOllama
from app.config import settings

def get_llm():
    """Get LLM client based on configuration."""
    if settings.LLM_PROVIDER == "vllm":
        return VLLMOpenAI(
            openai_api_base=settings.LLM_BASE_URL,
            model_name=settings.LLM_MODEL,
            max_tokens=settings.LLM_MAX_TOKENS,
            temperature=settings.LLM_TEMPERATURE,
        )
    else:  # ollama
        return ChatOllama(
            model=settings.LLM_MODEL,
            temperature=settings.LLM_TEMPERATURE,
        )
```

### 3.2 Model Settings

```python
# Recommended settings for Qwen-72B

MODEL_CONFIG = {
    "model": "qwen2.5-72b-instruct",
    "temperature": 0.1,      # Low for factual answers
    "max_tokens": 4096,      # Enough for detailed responses
    "top_p": 0.9,
    "stop": ["Observation:", "Human:"],  # ReAct stops
}
```

---

## 4. Agentic Framework

### 4.1 ReAct Pattern

The agent uses **ReAct (Reasoning + Acting)** pattern:

```
┌─────────────────────────────────────────────────────────────┐
│                   REACT REASONING LOOP                      │
│                                                             │
│   Question: "Kenapa margin bulan ini turun?"                │
│                                                             │
│   ┌───────────────────────────────────────────────────────┐ │
│   │ Thought: Perlu cek data COGS bulan ini               │ │
│   │ Action: sql_query                                     │ │
│   │ Action Input: SELECT SUM(cogs) FROM t_so WHERE...     │ │
│   │ Observation: COGS = Rp 4.5M (naik 15% dari bulan lalu)│ │
│   └───────────────────────────────────────────────────────┘ │
│                          ↓                                  │
│   ┌───────────────────────────────────────────────────────┐ │
│   │ Thought: COGS naik, perlu cek harga material          │ │
│   │ Action: sql_query                                     │ │
│   │ Action Input: SELECT item, price FROM m_material...   │ │
│   │ Observation: Tembaga naik 20%, Besi naik 5%           │ │
│   └───────────────────────────────────────────────────────┘ │
│                          ↓                                  │
│   ┌───────────────────────────────────────────────────────┐ │
│   │ Thought: Root cause ditemukan, bisa simpulkan         │ │
│   │ Final Answer: Margin turun 5% karena kenaikan harga   │ │
│   │               Tembaga dunia sebesar 20%.              │ │
│   └───────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
```

### 4.2 Agent Orchestrator

```python
# app/agent/orchestrator.py

from langchain.agents import AgentExecutor, create_react_agent
from langchain.prompts import PromptTemplate
from langchain.memory import ConversationBufferWindowMemory
from app.agent.llm_client import get_llm
from app.tools import sql_tool, rag_tool, calculator_tool, alert_tool

class AgentOrchestrator:
    def __init__(self):
        self.llm = get_llm()
        self.tools = [
            sql_tool.SQLQueryTool(),
            rag_tool.RAGSearchTool(),
            calculator_tool.CalculatorTool(),
            alert_tool.AlertTool(),
        ]
        self.memory = ConversationBufferWindowMemory(
            k=5,  # Keep last 5 exchanges
            memory_key="chat_history",
            return_messages=True,
        )
        
        self.agent = create_react_agent(
            llm=self.llm,
            tools=self.tools,
            prompt=self._get_prompt(),
        )
        
        self.executor = AgentExecutor(
            agent=self.agent,
            tools=self.tools,
            memory=self.memory,
            max_iterations=5,
            early_stopping_method="generate",
            handle_parsing_errors=True,
            verbose=True,
        )
    
    async def chat(self, query: str, context: dict) -> dict:
        """Process user query and return response."""
        try:
            result = await self.executor.ainvoke({
                "input": query,
                "context": context,
            })
            
            return {
                "answer": result["output"],
                "intermediate_steps": self._format_steps(result),
                "confidence": self._calculate_confidence(result),
            }
        except Exception as e:
            return {
                "answer": f"Maaf, terjadi error: {str(e)}",
                "confidence": 0.0,
            }
    
    def _get_prompt(self) -> PromptTemplate:
        return PromptTemplate.from_file("prompts/react_agent.txt")
```

### 4.3 ReAct Prompt Template

```text
# prompts/react_agent.txt

You are the **Sentient Factory Brain**, an AI assistant for manufacturing operations.

## Rules:
1. **Data-Driven**: Only answer based on tool results. Never guess.
2. **Language**: Use Indonesian (Bahasa Indonesia) for answers.
3. **Safety**: Never reveal raw SQL or sensitive data.

## Available Tools:
{tools}

## Format:
Question: the input question
Thought: think about what to do
Action: the action to take, must be one of [{tool_names}]
Action Input: the input to the action
Observation: the result of the action
... (repeat Thought/Action/Observation as needed)
Thought: I now know the final answer
Final Answer: the final answer to the question

## Context:
User Role: {user_role}
Current Page: {current_page}

## Chat History:
{chat_history}

## Begin!

Question: {input}
Thought: {agent_scratchpad}
```

---

## 5. Tools Implementation

### 5.1 SQL Query Tool

```python
# app/tools/sql_tool.py

from langchain.tools import BaseTool
from pydantic import BaseModel, Field
from app.database.connection import get_db_pool
import asyncpg

class SQLQueryInput(BaseModel):
    query: str = Field(description="SQL SELECT query to execute")

class SQLQueryTool(BaseTool):
    name = "sql_query"
    description = """
    Execute SQL query against PostgreSQL database.
    ONLY SELECT queries allowed. Tables available:
    - m_cust (customers): id, name, segment
    - m_item (items): id, code, name, category, sell_price
    - t_so (sales orders): id, cust_id, order_dt, total_amt, status
    - t_so_detail: so_id, item_id, qty, price, subtotal
    - t_po (purchase orders): id, vendor_id, total_amt, status
    - t_wo (work orders): id, item_id, machine_id, target_qty, good_qty
    - t_inv (inventory): item_id, wh_id, qty_on_hand, qty_available
    - ai_alert: id, alert_type, severity, title, description
    
    Use this tool to get business data.
    """
    args_schema = SQLQueryInput
    
    async def _arun(self, query: str) -> str:
        # Validate query (only SELECT allowed)
        if not query.strip().upper().startswith("SELECT"):
            return "Error: Only SELECT queries allowed."
        
        # Prevent dangerous operations
        forbidden = ["DROP", "DELETE", "UPDATE", "INSERT", "TRUNCATE", "ALTER"]
        if any(word in query.upper() for word in forbidden):
            return "Error: Dangerous operation not allowed."
        
        try:
            pool = await get_db_pool()
            async with pool.acquire() as conn:
                rows = await conn.fetch(query)
                
                if not rows:
                    return "No data found."
                
                # Format as markdown table
                headers = list(rows[0].keys())
                result = "| " + " | ".join(headers) + " |\n"
                result += "| " + " | ".join(["---"] * len(headers)) + " |\n"
                
                for row in rows[:20]:  # Limit to 20 rows
                    values = [str(v) for v in row.values()]
                    result += "| " + " | ".join(values) + " |\n"
                
                if len(rows) > 20:
                    result += f"\n... and {len(rows) - 20} more rows"
                
                return result
                
        except Exception as e:
            return f"Error: {str(e)}"
```

### 5.2 RAG Search Tool

```python
# app/tools/rag_tool.py

from langchain.tools import BaseTool
from langchain_community.vectorstores import Chroma
from langchain_community.embeddings import HuggingFaceEmbeddings
from pydantic import BaseModel, Field
from app.config import settings

class RAGSearchInput(BaseModel):
    query: str = Field(description="Search query for documents")

class RAGSearchTool(BaseTool):
    name = "rag_search"
    description = """
    Search through company documents (manuals, SOPs, policies).
    Use this for:
    - Machine troubleshooting guides
    - Standard operating procedures
    - Company policies
    - Product specifications
    """
    args_schema = RAGSearchInput
    
    def __init__(self):
        super().__init__()
        self.embeddings = HuggingFaceEmbeddings(
            model_name="sentence-transformers/paraphrase-multilingual-MiniLM-L12-v2"
        )
        self.vectorstore = Chroma(
            persist_directory=settings.CHROMA_PATH,
            embedding_function=self.embeddings,
        )
    
    async def _arun(self, query: str) -> str:
        try:
            docs = self.vectorstore.similarity_search(query, k=3)
            
            if not docs:
                return "No relevant documents found."
            
            result = "## Relevant Documents:\n\n"
            for i, doc in enumerate(docs, 1):
                result += f"### Source {i}: {doc.metadata.get('source', 'Unknown')}\n"
                result += f"{doc.page_content}\n\n"
            
            return result
            
        except Exception as e:
            return f"Error searching documents: {str(e)}"
```

### 5.3 Calculator Tool

```python
# app/tools/calculator_tool.py

from langchain.tools import BaseTool
from pydantic import BaseModel, Field
import ast
import operator

class CalculatorInput(BaseModel):
    expression: str = Field(description="Math expression to evaluate")

class CalculatorTool(BaseTool):
    name = "calculator"
    description = """
    Perform mathematical calculations.
    Use for:
    - Business math (margin, percentage, growth rate)
    - Currency calculations
    - Statistical operations
    
    Examples:
    - "1500000 * 0.11" (calculate tax)
    - "(5000000 - 4500000) / 5000000 * 100" (calculate margin %)
    """
    args_schema = CalculatorInput
    
    # Safe operators
    OPERATORS = {
        ast.Add: operator.add,
        ast.Sub: operator.sub,
        ast.Mult: operator.mul,
        ast.Div: operator.truediv,
        ast.Pow: operator.pow,
        ast.USub: operator.neg,
    }
    
    async def _arun(self, expression: str) -> str:
        try:
            # Parse and evaluate safely
            tree = ast.parse(expression, mode='eval')
            result = self._eval_node(tree.body)
            
            # Format result
            if isinstance(result, float):
                if result >= 1000000:
                    return f"Rp {result:,.0f}"
                elif result < 1:
                    return f"{result:.2%}"
                else:
                    return f"{result:,.2f}"
            
            return str(result)
            
        except Exception as e:
            return f"Error: {str(e)}"
    
    def _eval_node(self, node):
        if isinstance(node, ast.Constant):
            return node.value
        elif isinstance(node, ast.BinOp):
            left = self._eval_node(node.left)
            right = self._eval_node(node.right)
            return self.OPERATORS[type(node.op)](left, right)
        elif isinstance(node, ast.UnaryOp):
            operand = self._eval_node(node.operand)
            return self.OPERATORS[type(node.op)](operand)
        else:
            raise TypeError(f"Unsupported type: {type(node)}")
```

### 5.4 Alert Tool

```python
# app/tools/alert_tool.py

from langchain.tools import BaseTool
from pydantic import BaseModel, Field
from app.database.connection import get_db_pool
from datetime import datetime
import uuid

class AlertInput(BaseModel):
    alert_type: str = Field(description="Type: LowStock, Overdue, HighReject, etc.")
    severity: str = Field(description="Severity: low, medium, high, critical")
    entity_type: str = Field(description="Entity: SO, PO, WO, Customer, Machine")
    entity_id: int = Field(description="Entity ID")
    title: str = Field(description="Alert title")
    description: str = Field(description="Detailed description")

class AlertTool(BaseTool):
    name = "trigger_alert"
    description = """
    Create an alert notification in the system.
    Use when anomaly or action needed is detected.
    
    Alert types:
    - LowStock: Inventory below minimum
    - Overdue: Payment/delivery overdue
    - HighReject: Production reject rate high
    - MachineDown: Equipment failure
    - CashflowCritical: Cash balance critical
    """
    args_schema = AlertInput
    
    async def _arun(
        self,
        alert_type: str,
        severity: str,
        entity_type: str,
        entity_id: int,
        title: str,
        description: str
    ) -> str:
        try:
            pool = await get_db_pool()
            async with pool.acquire() as conn:
                alert_uuid = str(uuid.uuid4())
                await conn.execute("""
                    INSERT INTO ai_alert 
                    (uuid, alert_type, severity, entity_type, entity_id, title, description)
                    VALUES ($1, $2, $3, $4, $5, $6, $7)
                """, alert_uuid, alert_type, severity, entity_type, entity_id, title, description)
                
                return f"✅ Alert created: {title} (ID: {alert_uuid[:8]})"
                
        except Exception as e:
            return f"Error creating alert: {str(e)}"
```

---

## 6. NATS Integration

### 6.1 NATS Client

```python
# app/messaging/nats_client.py

import nats
from nats.aio.client import Client as NATSClient
from app.config import settings
import asyncio
import json

class NATSService:
    def __init__(self):
        self.nc: NATSClient = None
        self.handlers = {}
    
    async def connect(self):
        self.nc = await nats.connect(settings.NATS_URL)
        print(f"Connected to NATS at {settings.NATS_URL}")
    
    async def disconnect(self):
        if self.nc:
            await self.nc.close()
    
    def register_handler(self, subject: str, handler):
        self.handlers[subject] = handler
    
    async def start_listening(self):
        for subject, handler in self.handlers.items():
            await self.nc.subscribe(subject, cb=self._create_callback(handler))
            print(f"Subscribed to: {subject}")
    
    def _create_callback(self, handler):
        async def callback(msg):
            try:
                data = json.loads(msg.data.decode())
                result = await handler(data)
                
                if msg.reply:
                    await self.nc.publish(
                        msg.reply,
                        json.dumps(result).encode()
                    )
            except Exception as e:
                if msg.reply:
                    await self.nc.publish(
                        msg.reply,
                        json.dumps({"error": str(e)}).encode()
                    )
        return callback
```

### 6.2 Message Handlers

```python
# app/messaging/handlers.py

from app.agent.orchestrator import AgentOrchestrator
from app.models.request import ChatRequest, InsightRequest
from app.models.response import ChatResponse, InsightResponse

agent = AgentOrchestrator()

async def handle_chat(data: dict) -> dict:
    """Handle ai.chat messages from NestJS."""
    request = ChatRequest(**data)
    
    result = await agent.chat(
        query=request.message,
        context={
            "user_role": request.user_role,
            "current_page": request.current_page,
        }
    )
    
    return ChatResponse(
        answer=result["answer"],
        confidence=result.get("confidence", 0.85),
        sources=result.get("sources", []),
        reasoning_steps=result.get("intermediate_steps", []),
    ).model_dump()


async def handle_insights(data: dict) -> dict:
    """Generate AI insights for dashboard."""
    # Query for anomalies
    insights = []
    
    # Check low stock
    low_stock_query = """
        SELECT i.name, inv.qty_available, i.min_stock
        FROM t_inv inv
        JOIN m_item i ON inv.item_id = i.id
        WHERE inv.qty_available < i.min_stock
    """
    # ... execute and analyze
    
    return {"insights": insights}


async def handle_alert_check(data: dict) -> dict:
    """Periodic check for alert conditions."""
    alerts_created = []
    
    # Check various conditions
    # - Low stock items
    # - Overdue payments
    # - High reject rates
    # - Pending approvals
    
    return {"alerts_created": len(alerts_created)}
```

### 6.3 Main Entry Point

```python
# app/main.py

import asyncio
from app.messaging.nats_client import NATSService
from app.messaging.handlers import handle_chat, handle_insights, handle_alert_check
from app.database.connection import init_db_pool, close_db_pool

async def main():
    # Initialize database pool
    await init_db_pool()
    
    # Initialize NATS
    nats_service = NATSService()
    await nats_service.connect()
    
    # Register handlers
    nats_service.register_handler("ai.chat", handle_chat)
    nats_service.register_handler("ai.insights", handle_insights)
    nats_service.register_handler("ai.alert.check", handle_alert_check)
    
    # Start listening
    await nats_service.start_listening()
    
    print("🐍 Python AI Service running...")
    
    # Keep running
    try:
        while True:
            await asyncio.sleep(1)
    except KeyboardInterrupt:
        print("Shutting down...")
    finally:
        await nats_service.disconnect()
        await close_db_pool()

if __name__ == "__main__":
    asyncio.run(main())
```

---

## 7. Prompt Engineering

### 7.1 Global Persona

```text
# prompts/global_persona.txt

You are the **'Sentient Factory Brain'**, an advanced AI assistant for a Manufacturing Company.
Your goal is to maximize **Operational Efficiency** and **Profitability**.

**Core Rules:**
1. **Data-Driven**: Never guess. Base your answer ONLY on the provided Context. If data is missing, ask for it.
2. **Local Context**: Use Indonesian Language (Bahasa Indonesia) typical of factory professionals (Formal but practical).
3. **Security**: Do not reveal raw SQL queries or sensitive employee PII unless authorized.
4. **Safety First**: Never suggest bypassing safety procedures, even for efficiency.
```

### 7.2 Module-Specific Prompts

```python
# app/agent/prompts.py

PROMPTS = {
    "sales_quotation": """
Role: You are a **Commercial Strategist**.
Task: Analyze the winning probability of the following quotation request.

Context:
Customer: {customer_name}
Item: {item_name}
Qty: {qty}
Requested Price: {price}
History: Last 3 deals with this customer were at margin {margin_history}%.

Analyze the Risk vs Reward.
Output format:
- **Recommendation**: (Accept/Reject/Negotiate)
- **Winning Probability**: (0-100%)
- **Reasoning**: (One sentence why)
""",

    "maintenance_guide": """
Role: You are a **Senior Maintenance Engineer**.
Task: Diagnose the machine error based on the technical manual contexts provided.

Error Code: {error_code}
Machine: {machine_type}
Manual Excerpts:
{manual_excerpts}

Provide a step-by-step troubleshooting guide.
Style: Technical, Concise, Bullet points.
Warning: Always remind to turn off power before checking.
""",

    "executive_brief": """
Role: You are an **Executive Assistant**.
Task: Summarize yesterday's factory performance for the CEO.

Yesterday Stats:
- Revenue: {revenue}
- Output: {output_qty} (Target: {target_qty})
- Critical Issues: {issues}

Use **BLUF (Bottom Line Up Front)** format.
Structure:
1. **Financial Impact**: (Revenue & Profit estimation)
2. **Operational Pulse**: (Green/Red flags)
3. **Action Item**: (What needs CEO attention today)

Keep it under 150 words suitable for WhatsApp.
""",
}
```

### 7.3 JSON Enforcer

```text
# prompts/json_enforcer.txt

IMPORTANT: You must output **ONLY valid JSON**.
No markdown, no conversation, no 'Here is your JSON'.

Structure:
{
  "status": "ok" | "error",
  "data": { ... },
  "reasoning": "..."
}
```

---

## 8. Safety Guards

### 8.1 Hallucination Prevention

```python
# app/agent/guards.py

HALLUCINATION_GUARD = """
Knowledge Constraint:
You only know facts provided in the [Context] or retrieved from tools.
If the answer is not available, state: **'Maaf, data tidak tersedia di sistem.'**
DO NOT make up numbers or guess.
"""

def validate_answer(answer: str, sources: list) -> bool:
    """Validate that answer is based on actual sources."""
    if not sources:
        # No sources means AI might be hallucinating
        return False
    
    # Check for speculation markers
    speculation_words = ["mungkin", "sepertinya", "kira-kira", "kemungkinan"]
    for word in speculation_words:
        if word in answer.lower():
            return False
    
    return True
```

### 8.2 Safety First Rule

```python
SAFETY_GUARD = """
CRITICAL RULE: **Safety First**.
If a user asks how to speed up a machine, you MUST first check safety limits.
NEVER suggest:
- Bypassing sensors or interlocks
- Ignoring LOTO (Lock-Out Tag-Out) procedures
- Overriding safety limits for production targets

If a request violates safety, REFUSE immediately.
"""
```

### 8.3 HR Empathy Filter

```python
HR_EMPATHY_GUARD = """
Tone Rule: When discussing employee performance, maintain **Objectivity and Empathy**.

DO NOT use words like:
- 'Fire', 'Kick', 'Useless', 'Lazy'

INSTEAD use:
- 'Evaluation needed'
- 'Training required'
- 'Performance gap'

Remember: Low output might be caused by machine fault, not just human error.
"""
```

---

## 9. Response Models

### 9.1 Request Models

```python
# app/models/request.py

from pydantic import BaseModel, Field
from typing import Optional

class ChatRequest(BaseModel):
    message: str = Field(..., description="User's question")
    session_id: Optional[str] = Field(None, description="Chat session ID")
    user_id: str = Field(..., description="User UUID")
    user_role: str = Field("Staff", description="User's role")
    current_page: Optional[str] = Field(None, description="Current page context")

class InsightRequest(BaseModel):
    insight_types: list[str] = Field(
        default=["anomaly", "trend", "recommendation"],
        description="Types of insights to generate"
    )
```

### 9.2 Response Models

```python
# app/models/response.py

from pydantic import BaseModel, Field
from typing import Optional, Any

class ReasoningStep(BaseModel):
    thought: str
    action: str
    action_input: str
    observation: str

class ChatResponse(BaseModel):
    answer: str
    confidence: float = Field(ge=0, le=1)
    sources: list[str] = Field(default_factory=list)
    charts: Optional[list[dict]] = None
    reasoning_steps: list[ReasoningStep] = Field(default_factory=list)
    suggested_actions: list[str] = Field(default_factory=list)

class InsightResponse(BaseModel):
    type: str  # anomaly, trend, recommendation, risk
    severity: str  # low, medium, high, critical
    title: str
    description: str
    data: Optional[dict] = None
    recommendation: Optional[str] = None
```

---

## 10. Example Usage

### 10.1 Chat Flow

```python
# Example: User asks about margin drop

# 1. NestJS sends message to NATS
# Topic: ai.chat
# Payload:
{
    "message": "Kenapa margin bulan ini turun?",
    "user_id": "550e8400-e29b-41d4-a716-446655440000",
    "user_role": "Sales Manager",
    "current_page": "dashboard"
}

# 2. Python Agent processes with ReAct loop
# - Thought: Need to check COGS data
# - Action: sql_query
# - Observation: COGS increased 15%
# - Thought: Need to find root cause  
# - Action: sql_query (material prices)
# - Observation: Copper price up 20%
# - Final Answer: Margin dropped due to copper price increase

# 3. Response sent back
{
    "answer": "Margin bulan ini turun 5% karena kenaikan harga Tembaga dunia sebesar 20%. COGS meningkat 15% dibanding bulan lalu.",
    "confidence": 0.92,
    "sources": ["t_so", "m_material"],
    "charts": [
        {
            "type": "line",
            "title": "COGS Trend",
            "data": [...]
        }
    ],
    "reasoning_steps": [
        {
            "thought": "Perlu cek data COGS bulan ini",
            "action": "sql_query",
            "action_input": "SELECT SUM(cogs)...",
            "observation": "COGS = Rp 4.5B (naik 15%)"
        },
        {
            "thought": "Perlu cari penyebab kenaikan COGS",
            "action": "sql_query",
            "action_input": "SELECT material, price...",
            "observation": "Tembaga naik 20%"
        }
    ],
    "suggested_actions": [
        "Lihat detail breakdown per material",
        "Bandingkan dengan bulan sebelumnya",
        "Cari vendor alternatif"
    ]
}
```

### 10.2 Running the Service

```bash
# Start the service
python -m app.main

# Output:
# Connected to NATS at nats://localhost:4222
# Subscribed to: ai.chat
# Subscribed to: ai.insights  
# Subscribed to: ai.alert.check
# 🐍 Python AI Service running...
```

---

*Document Version: 1.0*
*Last Updated: 2024-12-27*
