# 🧠 Unit 2: Frameworks for AI Agents

This unit focuses on **agentic frameworks** - tools designed to help you build more flexible and powerful applications powered by LLMs.

![Agent Frameworks](../assets/thumbnail2.jpg)

## 📚 Course Progress

### ✅ Completed Units
- [x] [Unit 0: Welcome to the Course](../Unit-0-Welcome/README.md)
- [x] [Unit 1: Introduction to Agents](../Unit-1-Intro-to-Agents/README.md)
- [x] Unit 2.1: [smolagents](./Unit-2.1-Smolagents/README.md) – Hugging Face's lightweight agent framework
- [x] Unit 2.2: [LlamaIndex](./Unit-2.2-LlamaIndex/README.md) – Context-augmented agent system
- [x] Unit 2.3: [LangGraph](./Unit-2.3-LangGraph/README.md) – Stateful agent workflows

## 📌 Units Breakdown

### Unit 2.1: **smolagents**
- Introduction to smolagents
- Why use smolagents?
- Building Agents That Use Code
- Writing actions as code snippets or JSON blobs
- Tools and utilities
- Retrieval Agents
- Multi-Agent Systems
- Vision and Browser agents
- [Start Learning →](./Unit-2.1-Smolagents/README.md)

### Unit 2.2: **LlamaIndex**
- Introduction to LlamaIndex
- Introduction to LlamaHub
- Components in LlamaIndex
- Using Tools in LlamaIndex
- Using Agents in LlamaIndex
- Creating Agentic Workflows
- [Start Learning →](./Unit-2.2-LlamaIndex/README.md)

### Unit 2.3: **LangGraph**
- Introduction to LangGraph
- What is LangGraph?
- Building Blocks of LangGraph
- Building Your First LangGraph
- Document Analysis Graph
- [Start Learning →](./Unit-2.3-LangGraph/README.md)

## 🤖 When to Use an Agentic Framework

<details>
<summary>📋 Click to expand</summary>

Use an agentic framework when:

- You have **complex workflows** (e.g., multiple tool calls, dynamic decision-making)
- You need **abstractions** for better orchestration and modularity
- You **require features** like memory, retry logic, and error handling

Skip it when:
- You only need a simple prompt chain
- Predefined workflows are sufficient
- You want full control with less overhead
</details>

## 🔧 Key Components of Agentic Systems

<details>
<summary>🔍 Click to expand</summary>

To build robust agentic apps, you need:

- **LLM Engine** – The core brain that generates outputs
- **Tools List** – Functions or APIs the agent can use
- **Tool Call Parser** – Extracts actions from LLM output
- **System Prompt** – Coordinates agent behavior with the parser
- **Memory** – For retaining past interactions
- **Error Handling** – Retries, logs, and corrections for failed calls
</details>

## 🎯 Learning

- [x] Understand different agentic frameworks and their use cases
- [x] Build agents using smolagents for lightweight applications
- [x] Create context-aware systems with LlamaIndex
- [x] Implement stateful workflows with LangGraph
- [x] Choose the right framework for your specific needs
- [x] Combine multiple frameworks for complex applications

## 📚 Additional Resources

- [Hugging Face Agents Course](https://huggingface.co/learn/agents-course)
- [smolagents Documentation](https://github.com/smol-ai/smolagents)
- [LlamaIndex Documentation](https://docs.llamaindex.ai/)
- [LangGraph Documentation](https://python.langchain.com/docs/langgraph)
- [LangChain Academy](https://academy.langchain.com/)

## 📝 Notes

- Each framework has its unique strengths and use cases
- Start with smolagents for basic agent development
- Progress to LlamaIndex for context-aware applications
- Use LangGraph for complex, stateful workflows
- Practice with provided examples in each framework
- Consider combining frameworks for advanced applications
