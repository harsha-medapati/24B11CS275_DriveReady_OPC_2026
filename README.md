# 🤖 Agentic AI Lab & System Implementations

[![Python 3.10+](https://img.shields.io/badge/Python-3.10%2B-blue.svg)](https://www.python.org/)
[![LangChain](https://img.shields.io/badge/LangChain-Integration-green.svg)](https://www.langchain.com/)
[![Groq](https://img.shields.io/badge/LLM-Groq%20Cloud-orange.svg)](https://groq.com/)
[![Status](https://img.shields.io/badge/Status-Active%20Development-brightgreen.svg)]()

A structured repository tracking hands-on implementations, agent architectures, and LLM engineering patterns built throughout the Agentic AI series.

---

## 📌 Implementation Roadmap

| Day | Topic | Domain | Primary Model / Tools | Notebook |
| :--- | :--- | :--- | :--- | :--- |
| **Day 01** | LLM Fundamentals & Safety | Sweet Crumbs Bakery | `openai/gpt-oss-120b`, Meta Prompt Guard | [`DAY1/agenticAI_Day1.ipynb`](DAY1/agenticAI_Day1.ipynb) |
| **Day 02** | Prompts, Memory & Intent Agents | BrightPage Stationery | 4-Pillar System Prompt, Sliding Window Memory | [`DAY2/agenticAI_Day2.ipynb`](DAY2/agenticAI_Day2.ipynb) |

---

## 🚀 Module Breakdown

### 🔹 [Day 1] Foundations, Groq Integration & Guardrails
- **Directory:** [`DAY1/`](DAY1/)
- **Target Application:** Sweet Crumbs Bakery customer assistant.
- **Core Topics Covered:**
  - Token economics, sampling temperature ($T=0.2$ vs $T=1.2$), and stateless API mechanics.
  - Groq Cloud inference utilizing LangChain's `ChatGroq`.
  - Input/Output validation pipeline using Meta Llama Prompt Guard (`meta-llama/llama-prompt-guard-2-22m`).
  - Interactive testing cells for latency and token profiling.

### 🔹 [Day 2] In-Context Memory, Structured Extraction & Policies
- **Directory:** [`DAY2/`](DAY2/)
- **Target Application:** BrightPage Stationery & Office Supplies order bot.
- **Core Topics Covered:**
  - **4-Pillar System Calibration:** Role definition, store factual inventory, business logic constraints, and response length formatting.
  - **Few-Shot In-Context Classifier:** Deterministic customer intent tagging (`order` / `inquiry` / `complaint`).
  - **Schema Enforcement:** Converting conversational chat inputs into machine-readable JSON order records.
  - **Manual Sliding-Window Memory:** Stateful multi-turn dialogue management with strict token window boundary controls (`MAX_TURNS`).
  - **Exception & Rate Limiting Strategy:** Fallback wrappers handling HTTP 429 and transient connection faults.

---

## 🛠️ Quickstart & Setup

### 1. Environment Configuration
Clone the repository and set up a Python virtual environment:
```bash
git clone https://github.com/harsha-medapati/Agentic_Ai.git
cd Agentic_Ai
python -m venv venv
# Linux/macOS:
source venv/bin/activate
# Windows:
.\venv\Scripts\activate
```

### 2. Environment Variables
Copy `.env.example` to `.env` and specify your Groq API credentials:
```bash
cp .env.example .env
```
Inside `.env`:
```env
GROQ_API_KEY=gsk_your_groq_api_key_here
```

### 3. Running Notebooks
Launch Jupyter Notebook or JupyterLab:
```bash
jupyter notebook
```
Navigate to [`DAY1/agenticAI_Day1.ipynb`](DAY1/agenticAI_Day1.ipynb) or [`DAY2/agenticAI_Day2.ipynb`](DAY2/agenticAI_Day2.ipynb) to run the interactive cells.
