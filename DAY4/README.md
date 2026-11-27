# 🛠️ Day 4: Tool-Calling Agents, Structured Schemas, Persistent Sheets & Traces

Welcome to **Day 4** of the Agentic AI series. In this module, we transition from passive, tool-less pipelines into **autonomous tool-calling agents**. We implement LangChain's `@tool` decorator, enforce structured outputs via Pydantic schemas, connect persistent order logging via Google Sheets and CSV backends, and analyze execution traces.

---

## 🎯 Objectives & Overview

- **Domains:**
  - *Sweet Crumbs Home Bakery* (Core agent implementation, edge-case rejection, and docstring ablation experiments)
  - *BrightPage Stationery & Office Supplies* (Stationery tool creation, Pydantic order schema, and sheet persistence)
- **Model Used:** `openai/gpt-oss-20b` via Groq Cloud (`ChatGroq`, `temperature=0.2`).
- **Core Goals:** Understand how the model inspects function signatures and docstrings to select tools, enforce typed JSON extraction with `with_structured_output`, log confirmed records safely to sheets, and verify system integrity with regression suites.

---

## 🧠 Architecture & Engineering Concepts

### 1. The Tool Execution Loop & Docstring Binding
Unlike standard API prompts, an agent model does not execute code directly; it outputs structured tool call requests:
```
┌─────────────────────────────────┐
│     User Message / Request      │
└────────────────┬────────────────┘
                 │
                 ▼
┌─────────────────────────────────┐
│        Agent Model Loop         │
│  Inspects available @tool docs  │
└────────────────┬────────────────┘
                 │
         [Tool Call Needed?]
        /                  \
      YES                  NO
      /                      \
     ▼                        ▼
┌──────────────┐       ┌──────────────┐
│ Execute Tool │       │ Final Answer │
│ & Return Data│       │  to Customer │
└──────┬───────┘       └──────────────┘
       │
       ▼
[Feed ToolMessage back to Agent]
```

### 2. The "Break-It" Ablation Experiment: Why Docstrings Are API Docs
- When a function is decorated with `@tool`, LangChain converts its type hints and docstring into the model's tool definition schema.
- **Experiment:** Renaming `get_price` to `tool1` and stripping its docstring to `"does stuff"` caused tool selection to fail completely (0 tool calls generated on `"How much are 3 plum cakes?"`).
- **Takeaway:** Clear, descriptive function names and Google-style docstrings are mandatory for deterministic tool binding.

### 3. Pydantic Schemas & `with_structured_output`
- **Sweet Crumbs (`Order`):** Validates customer `name`, `item` (`Literal["plum cake", "chocolate cake", "cupcake"]`), `qty >= 1`, `date`, and `eggless` boolean.
- **BrightPage (`StationeryOrder`):** Validates customer `customer_name`, `item` (10-item retail `Literal`), `qty >= 1`, `delivery_type` (`"home_delivery"` | `"store_pickup"`), `delivery_date`, and optional `notes`.
- Tested against noisy, informal customer chat inputs (slang, missing punctuation, implied delivery) with 100% extraction accuracy into validated objects.

### 4. Reading Agent Traces with `pretty_print()`
Analyzing execution traces reveals the model's internal reasoning cycle:
- For multi-intent queries like *"Are you open on Sunday? How much are 2 chocolate cakes?"*:
  1. `AIMessage` calls `is_open(day="sunday")`
  2. `ToolMessage` returns `"Open 10 am to 6 pm on sunday."`
  3. `AIMessage` calls `get_price(item="chocolate cake", qty=2)`
  4. `ToolMessage` returns `"2 x chocolate cake = Rs 1300"`
  5. Final `AIMessage` synthesizes both tool outputs into a coherent answer.

### 5. Defensive Edge-Case Validation & Sheet Integrity
Adversarial and incomplete queries must be handled by the model without polluting persistent storage:
- **Unlisted item (`"donut"`):** Gracefully explains donuts are unavailable and lists existing items without booking.
- **Missing pickup date:** Asks the customer for their required date before proceeding.
- **Invalid quantity (`0 cupcakes`):** Enforces minimum order quantity policy without calling `log_order`.

---

## 🛠️ Step-by-Step Implementation Breakdown

1. **Section 1 · Setup & Sheets Backend:**
   - Configures `ChatGroq(model="openai/gpt-oss-20b", temperature=0.2)`.
   - Initializes `init_sheet()` using `gspread` in Google Colab or `LocalWorksheet` locally.
2. **Section 2 · Sweet Crumbs Bakery Agent:**
   - Defines `@tool` functions: `get_price`, `is_open`, and `log_order`.
   - Binds tools into `create_agent` with memory and system rules.
3. **Section 3 · Trace Analysis:**
   - Uses `pretty_print()` to inspect the exact message sequence and count model iterations.
4. **Section 4 · Customer Edge-Case Testing & Regression:**
   - Verifies that only valid, complete orders enter `sweet_crumbs_orders.csv`.
   - Evaluates all 13 rows in [`../DAY3/tests.csv`](../DAY3/tests.csv) (13/13 passed, 100%).
5. **Section 5 · BrightPage Stationery Agent:**
   - Builds `get_stationery_price` (with 10% bulk discount logic) and `check_store_and_delivery` tools.
   - Extracts `StationeryOrder` from noisy messages.
   - Implements `log_stationery_order` and logs confirmed orders to `brightpage_orders.csv`.
6. **Section 6 · Break-It & Restore-It Demonstration:**
   - Proves model dependence on docstring semantics.

---

## 📁 Files in This Directory

| File | Description |
| :--- | :--- |
| [`task.ipynb`](task.ipynb) | Complete Day 4 notebook with tool definitions, Pydantic schemas, sheet logging, and trace analysis. |
| [`sweet_crumbs_orders.csv`](sweet_crumbs_orders.csv) | Local CSV sheet mirror logging confirmed bakery orders. |
| [`brightpage_orders.csv`](brightpage_orders.csv) | Local CSV sheet mirror logging confirmed stationery orders. |
| [`README.md`](README.md) | Technical architecture guide, trace documentation, and run instructions (this file). |

---

## 🚀 How to Run

### Option A: Google Colab (Recommended)
1. Upload [`task.ipynb`](task.ipynb), [`sweet_crumbs_orders.csv`](sweet_crumbs_orders.csv), and [`brightpage_orders.csv`](brightpage_orders.csv) to Google Colab.
2. Under **Secrets (🔑)**, add your `GROQ_API_KEY` and enable notebook access.
3. Run all cells (`Ctrl + F9`). Authenticate with Google when prompted by `auth.authenticate_user()` to link Google Sheets.

### Option B: Local Setup
1. Ensure your virtual environment is active and dependencies are installed:
   ```bash
   pip install langchain langchain-groq gspread pydantic python-dotenv
   ```
2. Verify `GROQ_API_KEY` is set in your root `.env` file:
   ```env
   GROQ_API_KEY=gsk_your_groq_api_key_here
   ```
3. Launch Jupyter:
   ```bash
   jupyter notebook task.ipynb
   ```
