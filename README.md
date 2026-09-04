# 🤖 Agentic SQL Data Analyst | Enterprise BI Copilot

[![Python](https://img.shields.io/badge/Python-3.9+-blue.svg )](https://www.python.org/ )
[![Streamlit](https://img.shields.io/badge/Streamlit-Live-FF4B4B.svg )](https://streamlit.io/ )
[![LangChain](https://img.shields.io/badge/LangChain-Agentic-green.svg )](https://python.langchain.com/ )
[![Groq](https://img.shields.io/badge/Groq-Llama_3_120B-black.svg )](https://groq.com/ )
[![SQLite](https://img.shields.io/badge/SQLite-Database-003B57.svg )](https://www.sqlite.org/ )

### 🔴 [Live Application: Launch the Agentic SQL Analyst Here](https://agentic-sql-data-analyst-pzbxqtbysn4zjnbnbsuq5a.streamlit.app/ )

---

## 📖 The Problem: Why Traditional RAG Fails
* **Data Bottlenecks:** Business stakeholders constantly rely on data engineering teams to write custom SQL queries, slowing down decision-making.
* **Tabular Data Hallucinations:** Standard RAG (Retrieval-Augmented Generation) systems are built for unstructured text (PDFs, docs) and hallucinate wildly when asked to perform math or aggregations on tabular data.
* **Static Dashboards:** Traditional BI tools (Tableau, PowerBI) are rigid and cannot answer ad-hoc, zero-shot questions outside of their pre-built visuals.

## 💡 The Solution: Agentic AI
* **Autonomous SQL Generation:** Acts as a virtual Data Analyst that translates natural language directly into optimized SQL.
* **Deterministic Execution:** Instead of guessing the answer, the Agent executes the SQL directly against the database, ensuring 100% factual, mathematically correct outputs.
* **Democratized Analytics:** Allows non-technical users to query complex relational databases using plain English.

---

## ⚙️ System Architecture & The ReAct Workflow
This system bypasses standard prompt-and-response mechanics by utilizing the **ReAct (Reasoning + Acting)** framework via LangChain's SQL Toolkit.

* **Step 1: Schema Discovery (Action):** The Agent autonomously triggers the `sql_db_schema` tool to read the table structures, column names, and data types.
* **Step 2: Query Formulation (Thought):** The LLM reasons about the user's question and writes a highly specific SQL query.
* **Step 3: Syntax Validation (Action):** The Agent passes the query through a `sql_db_query_checker` to ensure it is safe and valid before execution.
* **Step 4: Execution & Self-Correction (Observation):** The Agent runs the query. **Crucially, if the SQL throws an error, the Agent reads the traceback and autonomously rewrites the query until it succeeds.**
* **Step 5: Synthesis:** The raw numerical output is passed back to the LLM to be formatted into a clean, professional natural language response.

---

## ✨ Key Technical Capabilities
* **Zero-Shot Querying:** Handles unseen questions without requiring pre-defined SQL templates.
* **Complex Aggregations:** Flawlessly executes `GROUP BY`, `ORDER BY`, `LIMIT`, and multi-condition `WHERE` clauses.
* **Ultra-Low Latency:** Powered by Groq's LPU inference engine, allowing a massive 120B parameter model to reason and execute in milliseconds.
* **Stateful UI:** Built with Streamlit to maintain chat history and provide a seamless conversational experience.

---

## 🛠️ Tech Stack & Engineering Rationale
* **Frontend (Streamlit):** Chosen for rapid deployment of data-centric, interactive web applications.
* **Orchestration (LangChain):** Utilized for its robust `create_sql_agent` toolkit and seamless tool-binding capabilities.
* **The Brain (Groq / Llama-3 120B):** Selected over standard OpenAI models to demonstrate expertise in Open-Source LLMs and high-speed inference optimization.
* **Data Layer (SQLite3):** A lightweight, serverless relational database containing 9,800+ rows of Superstore Sales data, perfect for edge deployment.

---

## 💻 Local Installation & Setup

To run this project locally on your machine, run the following commands in your terminal:

```bash
# 1. Clone the repository
git clone https://github.com/yourusername/Agentic-SQL-Data-Analyst.git
cd Agentic-SQL-Data-Analyst

# 2. Install dependencies
pip install -r requirements.txt

# 3. Set your Groq API Key (Linux/Mac )
export GROQ_API_KEY="your_api_key_here"
# (If on Windows Command Prompt, use: set GROQ_API_KEY="your_api_key_here")

# 4. Launch the application
streamlit run app.py
