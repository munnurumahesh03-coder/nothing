# 🤖 Agentic SQL Data Analyst | Enterprise BI Copilot

[![Python](https://img.shields.io/badge/Python-3.9+-blue.svg )](https://www.python.org/ )
[![Streamlit](https://img.shields.io/badge/Streamlit-Live-FF4B4B.svg )](https://streamlit.io/ )
[![LangChain](https://img.shields.io/badge/LangChain-Agentic-green.svg )](https://python.langchain.com/ )
[![Groq](https://img.shields.io/badge/Groq-Llama_3_120B-black.svg )](https://groq.com/ )
[![SQLite](https://img.shields.io/badge/SQLite-Database-003B57.svg )](https://www.sqlite.org/ )

### 🔴 [Live Application: Launch the Agentic SQL Analyst Here](https://agentic-sql-data-analyst-pzbxqtbysn4zjnbnbsuq5a.streamlit.app/ )

---

## 📖 The Problem
In most enterprises, business stakeholders rely on data engineering teams to write SQL queries and build dashboards. This creates a massive bottleneck. Traditional RAG (Retrieval-Augmented Generation) systems fail here because they can only search static text documents, not calculate real-time metrics from relational databases.

## 💡 The Solution
This project introduces an **Autonomous Agentic AI** that democratizes data access. By leveraging the **ReAct (Reasoning + Acting)** framework, the AI acts as a virtual Data Analyst. Users ask questions in plain English, and the Agent autonomously inspects the database schema, writes optimized SQL, executes it, and synthesizes the numerical results into a business-ready answer.

---

## ⚙️ System Architecture & ReAct Workflow

Unlike standard LLM chatbots, this system operates in a continuous feedback loop:
1. **Thought:** The Agent analyzes the user's natural language question.
2. **Action:** It queries the SQLite database to understand the schema and available tables.
3. **Observation:** It reads the schema and formulates a syntactically correct SQL query.
4. **Execution:** It runs the query. *If the SQL fails, the Agent reads the error and autonomously rewrites the query.*
5. **Synthesis:** It formats the raw database output into a clean, readable response.

## ✨ Key Capabilities & Example Queries
The Agent is capable of handling complex aggregations, filtering, and multi-condition logic. Try asking the live app:
* 🟢 *"What is the total sales revenue for each product category?"*
* 🟡 *"Who are our top 5 most valuable customers based on total sales?"*
* 🔴 *"Which sub-category of 'Office Supplies' has the lowest total sales in New York City?"*

---

## 🛠️ Tech Stack & Engineering Choices

| Component | Technology | Engineering Rationale |
|-----------|------------|-----------------------|
| **Frontend** | Streamlit | Rapid prototyping of a clean, stateful conversational UI. |
| **Orchestration** | LangChain | Provides the `create_sql_agent` toolkit for robust tool-calling. |
| **The Brain (LLM)** | Groq (120B) | Utilizes a massive open-source model with ultra-low latency inference. |
| **Data Layer** | SQLite3 | Lightweight, serverless relational database containing 9,800+ rows of Superstore Sales data. |

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
