# Local Qwen3 AI Agent

An autonomous AI agent powered by **Qwen3-8B**, running locally on a GPU and exposed through a **FastAPI REST API**.

The project demonstrates how to build an AI agent from the ground up without relying on OpenAI, Gemini, or other hosted LLM APIs.

The agent can reason about a user's request, decide when to use tools, execute those tools, incorporate the results, and return a final answer.

---

## 🚀 Features

- Local **Qwen3-8B** Large Language Model
- GPU acceleration with PyTorch
- Autonomous agent loop
- Tool selection and execution
- Calculator tool
- Web search tool
- Short-term conversation memory
- Multi-step reasoning workflow
- FastAPI REST API
- Health-check endpoint
- Structured API responses
- Cloudflare Quick Tunnel support
- Docker deployment configuration
- Designed for future cloud GPU deployment

---

# 🏗️ Architecture

```text
                         User
                           │
                           ▼
                    ┌─────────────┐
                    │   FastAPI   │
                    │   REST API  │
                    └──────┬──────┘
                           │
                           ▼
                    ┌─────────────┐
                    │ AI Agent    │
                    │ Controller  │
                    └──────┬──────┘
                           │
                 ┌─────────┴─────────┐
                 │                   │
                 ▼                   ▼
          ┌─────────────┐     ┌──────────────┐
          │ Qwen3-8B    │     │ Tool Registry│
          │ Local LLM   │     └──────┬───────┘
          └──────┬──────┘            │
                 │          ┌────────┴────────┐
                 │          │                 │
                 │          ▼                 ▼
                 │    Calculator        Web Search
                 │
                 ▼
             Agent Loop
                 │
                 ▼
             Final Answer
```

---

# 🧠 How the Agent Works

The agent follows a simple autonomous loop:

```text
User Request
     │
     ▼
Qwen3-8B
     │
     ├── Tool required?
     │       │
     │       ├── YES → Execute tool
     │       │             │
     │       │             ▼
     │       │        Tool result
     │       │             │
     │       │             ▼
     │       │        Qwen3-8B
     │       │
     │       └── NO
     │
     ▼
FINAL:
Answer
```

For example:

```text
User:

Calculate 1250 * 48 / 6
```

The agent can decide to use:

```text
TOOL:
calculator

INPUT:
1250 * 48 / 6
```

The calculator returns the result.

The result is then provided back to Qwen3-8B, which produces:

```text
FINAL:
The result is 10000.
```

---

# 🛠️ Technologies

| Technology | Purpose |
|---|---|
| Python | Core application |
| PyTorch | Deep learning framework |
| Qwen3-8B | Local language model |
| Hugging Face Transformers | Model loading and inference |
| FastAPI | REST API |
| Pydantic | Request/response validation |
| DDGS | Web search |
| Uvicorn | ASGI server |
| Docker | Containerization |
| Cloudflare Tunnel | Temporary public HTTPS endpoint |

---

# 📁 Project Structure

```text
ai-agent/
│
├── main.py
│
├── agent.py
│
├── llm.py
│
├── tools.py
│
├── memory.py
│
├── requirements.txt
│
├── Dockerfile
│
└── README.md
```

### `main.py`

FastAPI application and API endpoints.

### `agent.py`

Contains the autonomous agent loop, tool parsing, memory integration, and decision flow.

### `llm.py`

Loads Qwen3-8B and performs local inference using Transformers and PyTorch.

### `tools.py`

Contains the available agent tools:

- Calculator
- Web Search
- Tool registry
- Tool execution

### `memory.py`

Implements short-term conversation memory.

### `requirements.txt`

Python dependencies required by the project.

### `Dockerfile`

Container configuration for GPU-based deployment.

---

# 💻 Hardware

The project was developed and tested using a GPU environment capable of running Qwen3-8B.

Example development environment:

```text
GPU:
NVIDIA A100-SXM4-40GB

VRAM:
~40 GB

Python:
3.x

PyTorch:
CUDA-enabled

Operating System:
Linux / Google Colab
```

The model can also be adapted to other GPUs depending on available VRAM and quantization configuration.

---

# ⚙️ Installation

## 1. Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/local-qwen-ai-agent.git

cd local-qwen-ai-agent
```

---

## 2. Create a virtual environment

```bash
python -m venv venv
```

Activate it on Linux/macOS:

```bash
source venv/bin/activate
```

Windows:

```powershell
venv\Scripts\activate
```

---

## 3. Install dependencies

```bash
pip install -r requirements.txt
```

---

# 🤖 Run the Agent

Start the FastAPI server:

```bash
uvicorn main:app --host 0.0.0.0 --port 8000
```

The API will be available at:

```text
http://localhost:8000
```

---

# ❤️ Health Check

Send a GET request to:

```text
GET /health
```

Example:

```bash
curl http://localhost:8000/health
```

Response:

```json
{
  "status": "healthy",
  "model": "Qwen/Qwen3-8B",
  "agent": "online"
}
```

---

# 🧠 Run the AI Agent

Endpoint:

```text
POST /agent
```

Request:

```json
{
  "task": "Calculate 1250 * 48 / 6"
}
```

Example using Python:

```python
import requests

response = requests.post(
    "http://localhost:8000/agent",
    json={
        "task": "Calculate 1250 * 48 / 6"
    }
)

print(response.json())
```

Example response:

```json
{
  "success": true,
  "answer": "The result is 10000.",
  "tools_used": [
    "calculator"
  ],
  "steps": 2
}
```

---

# 🔎 Web Search

The agent can use the web-search tool when current information is required.

Example:

```json
{
  "task": "Find the latest developments in AI agents."
}
```

The agent can decide to invoke:

```text
web_search
```

and then use the returned search results to formulate the final response.

---

# 🧮 Calculator

The calculator supports mathematical expressions.

Example:

```text
1250 * 48 / 6
```

Other supported operations include:

```text
sqrt(144)
pow(2, 10)
sin(pi / 2)
cos(0)
abs(-25)
round(3.14159, 2)
```

---

# 🧠 Agent Memory

The project includes short-term in-memory conversation history.

Example:

```text
User:
My name is Alex.

Assistant:
Nice to meet you, Alex.

User:
What is my name?

Assistant:
Your name is Alex.
```

Memory is maintained by the `AgentMemory` class.

Current memory is process-based and is cleared when the application restarts.

Future versions can replace this with:

- Redis
- PostgreSQL
- MongoDB
- Vector database
- Long-term semantic memory

---

# 🌐 Public API with Cloudflare Tunnel

During development, the FastAPI service can be exposed temporarily using Cloudflare Quick Tunnel.

Example:

```bash
cloudflared tunnel --url http://127.0.0.1:8000
```

Cloudflare provides a temporary HTTPS address such as:

```text
https://example.trycloudflare.com
```

The public endpoint can then be tested:

```python
import requests

PUBLIC_URL = "https://example.trycloudflare.com"

response = requests.get(
    f"{PUBLIC_URL}/health"
)

print(response.json())
```

### Important

Cloudflare Quick Tunnels are intended for development/testing and are not a production deployment strategy.

---

# 🐳 Docker

The repository includes a Dockerfile for deployment to a GPU-enabled environment.

Build:

```bash
docker build -t local-qwen-agent:1.0 .
```

Run:

```bash
docker run --gpus all \
    -p 8000:8000 \
    local-qwen-agent:1.0
```

The target deployment architecture is:

```text
GPU Server
    │
    ▼
Docker
    │
    ▼
Qwen3-8B Container
    │
    ├── FastAPI
    ├── Agent
    ├── Calculator
    └── Web Search
```

---

# 🔐 Security

The initial prototype was designed for development.

Before production deployment, the following security controls should be added:

- API authentication
- API keys or OAuth
- Rate limiting
- Input validation
- Request size limits
- Structured logging
- Secrets management
- HTTPS
- Tool permission controls
- Network restrictions
- Monitoring and alerting

The project intentionally does **not** expose unrestricted Python code execution through the public API.

---

# 📊 API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| GET | `/` | API information |
| GET | `/health` | Health check |
| POST | `/agent` | Execute an AI-agent task |
| GET | `/docs` | Interactive Swagger API documentation |

FastAPI automatically provides interactive documentation at:

```text
http://localhost:8000/docs
```

---

# 🔄 Agent Execution Flow

```text
1. User sends task
        ↓
2. FastAPI validates request
        ↓
3. Agent receives task
        ↓
4. Qwen3-8B determines next action
        ↓
5. Agent parses tool request
        ↓
6. Tool executes
        ↓
7. Tool result returned to agent
        ↓
8. Qwen3-8B continues solving
        ↓
9. Agent produces FINAL response
        ↓
10. FastAPI returns JSON
```

---

# 🚀 Future Improvements

The project is designed to evolve into a production-grade agent platform.

### Planned improvements

- [ ] Structured function/tool calling
- [ ] Persistent memory
- [ ] Redis integration
- [ ] PostgreSQL integration
- [ ] RAG pipeline
- [ ] Vector database
- [ ] Additional tools
- [ ] Authentication
- [ ] Rate limiting
- [ ] Observability
- [ ] Prometheus metrics
- [ ] Distributed task execution
- [ ] Agent planning
- [ ] Multi-agent collaboration
- [ ] Human-in-the-loop workflows
- [ ] GPU cloud deployment
- [ ] CI/CD pipeline
- [ ] Automated testing
- [ ] Production monitoring

---

# 🧪 Example Tasks

The agent can be tested with tasks such as:

```text
Calculate 1250 * 48 / 6
```

```text
What is the square root of 144?
```

```text
Search for the latest developments in AI agents.
```

```text
Calculate 25^2 and explain the result.
```

```text
Find current information about Qwen3 and summarize it.
```

---

# 🎯 Project Goals

This project demonstrates practical experience with:

- Local LLM deployment
- AI agent architecture
- LLM inference
- Tool calling
- Autonomous task execution
- REST API development
- GPU acceleration
- Containerization
- Cloud deployment architecture
- AI application security
- Agent memory
- MLOps-oriented deployment

---

# 📌 Current Status

```text
Local Qwen3-8B              ✅
GPU inference               ✅
Agent loop                  ✅
Calculator tool             ✅
Web search tool             ✅
Agent memory                ✅
FastAPI API                 ✅
Health endpoint              ✅
Cloudflare public endpoint  ✅
Docker configuration        ✅
Docker execution in Colab  ⚠️ Colab sandbox limitation
Production deployment       🔜
Authentication              🔜
Persistent memory           🔜
CI/CD                       🔜
```

---

# 👨‍💻 Author

**Basil Sajeev**

MSc Computer Science  
AI / Machine Learning / Software Engineering

Interests:

- Artificial Intelligence
- Machine Learning
- Generative AI
- AI Agents
- Large Language Models
- MLOps
- Cloud Computing
- Distributed Systems

---

# 📄 License

This project is intended for educational, research, and portfolio purposes.
