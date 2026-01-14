# ☁️ Cloud AI Setup: Google Gemini

> **Purpose:** Development testing menggunakan Gemini AI sebelum Mac Mini M4 tiba
> **Provider:** Google AI (Gemini)
> **Last Updated:** 2024-12-27

---

## 1. Overview

Gemini adalah pilihan **development/testing** yang ideal karena:
- ✅ Free tier generous (1500 req/day untuk Gemini 1.5 Flash)
- ✅ API compatible dengan OpenAI format
- ✅ Mudah di-switch ke local LLM nanti
- ✅ Multilingual (support Bahasa Indonesia)

### Model Options

| Model                    | Speed  | Context   | Best For               |
| ------------------------ | ------ | --------- | ---------------------- |
| **gemini-2.0-flash-exp** | Fast   | 1M tokens | Development, testing   |
| **gemini-1.5-flash**     | Fast   | 1M tokens | Production (free tier) |
| **gemini-1.5-pro**       | Slower | 2M tokens | Complex reasoning      |

---

## 2. Get API Key

### 2.1 Via Google AI Studio

1. Buka [Google AI Studio](https://aistudio.google.com/)
2. Login dengan Google Account
3. Klik **"Get API Key"** di sidebar
4. Klik **"Create API Key"**
5. Copy dan simpan key (format: `AIza...`)

### 2.2 Via Google Cloud (Production)

```bash
# Install gcloud CLI
brew install google-cloud-sdk

# Login
gcloud auth login

# Create project
gcloud projects create sentient-factory

# Enable Gemini API
gcloud services enable generativelanguage.googleapis.com

# Create API key
gcloud alpha services api-keys create --display-name="Sentient AI"
```

---

## 3. Python Setup

### 3.1 Install Dependencies

```bash
# Create virtual environment
python -m venv venv
source venv/bin/activate

# Install Google Generative AI SDK
pip install google-generativeai

# Install LangChain integration
pip install langchain-google-genai

# Install other dependencies
pip install pydantic python-dotenv
```

### 3.2 Environment Configuration

```env
# .env

# Gemini API Key
GEMINI_API_KEY=AIzaSyB6mT5YVLmjSgYiWTCJmZfXpOmcv0GtSBo

# Model Configuration
GEMINI_MODEL=gemini-2.0-flash-exp
GEMINI_TEMPERATURE=0.1
GEMINI_MAX_TOKENS=4096
```

---

## 4. Basic Usage

### 4.1 Direct SDK Usage

```python
# basic_gemini.py

import google.generativeai as genai
from dotenv import load_dotenv
import os

load_dotenv()

# Configure API key
genai.configure(api_key=os.getenv("GEMINI_API_KEY"))

# Initialize model
model = genai.GenerativeModel(
    model_name="gemini-2.0-flash-exp",
    generation_config={
        "temperature": 0.1,
        "max_output_tokens": 4096,
    },
    system_instruction="""
    You are the 'Sentient Factory Brain', an AI assistant for manufacturing.
    Answer in Indonesian (Bahasa Indonesia).
    Be data-driven, never guess.
    """
)

# Simple query
response = model.generate_content("Jelaskan cara meningkatkan efisiensi pabrik")
print(response.text)
```

### 4.2 Chat Session

```python
# chat_session.py

import google.generativeai as genai
from dotenv import load_dotenv
import os

load_dotenv()
genai.configure(api_key=os.getenv("GEMINI_API_KEY"))

model = genai.GenerativeModel("gemini-2.0-flash-exp")

# Start chat session (maintains history)
chat = model.start_chat(history=[])

# First message
response1 = chat.send_message("Saya manajer pabrik, revenue bulan ini turun 15%")
print("AI:", response1.text)

# Follow-up (context maintained)
response2 = chat.send_message("Apa penyebab utamanya?")
print("AI:", response2.text)

# Another follow-up
response3 = chat.send_message("Bagaimana cara memperbaikinya?")
print("AI:", response3.text)
```

---

## 5. LangChain Integration

### 5.1 Basic LangChain

```python
# langchain_gemini.py

from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_core.messages import HumanMessage, SystemMessage
from dotenv import load_dotenv
import os

load_dotenv()

# Initialize LLM
llm = ChatGoogleGenerativeAI(
    model="gemini-2.0-flash-exp",
    google_api_key=os.getenv("GEMINI_API_KEY"),
    temperature=0.1,
    max_output_tokens=4096,
)

# With system message
messages = [
    SystemMessage(content="""
        You are the 'Sentient Factory Brain'.
        Answer in Indonesian.
        Be concise and data-driven.
    """),
    HumanMessage(content="Berapa revenue bulan ini?"),
]

response = llm.invoke(messages)
print(response.content)
```

### 5.2 With ReAct Agent

```python
# react_agent_gemini.py

from langchain_google_genai import ChatGoogleGenerativeAI
from langchain.agents import AgentExecutor, create_react_agent
from langchain_core.tools import tool
from langchain import hub
from dotenv import load_dotenv
import os

load_dotenv()

# Initialize LLM
llm = ChatGoogleGenerativeAI(
    model="gemini-2.0-flash-exp",
    google_api_key=os.getenv("GEMINI_API_KEY"),
    temperature=0.1,
)

# Define tools
@tool
def get_revenue(month: str) -> str:
    """Get revenue for a specific month. Input: month name (e.g., 'December 2024')"""
    # Simulated data
    data = {
        "December 2024": "Rp 5,234,000,000",
        "November 2024": "Rp 4,890,000,000",
    }
    return data.get(month, "Data tidak tersedia")

@tool
def get_top_customers(limit: int = 5) -> str:
    """Get top customers by revenue. Input: number of customers to return."""
    # Simulated data
    return """
    1. PT ABC Manufacturing: Rp 890,000,000
    2. CV XYZ Trading: Rp 756,000,000
    3. PT Maju Jaya: Rp 650,000,000
    """

@tool
def calculator(expression: str) -> str:
    """Calculate mathematical expression. Input: math expression like '100 * 0.15'"""
    try:
        result = eval(expression)
        return str(result)
    except:
        return "Error in calculation"

tools = [get_revenue, get_top_customers, calculator]

# Get ReAct prompt
prompt = hub.pull("hwchase17/react")

# Create agent
agent = create_react_agent(llm, tools, prompt)
agent_executor = AgentExecutor(
    agent=agent,
    tools=tools,
    verbose=True,
    max_iterations=5,
)

# Run query
result = agent_executor.invoke({
    "input": "Berapa revenue December 2024? Dan siapa top 3 customer?"
})
print("\n=== FINAL ANSWER ===")
print(result["output"])
```

---

## 6. Integration with Python AI Service

### 6.1 Update Config

```python
# app/config.py

from pydantic_settings import BaseSettings
from typing import Literal

class Settings(BaseSettings):
    # LLM Provider - Easy switch between Gemini and Ollama
    LLM_PROVIDER: Literal["gemini", "ollama"] = "gemini"
    
    # Gemini Settings (Development)
    GEMINI_API_KEY: str = ""
    GEMINI_MODEL: str = "gemini-2.0-flash-exp"
    
    # Ollama Settings (Production - Mac Mini)
    OLLAMA_BASE_URL: str = "http://localhost:11434"
    OLLAMA_MODEL: str = "sentient-brain"
    
    # Common Settings
    LLM_TEMPERATURE: float = 0.1
    LLM_MAX_TOKENS: int = 4096
    
    class Config:
        env_file = ".env"

settings = Settings()
```

### 6.2 LLM Client Factory

```python
# app/agent/llm_client.py

from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_community.chat_models import ChatOllama
from app.config import settings

def get_llm():
    """
    Factory function to get LLM based on configuration.
    Easily switch between Gemini (dev) and Ollama (prod).
    """
    if settings.LLM_PROVIDER == "gemini":
        return ChatGoogleGenerativeAI(
            model=settings.GEMINI_MODEL,
            google_api_key=settings.GEMINI_API_KEY,
            temperature=settings.LLM_TEMPERATURE,
            max_output_tokens=settings.LLM_MAX_TOKENS,
        )
    else:  # ollama
        return ChatOllama(
            model=settings.OLLAMA_MODEL,
            base_url=settings.OLLAMA_BASE_URL,
            temperature=settings.LLM_TEMPERATURE,
        )
```

### 6.3 Full Agent with Gemini

```python
# app/agent/orchestrator.py

from langchain.agents import AgentExecutor, create_react_agent
from langchain.prompts import PromptTemplate
from langchain.memory import ConversationBufferWindowMemory
from app.agent.llm_client import get_llm
from app.tools import sql_tool, rag_tool, calculator_tool

class AgentOrchestrator:
    def __init__(self):
        self.llm = get_llm()  # Works with Gemini or Ollama
        self.tools = [
            sql_tool.SQLQueryTool(),
            rag_tool.RAGSearchTool(),
            calculator_tool.CalculatorTool(),
        ]
        self.memory = ConversationBufferWindowMemory(k=5)
        
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
            verbose=True,
        )
    
    async def chat(self, query: str, context: dict) -> dict:
        result = await self.executor.ainvoke({
            "input": query,
            "user_role": context.get("user_role", "Staff"),
        })
        return {
            "answer": result["output"],
            "confidence": 0.85,
        }
    
    def _get_prompt(self) -> PromptTemplate:
        return PromptTemplate.from_template("""
You are the 'Sentient Factory Brain', an AI assistant for manufacturing.

Rules:
1. Answer in Indonesian (Bahasa Indonesia)
2. Be data-driven, use tools to get data
3. Never guess, if data not available say so

Available tools:
{tools}

Tool names: {tool_names}

Format:
Question: the input question
Thought: think about what to do
Action: tool name
Action Input: tool input
Observation: tool result
... (repeat as needed)
Thought: I now know the answer
Final Answer: the answer in Indonesian

User Role: {user_role}
Chat History: {chat_history}

Question: {input}
{agent_scratchpad}
""")
```

---

## 7. Function Calling (Advanced)

Gemini supports native function calling:

```python
# function_calling.py

import google.generativeai as genai
from dotenv import load_dotenv
import os

load_dotenv()
genai.configure(api_key=os.getenv("GEMINI_API_KEY"))

# Define functions
get_revenue_func = genai.protos.FunctionDeclaration(
    name="get_revenue",
    description="Get revenue data for a specific period",
    parameters=genai.protos.Schema(
        type=genai.protos.Type.OBJECT,
        properties={
            "month": genai.protos.Schema(type=genai.protos.Type.STRING),
            "year": genai.protos.Schema(type=genai.protos.Type.INTEGER),
        },
        required=["month", "year"]
    )
)

check_stock_func = genai.protos.FunctionDeclaration(
    name="check_stock",
    description="Check inventory stock level for an item",
    parameters=genai.protos.Schema(
        type=genai.protos.Type.OBJECT,
        properties={
            "item_code": genai.protos.Schema(type=genai.protos.Type.STRING),
            "warehouse": genai.protos.Schema(type=genai.protos.Type.STRING),
        },
        required=["item_code"]
    )
)

# Create tool
tools = genai.protos.Tool(
    function_declarations=[get_revenue_func, check_stock_func]
)

# Initialize model with tools
model = genai.GenerativeModel(
    model_name="gemini-2.0-flash-exp",
    tools=[tools],
)

# Query
response = model.generate_content("Berapa revenue bulan Desember 2024?")

# Check if function call requested
if response.candidates[0].content.parts[0].function_call:
    func_call = response.candidates[0].content.parts[0].function_call
    print(f"Function: {func_call.name}")
    print(f"Args: {dict(func_call.args)}")
    
    # Execute function and send result back
    # ... implementation
```

---

## 8. Rate Limits & Pricing

### Free Tier (AI Studio)

| Model                | RPM | RPD   | TPM |
| -------------------- | --- | ----- | --- |
| gemini-1.5-flash     | 15  | 1,500 | 1M  |
| gemini-2.0-flash-exp | 10  | 1,500 | 4M  |
| gemini-1.5-pro       | 2   | 50    | 32K |

### Paid Tier (Vertex AI)

| Model            | Input (per 1M tokens) | Output (per 1M tokens) |
| ---------------- | --------------------- | ---------------------- |
| gemini-1.5-flash | $0.075                | $0.30                  |
| gemini-1.5-pro   | $1.25                 | $5.00                  |

---

## 9. Migration to Local LLM

Ketika Mac Mini M4 tiba, switch dengan mudah:

```env
# .env - Development (Gemini)
LLM_PROVIDER=gemini
GEMINI_API_KEY=AIzaSy...

# .env - Production (Ollama)
LLM_PROVIDER=ollama
OLLAMA_BASE_URL=http://localhost:11434
OLLAMA_MODEL=sentient-brain
```

```python
# Kode tidak perlu diubah!
llm = get_llm()  # Automatically uses correct provider
```

---

## 10. Testing Checklist

```bash
# 1. Test API connection
python -c "
import google.generativeai as genai
genai.configure(api_key='YOUR_KEY')
model = genai.GenerativeModel('gemini-2.0-flash-exp')
print(model.generate_content('Hello').text)
"

# 2. Test LangChain integration
python -c "
from langchain_google_genai import ChatGoogleGenerativeAI
llm = ChatGoogleGenerativeAI(model='gemini-2.0-flash-exp', google_api_key='YOUR_KEY')
print(llm.invoke('Hello').content)
"

# 3. Test agent with tools
python react_agent_gemini.py
```

---

*Document Version: 1.0*
*Last Updated: 2024-12-27*
