# 🛠️ ToolCallingAgent Overview

<a href="./tool_calling_agents.ipynb" target="_blank">
  <img src="https://img.shields.io/badge/Notebook-Open%20.ipynb-blue?style=for-the-badge&logo=jupyter" alt="Notebook Button"/>
</a>


**Tool Calling Agent** is a type of agent in the [smolagents](https://github.com/smol-ai/smol-agents) framework that uses **built-in tool-calling capabilities of LLM providers** to generate tools calls as  **JSON objects** instead of executable Python code as standard approach used by OpenAI, Anthropic and many more.

Unlike `CodeAgent`, which generates Python code and executes it, `ToolCallingAgent` generates **JSON blobs** containing tool names and arguments. These are parsed and executed by the system.

### ✅ Use Case
ToolCallingAgents are ideal for:
- Simple actions
- Stateless or low-complexity workflows
- Environments where code execution is limited or undesirable

## How do Tools Calling Agents Work?
 **Tool Calling Agents** follow the same multi-step workflow as Code Agents

### Key Difference 
- How they structure their Actions: 
    instead of executable code, they **generate JSON objects that specify tool names and arguments**. The system then **parses these instructions** to execute the appropriate tools.


### 📌 Example

```python
from smolagents import ToolCallingAgent, DuckDuckGoSearchTool, HfApiModel

agent = ToolCallingAgent(tools=[DuckDuckGoSearchTool()], model=HfApiModel())
agent.run("Search for the best music recommendations for a party at the Wayne's mansion.")
```

**Tool Parse:**
```json
[
  {
    "name": "web_search",
    "arguments": "best music recommendations for a party at Wayne's mansion"
  }
]
```
**Output**
```
╭─────────────────────────────────────────────────────────────────────────────────────────────────────────────────╮
│ Calling tool: 'web_search' with arguments: {'query': "best music recommendations for a party at Wayne's         │
│ mansion"}                                                                                                       │
╰─────────────────────────────────────────────────────────────────────────────────────────────────────────────────╯
```

### 🔍 Benefits
- Safer and more portable than code generation
- Compatible with OpenAI / Anthropic-style tool-calling APIs
- Easy to trace and audit tool usage

### 📄 Related
- [ToolCallingAgent Documentation](https://huggingface.co/docs/smolagents/v1.8.1/en/reference/agents#smolagents.ToolCallingAgent)

