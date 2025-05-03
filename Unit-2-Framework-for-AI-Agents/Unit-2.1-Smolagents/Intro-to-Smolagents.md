# 🤖 Unit 2.1 – Introduction to **smolagents**

**smolagents** is a lightweight, open-source agent framework developed by **Hugging Face**. It enables you to build capable and flexible AI agents using minimal abstraction while still supporting advanced use cases like web browsing, retrieval, and multimodal inputs.

## 🎯 Unit Objective:
Learn how to build intelligent agents with smolagents that can:
- Search for information
- Execute code
- Use tools and retrieve documents
- Interact with web pages
- Leverage vision capabilities
- Work together as multi-agent systems

---

## 📦 What You’ll Learn

### 1️⃣ **Why Use smolagents**
- Simple yet powerful alternative to other frameworks (e.g., LangGraph, LlamaIndex)
- Best suited when:
  - You want fine-grained control
  - You prefer code generation (e.g., Python) over JSON/function calls
- Learn about its pros/cons and when to choose it

### 2️⃣ **CodeAgents**
- These agents generate **Python code** directly
- Useful for software development, logic-heavy tasks
- Hands-on examples provided

### 3️⃣ **ToolCallingAgents**
- These agents output **structured JSON/text** instead of code
- Easier to interpret but less flexible than CodeAgents
- Ideal for integrating tools with predictable input/output

### 4️⃣ **Tools**
- Functions that agents can call
- Defined using `Tool` class or `@tool` decorator
- Includes:
  - Creating your own tools
  - Sharing tools with the community
  - Using default or community toolboxes

### 5️⃣ **Retrieval Agents**
- Enable **Retrieval-Augmented Generation (RAG)**
- Can access knowledge bases and web data
- Support memory and fallback strategies for robust querying

### 6️⃣ **Multi-Agent Systems**
- Combine multiple agents for complex workflows
- Examples include connecting a web search agent with a coding agent
- Focuses on orchestration, communication, and task delegation

### 7️⃣ **Vision and Browser Agents**
- Use **Vision-Language Models (VLMs)** for image understanding
- Build browser agents that:
  - View web pages
  - Interpret visual content
  - Extract structured data from websites

---

## 🔗 Resources
- [smolagents Documentation](https://huggingface.co/docs/smolagents)
- [Building Effective Agents](https://www.anthropic.com/research/building-effective-agents)
- [Agent Guidelines](https://huggingface.co/docs/smolagents/tutorials/building_good_agents)
- [LangGraph Agents](https://langchain-ai.github.io/langgraph/)
- [Function Calling Guide](https://platform.openai.com/docs/guides/function-calling)
- [RAG Best Practices](https://www.pinecone.io/learn/retrieval-augmented-generation/)

