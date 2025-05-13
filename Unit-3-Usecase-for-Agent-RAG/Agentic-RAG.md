# 🧠 Agentic Retrieval Augmented Generation (RAG)

In this unit, we explore how to empower Alfred, our AI agent, with **Agentic RAG** to effectively plan and manage the gala.

---

## 📚 What is RAG?

**Retrieval Augmented Generation (RAG)** enhances LLMs by:

* **Retrieving** relevant, up-to-date data from external sources
* **Augmenting** the prompt to the LLM with that retrieved context

> 🤖 LLMs are powerful, but they may lack **real-time** or **domain-specific** knowledge. RAG fills in these gaps.
> RAG solves this problem by finding and retrieving relevant information from your data and forwarding that to the LLM.
![RAG](../assets/rag%20(1).png)
---

## 🕴️ Why Agentic RAG?

While standard RAG retrieves data before answering, **Agentic RAG** allows agents like Alfred to:

* Use reasoning to choose *how* to answer
* Decide *which tools* to use (e.g., search, weather APIs, custom databases)
* Execute complex workflows dynamically

In other words, Alfred doesn’t just pull in documents—he **thinks**, chooses tools, and adapts based on the question.

![Agentic RAG](../assets/agentic-rag%20(1).png)
---

## 🛠️ Alfred’s Agentic RAG Capabilities

To prepare for the gala, Alfred needs to:

* 🔍 Retrieve guest info from structured data sources
* 🌤️ Get live weather updates
* 🗞️ Pull the latest news
* 📈 Fetch Hugging Face Hub model stats

All these require **tool-augmented** RAG functionality, powered by agents that can:

* Invoke tools based on intent
* Chain together actions
* Decide the optimal path to get accurate answers

---

## 🚀 Building the Agentic RAG Workflow

We will:

1. Build a custom RAG tool to retrieve invitee details
2. Add tools for:

   * Web search
   * Weather updates
   * Hugging Face model usage stats
3. Combine everything into a **multi-tool agent** capable of autonomous reasoning

> 💡 Agentic RAG = RAG + Tools + Agent Reasoning
> Alfred = Your AI party planner + smart researcher + decision-maker
