# 🛠️ Tools in `smolagents`

<a href="./tools.ipynb" target="_blank">
  <img src="https://img.shields.io/badge/Notebook-Open%20.ipynb-blue?style=for-the-badge&logo=jupyter" alt="Notebook Button"/>
</a>

In `smolagents`, tools are treated as **functions that an LLM can call within an agent system**.

---

## 🔧 What Is a Tool?

To interact with a tool, the LLM needs an **interface description** with these key components:

- **Name**: What the tool is called
- **Tool** description: What the tool does
- **Input types and descriptions**: What arguments the tool accepts
- **Output type**: What the tool returns

---

## 🎉 Example Use Case: Alfred's Party Planner

Alfred, planning a party at Wayne Manor, might use tools like:

- `web_search`: to find catering services.
- `image_generator`: to create promotional visuals.
- `superhero_party_theme_generator`: to suggest party themes.

![How a Tool Call is Managed](../../assets/Agent_ManimCE.gif)

---

## 🧰 Tool Creation Methods

### 1. The `@tool` Decorator

Simplifies tool definition via functions with docstrings and type hints.

Using this approach, we define a function with:
- **A clear and descriptive function name** that helps the LLM understand its purpose.
- **Type hints for both inputs and outputs** to ensure proper usage.
- **A detailed description**, including an Args: section where each argument is expilicitly described.  

```python
from smolagents import CodeAgent, HfApiModel, tool

# Let's pretend we have a function that fetches the highest-rated catering services.
@tool
def catering_service_tool(query: str) -> str:
    """
    This tool returns the highest-rated catering service in Gotham City.

    Args:
        query: A search term for finding catering services.
    """
    # Example list of catering services and their ratings
    services = {
        "Gotham Catering Co.": 4.9,
        "Wayne Manor Catering": 4.8,
        "Gotham City Events": 4.7,
    }

    # Find the highest rated catering service (simulating search query filtering)
    best_service = max(services, key=services.get)

    return best_service


agent = CodeAgent(tools=[catering_service_tool], model=HfApiModel())

# Run the agent to find the best catering service
result = agent.run(
    "Can you give me the name of the highest-rated catering service in Gotham City?"
)

print(result)   # Output: Gotham Catering Co.
    ...
```

### 2. Defining a Tool as a Python Class

Involves creating a subclass of Tools

Ideal for complex logic or metadata control.

The class wraps the function with metadata that helps the LLM understand how to use it effectively. In this class, we define:

- `name`: The tool’s name.
- `description`: A description used to populate the agent’s system prompt.
- `inputs`: A dictionary with keys `type` and `description`, providing information to help the Python interpreter process inputs.
- `output_type`: Specifies the expected output type.
- `forward`: The method containing the inference logic to execute.


```python
from smolagents import Tool, CodeAgent, HfApiModel

class SuperheroPartyThemeTool(Tool):
    name = "superhero_party_theme_generator"
    description = """
    This tool suggests creative superhero-themed party ideas based on a category.
    It returns a unique party theme idea."""

    inputs = {
        "category": {
            "type": "string",
            "description": "The type of superhero party (e.g., 'classic heroes', 'villain masquerade', 'futuristic Gotham').",
        }
    }

    output_type = "string"

    def forward(self, category: str):
        themes = {
            "classic heroes": "Justice League Gala: Guests come dressed as their favorite DC heroes with themed cocktails like 'The Kryptonite Punch'.",
            "villain masquerade": "Gotham Rogues' Ball: A mysterious masquerade where guests dress as classic Batman villains.",
            "futuristic Gotham": "Neo-Gotham Night: A cyberpunk-style party inspired by Batman Beyond, with neon decorations and futuristic gadgets."
        }

        return themes.get(category.lower(), "Themed party idea not found. Try 'classic heroes', 'villain masquerade', or 'futuristic Gotham'.")

# Instantiate the tool
party_theme_tool = SuperheroPartyThemeTool()
agent = CodeAgent(tools=[party_theme_tool], model=HfApiModel())

# Run the agent to generate a party theme idea
result = agent.run(
    "What would be a good superhero party idea for a 'villain masquerade' theme?"
)

print(result)  # Output: "Gotham Rogues' Ball: A mysterious masquerade where guests dress as classic Batman villains."
```

---

## 🧪 Built-in Toolbox

`smolagents` includes default tools:

- `PythonInterpreterTool`
- `FinalAnswerTool`
- `UserInputTool`
- `DuckDuckGoSearchTool`
- `GoogleSearchTool`
- `VisitWebpageTool`

---

## 📦 Sharing and Importing Tools

### Push to Hugging Face Hub

```python
tool.push_to_hub("username/tool_repo", token="YOUR_TOKEN")
```

### Load from Hub

```python
from smolagents import load_tool, CodeAgent, HfApiModel

image_generation_tool = load_tool(
    "m-ric/text-to-image",
    trust_remote_code=True
)

agent = CodeAgent(
    tools=[image_generation_tool],
    model=HfApiModel()
)

agent.run("Generate an image of a luxurious superhero-themed party at Wayne Manor with made-up superheros.")
```

### Import from HF Spaces

```python
from smolagents import CodeAgent, HfApiModel, Tool

image_generation_tool = Tool.from_space(
    "black-forest-labs/FLUX.1-schnell",
    name="image_generator",
    description="Generate an image from a prompt"
)

model = HfApiModel("Qwen/Qwen2.5-Coder-32B-Instruct")

agent = CodeAgent(tools=[image_generation_tool], model=model)

agent.run(
    "Improve this prompt, then generate an image of it.",
    additional_args={'user_prompt': 'A grand superhero-themed party at Wayne Manor, with Alfred overseeing a luxurious gala'}
)
```

### Import LangChain Tools

```python
from langchain.agents import load_tools
from smolagents import CodeAgent, HfApiModel, Tool

search_tool = Tool.from_langchain(load_tools(["serpapi"])[0])

agent = CodeAgent(tools=[search_tool], model=model)

agent.run("Search for luxury entertainment ideas for a superhero-themed event, such as live performances and interactive experiences.")
```
### Importing from any MCP Server
smolagents also allows importing tools from the hundreds of MCP servers available on [glama](https://glama.ai/mcp/servers).ai or [smithery.ai](https://smithery.ai/).

```python
#Install mcp client
pip install "smolagents[mcp]"
#MCP Servers Tools loaded in ToolCollection object
import os
from smolagents import ToolCollection, CodeAgent
from mcp import StdioServerParameters
from smolagents import HfApiModel


model = HfApiModel("Qwen/Qwen2.5-Coder-32B-Instruct")


server_parameters = StdioServerParameters(
    command="uvx",
    args=["--quiet", "pubmedmcp@0.1.3"],
    env={"UV_PYTHON": "3.12", **os.environ},
)

with ToolCollection.from_mcp(server_parameters, trust_remote_code=True) as tool_collection:
    agent = CodeAgent(tools=[*tool_collection.tools], model=model, add_base_tools=True)
    agent.run("Please find a remedy for hangover.")

```
---

## 🧠 Pro Tip

Use meaningful names, rich docstrings, and type hints to help LLMs understand and use your tools effectively!

---

## 🔗 Resources
- [Tools Tutorial](https://huggingface.co/docs/smolagents/tutorials/tools) - Explore this tutorial to learn how to work with tools effectively.
- [Tools Documentation](https://huggingface.co/docs/smolagents/v1.8.1/en/reference/tools) - Comprehensive reference documentation on tools.
- [Tools Guided Tour](https://huggingface.co/docs/smolagents/v1.8.1/en/guided_tour#tools) - A step-by-step guided tour to help you build and utilize tools efficiently.
- [Building Effective Agents](https://huggingface.co/docs/smolagents/tutorials/building_good_agents) - A detailed guide on best practices for developing reliable and high-performance custom function agents