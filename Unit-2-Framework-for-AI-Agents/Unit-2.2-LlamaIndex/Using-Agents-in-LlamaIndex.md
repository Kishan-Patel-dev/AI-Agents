# 🧠 Using Agents in LlamaIndex
<a href="./agents.ipynb" target="_blank">
  <img src="https://img.shields.io/badge/Notebook-Open%20.ipynb-blue?style=for-the-badge&logo=jupyter" alt="Notebook Button"/>
</a>

Agents in LlamaIndex combine reasoning, planning, and tool use to achieve user goals using LLMs. You can supercharge them with tools or functions for flexible, dynamic task execution.

>"An Agent is a system that leverages an AI model to interact with its environment to achieve a user-defined objective. It combines reasoning, planning, and action execution (often via external tools) to fulfil tasks."

## 🧩 Types of Agents
LlamaIndex supports three main **reasoning agent** types:
![Agents in LlamaIndex](../../assets/types%20of%20Agents.png)
- **Function Calling Agents**: Use function-calling LLMs to call tools via defined schemas.
- **ReAct Agents**: Handle complex reasoning using any LLM with chat/text capabilities.
- **Advanced Custom Agents**: Built with deeper customization for complex workflows.

> ℹ️ See [`BaseWorkflowAgent`](https://github.com/run-llama/llama_index/blob/main/llama-index-core/llama_index/core/agent/workflow/base_agent.py) for advanced usage.

---

## 🚀 Initializing an Agent
- Providing set of functions/tools that define its capabilities.
- Agent will automatically use the function calling API or standard ReAct agent loop.
- LLM to create tool calls based on provided schemas avoiding specific prompting.
- ReAct agents are also good at complex reasoning tasks and can work with any LLM that has chat or text completion capabilities.

### 1. Define a Tool
```python
# define sample Tool -- type annotations, function names, and docstrings, are all included in parsed schemas!
def multiply(a: int, b: int) -> int:
    """Multiplies two integers and returns the resulting integer"""
    return a * b
```

### 2. Set up the Agent
```python
from llama_index.llms.huggingface_api import HuggingFaceInferenceAPI
from llama_index.core.agent.workflow import AgentWorkflow
from llama_index.core.tools import FunctionTool

llm = HuggingFaceInferenceAPI(model_name="Qwen/Qwen2.5-Coder-32B-Instruct")
agent = AgentWorkflow.from_tools_or_functions([FunctionTool.from_defaults(multiply)], llm=llm)
```

---

## 🧠 Agent Memory (State)
Agents are **stateless by default**, but can maintain memory using a `Context`.

```python
from llama_index.core.workflow import Context

ctx = Context(agent)
await agent.run("My name is Bob.", ctx=ctx)
await agent.run("What was my name again?", ctx=ctx)
```

---

## 📚 Creating RAG Agents
- Agentic RAG is a powerful way to use agents to answer questions about your data.
![RAG Agent with `QueryEngineTools`](../../assets/agentic-rag.png)

Enable **Agentic RAG** by wrapping a `QueryEngine` as a tool:

```python
from llama_index.core.tools import QueryEngineTool

query_engine = index.as_query_engine(llm=llm, similarity_top_k=3)
query_engine_tool = QueryEngineTool.from_defaults(
    query_engine=query_engine,
    name="persona_db",
    description="Access persona descriptions",
    return_direct=False,
)

agent = AgentWorkflow.from_tools_or_functions(
    [query_engine_tool], 
    llm=llm,
    system_prompt="You are a helpful assistant that has access to a database containing persona descriptions. "
)
```

---

## 🤖 Multi-Agent Systems

Define specialized agents and orchestrate them with `AgentWorkflow`:

- **Agents in LlamaIndex can also directly be used as tools** for other agents, for more complex and custom scenarios.

```python
from llama_index.core.agent.workflow import (
    AgentWorkflow,
    FunctionAgent,
    ReActAgent,
)

# Define some tools
def add(a: int, b: int) -> int:
    """Add two numbers."""
    return a + b


def subtract(a: int, b: int) -> int:
    """Subtract two numbers."""
    return a - b


# Create agent configs
# NOTE: we can use FunctionAgent or ReActAgent here.
# FunctionAgent works for LLMs with a function calling API.
# ReActAgent works for any LLM.
calculator_agent = ReActAgent(
    name="calculator",
    description="Performs basic arithmetic operations",
    system_prompt="You are a calculator assistant. Use your tools for any math operation.",
    tools=[add, subtract],
    llm=llm,
)

query_agent = ReActAgent(
    name="info_lookup",
    description="Looks up information about XYZ",
    system_prompt="Use your tool to query a RAG system to answer information about XYZ",
    tools=[query_engine_tool],
    llm=llm
)

# Create and run the workflow
agent = AgentWorkflow(
    agents=[calculator_agent, query_agent], root_agent="calculator"
)

# Run the system
response = await agent.run(user_msg="Can you add 5 and 3?")
```

---

## 🔗 Learn More

Explore:
- [AgentWorkflow Introduction](https://docs.llamaindex.ai/en/stable/examples/agent/agent_workflow_basic/)
- [Agent Learning Guide](https://docs.llamaindex.ai/en/stable/understanding/agent/)
